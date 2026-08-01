# Corporate-Level Bash Scripting L3 — Complete Interview Questions and Answers


---

<!-- Original file: 01. Bash Fundamentals and Production Standards.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Fundamentals and Production Standards

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** Interpreter behavior, strict mode, quoting, exit status, execution model, and production readiness.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. What makes a Bash script production-ready rather than merely functional?

### Detailed answer

A production-ready script is deterministic, observable, secure, testable, maintainable, and safe to rerun. It validates prerequisites and input, uses predictable exit codes, records useful logs, handles signals and temporary files, avoids destructive defaults, supports dry-run or confirmation where appropriate, and documents operating assumptions.

It should also define ownership, deployment method, dependency versions, rollback, monitoring, and failure escalation. L3 engineers think about the script as an operational service component rather than a collection of shell commands.

### Example

```bash
#!/usr/bin/env bash
set -Eeuo pipefail
IFS=$'\n\t'

readonly SCRIPT_NAME=${0##*/}
readonly LOG_FILE=${LOG_FILE:-/var/log/example.log}

log() { printf '%s [%s] %s\n' "$(date -Is)" "$1" "$2" | tee -a "$LOG_FILE" >&2; }
die() { log ERROR "$1"; exit "${2:-1}"; }

main() {
  command -v curl >/dev/null 2>&1 || die "curl is required" 127
  log INFO "Starting"
}

main "$@"
```

### L3 follow-up discussion

Discuss idempotency, configuration externalization, least privilege, timeouts, retries, concurrency control, and how operations teams will diagnose failures at 02:00.

### Production caution

`set -e` alone does not make a script safe. Error behavior has exceptions, pipelines need `pipefail`, and cleanup still requires traps.

---

## Q2. Explain shebang selection: `/bin/bash` versus `/usr/bin/env bash`.

### Detailed answer

`#!/bin/bash` selects an exact interpreter path and is predictable on systems where Bash is installed there. `#!/usr/bin/env bash` searches `PATH`, which improves portability across platforms but can execute an unexpected Bash when `PATH` is controlled or polluted.

For controlled Linux servers, an absolute path is often preferred. For developer tools across varied environments, `env` may be appropriate. The decision should match the deployment and security model.

### Example

```bash
#!/bin/bash
printf 'Interpreter: %s\n' "$BASH"
printf 'Version: %s\n' "$BASH_VERSION"
```

### L3 follow-up discussion

The shebang matters only when the file is executed directly. `bash script.sh` explicitly invokes Bash, while `. script.sh` or `source script.sh` executes in the current shell and ignores the shebang.

### Production caution

Never assume a script invoked by `sh script.sh` will use Bash. On many systems, `/bin/sh` is another shell and Bash-specific syntax can fail.

---

## Q3. What do `set -e`, `set -u`, `set -o pipefail`, and `set -E` do?

### Detailed answer

`set -e` requests exit when an unhandled simple command fails, but its behavior has exceptions in conditionals, lists, loops, command substitutions, and functions. `set -u` treats expansion of unset variables as errors, with careful handling needed for optional variables and arrays. `pipefail` makes a pipeline fail when any component fails rather than only the last command. `set -E` propagates the `ERR` trap into functions, command substitutions, and subshell contexts where supported.

Together they improve failure visibility, but they do not replace explicit validation and error handling.

### Example

```bash
set -Eeuo pipefail

trap 'printf "ERROR line=%s command=%q status=%s\n"   "$LINENO" "$BASH_COMMAND" "$?" >&2' ERR

grep -q 'needle' input.txt | sort >output.txt
```

### L3 follow-up discussion

Explain why expected failures should appear inside explicit conditionals, for example `if grep -q ...; then`, so strict mode does not confuse normal control flow with an incident.

### Production caution

Blindly enabling `set -u` can break code that accesses empty positional parameters. Use `${1-}`, `${var:-default}`, or argument-count validation.

---

## Q4. How do exit statuses work in Bash?

### Detailed answer

Commands return an integer status from 0 to 255. Zero conventionally means success; nonzero means failure or a special condition. `$?` contains the most recent status, pipeline statuses are available in `PIPESTATUS`, and `exit` sets the script status.

A mature script defines meaningful exit codes, preserves failures across wrappers, and avoids overwriting `$?` before it is captured.

### Example

```bash
if ! output=$(some_command 2>&1); then
  rc=$?
  printf 'Command failed rc=%d output=%s\n' "$rc" "$output" >&2
  exit "$rc"
fi
```

### L3 follow-up discussion

The example above illustrates a subtlety: using `!` inverts the status, so `$?` inside the branch is zero. A safer pattern is `set +e; output=$(...); rc=$?; set -e`, or an `if output=$(...); then ... else rc=$?; ... fi` structure.

### Production caution

Do not reuse operating-system-reserved or command-specific exit codes carelessly. Document the script's contract for schedulers and monitoring.

---

## Q5. What is the difference between executing and sourcing a script?

### Detailed answer

Executing starts a new process or shell environment. Variable assignments, directory changes, functions, and shell options normally do not affect the parent shell. Sourcing with `source file` or `. file` runs the file in the current shell, so it can modify the caller's environment.

Libraries intended for sourcing should not call `exit`, change shell options globally without restoration, or execute `main` automatically.

### Example

```bash
# library.sh
is_sourced() [[ ${BASH_SOURCE[0]} != "$0" ]]

cleanup_library_state() { :; }

if ! is_sourced; then
  printf 'This file is intended to be sourced.\n' >&2
  exit 2
fi
```

### L3 follow-up discussion

Use `BASH_SOURCE` to locate library files reliably. Discuss namespace conventions for library functions and variables to prevent collisions.

### Production caution

Sourcing untrusted content is equivalent to executing arbitrary code in the current shell.

---

## Q6. Explain parsing order and why quoting is central to Bash correctness.

### Detailed answer

Bash reads syntax, performs expansions, word splitting, and pathname expansion, then executes commands. Unquoted parameter expansion can turn one value into several arguments and can expand wildcard characters. Quoting controls these transformations.

The default rule for production scripting is to quote expansions: `"$var"`, `"${array[@]}"`, and `"$(command)"`. Leave expansions unquoted only when intentional and reviewed.

### Example

```bash
file='Quarterly Report *.txt'
printf '<%s>\n' "$file"

# Safe:
rm -- "$file"

# Unsafe:
# rm $file
```

### L3 follow-up discussion

Explain that quotes are syntax and are removed before command execution. Storing quote characters inside a variable does not recreate shell quoting.

### Production caution

Unquoted values obtained from users, filenames, APIs, or configuration are a common source of data loss and command-injection-like behavior.

---

## Q7. What is the role of `IFS`?

### Detailed answer

The Internal Field Separator controls word splitting and parts of `read`. Its default contains space, tab, and newline. Changing it globally can alter unrelated expansions and loops. A common strict-mode setting is newline and tab, but even that does not make unquoted expansions safe.

Prefer local, command-specific use such as `IFS=, read -r ...` or `while IFS= read -r line`.

### Example

```bash
while IFS= read -r line || [[ -n $line ]]; do
  printf 'line=%q\n' "$line"
done < input.txt

IFS=, read -r name role region <<<"$record"
```

### L3 follow-up discussion

Discuss how `read -r` prevents backslash interpretation and why a final unterminated line requires the `|| [[ -n $line ]]` pattern.

### Production caution

Do not set `IFS` to newline globally and assume filenames are safe; filenames may contain newlines.

---

## Q8. How do Bash options and `shopt` differ?

### Detailed answer

`set -o` controls shell options such as `errexit`, `nounset`, and `pipefail`. `shopt` controls Bash-specific behavior such as `nullglob`, `globstar`, `failglob`, and `lastpipe`.

Options are process-local but can affect sourced callers. Libraries should record and restore any options they change.

### Example

```bash
old_nullglob=$(shopt -p nullglob)
shopt -s nullglob
files=(/var/log/*.log)
eval "$old_nullglob"

printf 'Matched %d files\n' "${#files[@]}"
```

### L3 follow-up discussion

Explain `nullglob` versus `failglob`: without either, an unmatched pattern remains literal; with `nullglob`, it disappears; with `failglob`, expansion fails.

### Production caution

Using `eval` to restore trusted output from `shopt -p` is acceptable in this narrow pattern; do not generalize it to untrusted text.

---

## Q9. How should a Bash script discover its own directory?

### Detailed answer

`$0` can be relative, can contain a path through a symlink, and changes meaning when sourced. `${BASH_SOURCE[0]}` identifies the current Bash source file. A common approach resolves its directory with `cd -- "$(dirname -- "${BASH_SOURCE[0]}")" && pwd -P`.

Full symlink resolution requires a deliberate loop and differs across platforms because `readlink -f` is not universal.

### Example

```bash
SCRIPT_DIR=$(
  cd -- "$(dirname -- "${BASH_SOURCE[0]}")" >/dev/null 2>&1
  pwd -P
) || exit 1

source "$SCRIPT_DIR/lib/common.sh"
```

### L3 follow-up discussion

Discuss whether the deployment wants the physical resolved directory or the symlink location, because configuration lookup may intentionally follow one or the other.

### Production caution

Do not use `cd "$(dirname "$0")"` without checking failure; a failed `cd` can make later relative paths operate in the wrong directory.

---

## Q10. How do you design a Bash script entry point?

### Detailed answer

Put behavior in functions and call a single `main "$@"` at the end. This separates initialization, parsing, validation, execution, and cleanup. It also lets the file be sourced for testing without running operational code.

Use `readonly` constants, local variables inside functions, and return statuses rather than implicit global state.

### Example

```bash
main() {
  parse_args "$@"
  validate_config
  acquire_lock
  run_operation
}

if [[ ${BASH_SOURCE[0]} == "$0" ]]; then
  main "$@"
fi
```

### L3 follow-up discussion

Explain dependency injection for testability: commands or paths can be supplied through variables, functions, or a controlled PATH in tests.

### Production caution

Do not put destructive commands at top level before validation and trap registration.

---

## Recommended references

- GNU Bash Reference Manual
- POSIX Shell Command Language
- ShellCheck documentation

---

<!-- Original file: 02. Variables Parameters Expansion and Quoting.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Variables, Parameters, Expansion, and Quoting

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** Parameter expansion, arguments, arithmetic, substitutions, patterns, configuration, and safe string handling.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. Explain parameter expansion operators such as `:-`, `:=`, `:?`, and `:+`.

### Detailed answer

`${var:-default}` uses a default when the variable is unset or null. `${var:=default}` also assigns that default. `${var:?message}` aborts the expansion with an error when unset or null. `${var:+alternate}` uses the alternate only when the variable is set and non-null. Omitting the colon tests only whether the variable is unset, not whether it is empty.

These operators make configuration contracts explicit.

### Example

```bash
API_URL=${API_URL:-https://api.example.invalid}
: "${TOKEN:?TOKEN must be set}"
CACHE_DIR=${CACHE_DIR:=/var/tmp/mytool}
verbose_flag=${VERBOSE:+--verbose}
```

### L3 follow-up discussion

Explain the no-colon variants: `${var-default}` accepts an empty string as set, while `${var:-default}` treats it as missing.

### Production caution

Do not place secrets inside parameter-expansion error messages if logs may be collected centrally.

---

## Q2. What is the difference between `$*` and `$@`?

### Detailed answer

Inside double quotes, `"$@"` expands each positional parameter as a separate argument and is almost always correct for forwarding. `"$*"` joins all positional parameters into one word using the first character of IFS.

Unquoted `$@` and `$*` undergo splitting and globbing and should rarely appear.

### Example

```bash
wrapper() {
  printf 'argc=%d\n' "$#"
  real_command "$@"
}

wrapper "one value" "*.txt"
```

### L3 follow-up discussion

Discuss zero-argument behavior: `"$@"` expands to no arguments, while `"$*"` expands to one empty argument when quoted in some contexts.

### Production caution

Using `command $@` can corrupt filenames and turn wildcard characters into unrelated files.

---

## Q3. Explain substring removal and replacement in parameter expansion.

### Detailed answer

`${var#pattern}` removes the shortest prefix match; `${var##pattern}` removes the longest. `%` and `%%` do the same for suffixes. `${var/pat/repl}` replaces the first match, while `${var//pat/repl}` replaces all matches.

Patterns use shell glob syntax, not regular expressions.

### Example

```bash
path=/var/log/app/server.log
name=${path##*/}
dir=${path%/*}
stem=${name%.log}
safe=${name// /_}

printf '%s %s %s %s\n' "$name" "$dir" "$stem" "$safe"
```

### L3 follow-up discussion

Explain why external `basename`, `dirname`, `sed`, or `tr` may be unnecessary for simple transformations, reducing process creation in hot loops.

### Production caution

Pattern characters in user-provided values can change matching semantics. Escape or avoid pattern-based replacement when literal behavior is required.

---

## Q4. How do indirect expansion and namerefs work?

### Detailed answer

`${!name}` expands the variable whose name is stored in `name`. `declare -n ref=target` creates a nameref that reads or writes the referenced variable. Namerefs are useful for generic functions returning arrays or modifying caller variables.

They are Bash-specific and require careful validation of variable names.

### Example

```bash
set_value() {
  local -n out_ref=$1
  out_ref=$2
}

result=
set_value result "complete"
printf '%s\n' "$result"
```

### L3 follow-up discussion

Namerefs can reference arrays and associative arrays, which makes library APIs cleaner than `eval` or global variables.

### Production caution

Never allow an untrusted string to become an unrestricted nameref target. Validate with a variable-name pattern and limit allowed destinations.

---

## Q5. Explain arithmetic expansion and arithmetic contexts.

### Detailed answer

`$((expression))`, `(( expression ))`, `let`, and arithmetic `for` loops evaluate integer arithmetic. Variable names can appear without `$` in arithmetic contexts. Bash integers are fixed-width signed values determined by the platform, and overflow is not checked as a business error.

`(( expression ))` returns success when the result is nonzero and failure when zero, which can interact with `set -e`.

### Example

```bash
count=0
(( ++count ))       # returns success because result is 1
total=$((count * 1024))

if (( total > 1000 )); then
  printf 'large\n'
fi
```

### L3 follow-up discussion

Explain why `((count++))` can return status 1 when the old value is zero, unexpectedly triggering errexit. Prefix increment avoids that first-zero result.

### Production caution

Do not use Bash integer arithmetic for money, very large values, or decimal calculations without an appropriate external tool and validation.

---

## Q6. How do command substitution and process substitution differ?

### Detailed answer

Command substitution `$(cmd)` captures standard output, removes trailing newlines, and supplies text to an expansion. Process substitution `<(cmd)` or `>(cmd)` provides a file-like path connected to a process, allowing streaming without storing all output.

Process substitution is Bash-specific and may use `/dev/fd` or named pipes.

### Example

```bash
mapfile -t lines < <(generate_lines)

diff -u <(sort old.txt) <(sort new.txt)

output=$(command_producing_text)
```

### L3 follow-up discussion

Command substitution executes in a subshell environment, so variable changes inside usually do not affect the parent. Process substitution is also asynchronous and must be considered during error handling.

### Production caution

Do not store binary data in shell variables; command substitution cannot preserve NUL bytes.

---

## Q7. What is brace expansion and when can it be dangerous?

### Detailed answer

Brace expansion is a textual expansion performed before parameter expansion, for example `{1..5}` or `{dev,test,prod}`. Variables inside braces are not dynamically evaluated as range endpoints in the usual way.

It is convenient for static command generation but can create huge argument lists or unintended filenames.

### Example

```bash
printf '%s\n' server-{01..05}
mkdir -p -- /srv/app/{bin,conf,log,tmp}
```

### L3 follow-up discussion

For dynamic ranges, use arithmetic loops rather than `eval` around brace syntax.

### Production caution

Avoid brace expansions with very large ranges because the shell materializes every word before execution.

---

## Q8. How do glob patterns differ from regular expressions?

### Detailed answer

Globs match pathnames or strings with shell pattern syntax: `*`, `?`, and bracket expressions. Bash regular expressions are used by `[[ string =~ regex ]]` and follow extended regular expression rules. Quoting the right-hand side of `=~` changes regex interpretation.

Use globs for filename and simple shell matching; use regex for structured validation.

### Example

```bash
file=report-2026.csv
if [[ $file == report-*.csv ]]; then
  printf 'glob matched\n'
fi

if [[ $file =~ ^report-([0-9]{4})[.]csv$ ]]; then
  printf 'year=%s\n' "${BASH_REMATCH[1]}"
fi
```

### L3 follow-up discussion

Discuss `extglob`, which adds richer glob operators such as `@(pattern)` and `!(pattern)` when enabled through `shopt -s extglob`.

### Production caution

Never parse complex formats with an unreadable regex when a dedicated parser is safer.

---

## Q9. How should environment variables and configuration precedence be designed?

### Detailed answer

Define a clear precedence such as command-line option, environment variable, configuration file, then default. Validate after all sources are merged. Keep secrets separate from general configuration and avoid exporting variables unnecessarily.

A script should print effective non-secret configuration in debug mode for reproducibility.

### Example

```bash
timeout=${TIMEOUT:-30}
config_file=${CONFIG_FILE:-/etc/mytool.conf}

# Later CLI parsing may override:
# --timeout 10

[[ $timeout =~ ^[1-9][0-9]*$ ]] || {
  printf 'Invalid timeout: %q\n' "$timeout" >&2
  exit 2
}
```

### L3 follow-up discussion

Distinguish shell variables from exported environment variables. Child processes only receive exported values.

### Production caution

Do not source arbitrary configuration files unless they are trusted executable shell code. Prefer a data format with a safe parser for untrusted configuration.

---

## Q10. How do you safely represent arbitrary strings in Bash?

### Detailed answer

Bash variables can hold bytes except NUL. Use arrays to preserve argument boundaries. Use `printf '%q'` for a shell-escaped diagnostic representation, not as a general data serialization format. For records containing arbitrary filenames, use NUL-delimited streams between tools that support them.

Never rely on newline-delimited filenames for fully general filesystem processing.

### Example

```bash
printf 'value=%q\n' "$value"

find /data -type f -print0 |
while IFS= read -r -d '' file; do
  printf 'processing %q\n' "$file"
done
```

### L3 follow-up discussion

Discuss why JSON, CSV, and YAML need real parsers. Shell quoting is not a substitute for data-format escaping.

### Production caution

Bash cannot store NUL bytes, so it is unsuitable for arbitrary binary payload manipulation.

---

## Recommended references

- GNU Bash Reference Manual: Shell Parameter Expansion
- ShellCheck quoting rules

---

<!-- Original file: 03. Control Flow Functions and Error Handling.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Control Flow, Functions, and Error Handling

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** Conditions, loops, functions, scope, retries, timeouts, and CLI parsing.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. When should `[[ ... ]]`, `[ ... ]`, and `test` be used?

### Detailed answer

`[[ ... ]]` is Bash syntax with safer string handling, pattern matching, regex support, and no word splitting or pathname expansion on ordinary operands. `[ ... ]` and `test` are command forms with POSIX portability but require more careful quoting and operator rules.

For Bash-only production scripts, `[[ ... ]]` is usually preferred. Use `[ ... ]` when writing portable `/bin/sh` code.

### Example

```bash
if [[ -f $file && $name == *.log ]]; then
  printf 'regular log file\n'
fi

if [ -f "$file" ] && [ "$name" = expected ]; then
  :
fi
```

### L3 follow-up discussion

Explain that `<` and `>` inside `[[ ]]` are string comparisons, while numeric comparisons should use arithmetic contexts or `-lt`, `-gt` with test.

### Production caution

Do not write `[ "$a" == "$b" ]` in strictly portable shell; use `=`.

---

## Q2. How do `&&`, `||`, `if`, and command lists interact with errexit?

### Detailed answer

`cmd1 && cmd2` runs the second command only if the first succeeds. `cmd1 || cmd2` runs the second only if the first fails. Commands used as conditional tests are contexts where `set -e` is generally suppressed.

For important operational logic, an explicit `if` is clearer and handles status capture safely.

### Example

```bash
if backup_database; then
  log INFO "Backup completed"
else
  rc=$?
  log ERROR "Backup failed rc=$rc"
  exit "$rc"
fi
```

### L3 follow-up discussion

Avoid `cmd && success || failure` when `success` itself can fail; the failure branch may run even though the original command succeeded.

### Production caution

Compact boolean chains can hide which step failed and are poor for incident diagnostics.

---

## Q3. How do loops safely process lines and files?

### Detailed answer

Use `while IFS= read -r line` for text lines and NUL-delimited input for arbitrary filenames. Avoid `for x in $(command)` because command substitution removes trailing newlines and word-splits/globs the output.

Redirect into the loop or use process substitution depending on whether variable changes must remain in the current shell.

### Example

```bash
count=0
while IFS= read -r -d '' file; do
  (( ++count ))
  process_file "$file"
done < <(find /data -type f -print0)

printf 'count=%d\n' "$count"
```

### L3 follow-up discussion

A pipeline into `while` often runs the loop in a subshell, so assignments may be lost. Process substitution avoids that in Bash.

### Production caution

Do not parse `ls` output. Filenames can contain spaces, tabs, quotes, and newlines.

---

## Q4. How should functions return data?

### Detailed answer

A Bash function's `return` value is only an exit status from 0 to 255. Return structured or string data through stdout, a nameref output parameter, a global with a strict namespace, or a file/stream. Keep diagnostic logs on stderr so command substitution captures only data.

Choose one contract and document it.

### Example

```bash
lookup_user() {
  local user=$1
  local -n out_uid=$2
  out_uid=$(id -u -- "$user") || return
}

uid=
lookup_user root uid || exit
printf 'uid=%s\n' "$uid"
```

### L3 follow-up discussion

For large output, stream it rather than command-substituting it into memory. Namerefs are Bash-specific but preserve arrays.

### Production caution

Do not `echo` both logs and data from the same function if callers use `result=$(function)`.

---

## Q5. Explain variable scope and `local`.

### Detailed answer

Variables are global by default. `local` creates dynamically scoped function-local variables in Bash: called functions can see a caller's local variable unless they declare their own. This differs from lexical scope in many languages.

Use `local` consistently, initialize variables, and avoid generic global names. `local var=$(cmd)` can mask the command's failure because the declaration command may return success.

### Example

```bash
get_value() {
  local value
  if ! value=$(some_command); then
    return 1
  fi
  printf '%s\n' "$value"
}
```

### L3 follow-up discussion

Discuss `declare`, `readonly`, integer attributes, arrays, and how attributes on local variables can improve contracts.

### Production caution

Avoid `local value=$(cmd)` when the command's status matters; split declaration and assignment.

---

## Q6. How do recursion and call stacks work in Bash?

### Detailed answer

Bash functions may call themselves, but recursion is limited by process stack, shell implementation, and operational readability. `FUNCNAME`, `BASH_SOURCE`, and `BASH_LINENO` expose call-stack information.

Iterative approaches are usually safer for directory traversal or retry logic.

### Example

```bash
stack_trace() {
  local i
  for ((i=0; i<${#FUNCNAME[@]}-1; i++)); do
    printf '%s:%s %s()\n'       "${BASH_SOURCE[i+1]}" "${BASH_LINENO[i]}" "${FUNCNAME[i+1]}" >&2
  done
}
```

### L3 follow-up discussion

Use stack traces in an ERR trap or diagnostic mode, but keep logs readable and avoid exposing secrets in arguments.

### Production caution

Recursive filesystem logic can follow cycles through symlinks unless explicitly controlled.

---

## Q7. How do `break`, `continue`, and labeled loop counts work?

### Detailed answer

`break [n]` exits one or more nested loops. `continue [n]` advances one or more nested loops. They are useful but can obscure control flow when heavily nested.

Refactoring inner work into functions often makes error handling and testing clearer.

### Example

```bash
for host in "${hosts[@]}"; do
  for port in "${ports[@]}"; do
    if check "$host" "$port"; then
      printf 'Found service on %s:%s\n' "$host" "$port"
      break 2
    fi
  done
done
```

### L3 follow-up discussion

Explain when returning from a helper function is clearer than `break 3` from deeply nested loops.

### Production caution

Avoid swallowing unexpected failures with `continue`; log and classify skipped items.

---

## Q8. How should retry logic be implemented?

### Detailed answer

Retry only transient, idempotent operations. Use bounded attempts, timeout per attempt, exponential backoff with jitter, a maximum total duration, and clear logging. Do not retry authentication failures, validation errors, or destructive operations without an idempotency key.

Respect remote service rate limits and Retry-After guidance.

### Example

```bash
retry() {
  local max=$1 delay=$2
  shift 2
  local attempt rc
  for ((attempt=1; attempt<=max; attempt++)); do
    if "$@"; then
      return 0
    else
      rc=$?
    fi
    (( attempt == max )) && return "$rc"
    sleep "$delay"
    delay=$((delay * 2))
  done
}
```

### L3 follow-up discussion

Add jitter and a total deadline in distributed systems to avoid synchronized retry storms.

### Production caution

Never wrap every command in retries. Repeating a partial destructive operation can make recovery worse.

---

## Q9. How should timeouts be applied?

### Detailed answer

External commands can hang because of network, filesystem, locks, DNS, or child processes. Apply native timeouts when available, such as curl connect/max time, SSH ConnectTimeout/ServerAlive settings, database client timeouts, and `timeout` as an outer guard.

A timeout must produce a distinct error and cleanup child processes and temporary state.

### Example

```bash
if ! timeout --signal=TERM --kill-after=5s 30s     curl --fail --silent --show-error          --connect-timeout 5 --max-time 25          "$url" -o "$tmp"; then
  rc=$?
  printf 'Download failed or timed out rc=%d\n' "$rc" >&2
  exit "$rc"
fi
```

### L3 follow-up discussion

GNU `timeout` status 124 commonly indicates timeout; 137 may indicate forced KILL. Native application timeouts usually provide cleaner cleanup.

### Production caution

A timeout around a parent command may leave grandchildren unless process groups and signal behavior are understood.

---

## Q10. How do you structure argument parsing?

### Detailed answer

Use `getopts` for portable short options or a carefully implemented loop for GNU-style long options. Define usage, required values, defaults, mutual exclusions, repeated options, `--` handling, and meaningful exit status 2 for usage errors.

Validate syntax separately from executing operations.

### Example

```bash
usage() { printf 'Usage: %s [-n] -f FILE [--] [args...]\n' "${0##*/}"; }

dry_run=false file=
while getopts ':nf:h' opt; do
  case $opt in
    n) dry_run=true ;;
    f) file=$OPTARG ;;
    h) usage; exit 0 ;;
    :) printf 'Option -%s requires a value\n' "$OPTARG" >&2; exit 2 ;;
    \?) printf 'Unknown option -%s\n' "$OPTARG" >&2; exit 2 ;;
  esac
done
shift "$((OPTIND-1))"
[[ -n $file ]] || { usage >&2; exit 2; }
```

### L3 follow-up discussion

For a large interface, consider a language with a mature CLI parser or generate completion/docs from a single schema.

### Production caution

Do not execute commands while still parsing options; an invalid later option could leave partial changes.

---

## Recommended references

- GNU Bash Reference Manual: Conditional Constructs
- ShellCheck wiki

---

<!-- Original file: 04. Arrays Data Structures and Data Processing.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Arrays, Data Structures, and Data Processing

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** Indexed and associative arrays, command construction, structured formats, streams, and file descriptors.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. Explain indexed arrays and correct expansion.

### Detailed answer

Indexed arrays map nonnegative integer indices to strings. `"${array[@]}"` expands each element as a separate word; `"${array[*]}"` joins elements using the first IFS character. `${#array[@]}` gives element count, and `${!array[@]}` gives indices.

Arrays are the correct way to build command arguments dynamically.

### Example

```bash
args=(--fail --silent)
[[ $verbose == true ]] && args+=(--verbose)
args+=(--output "$output" "$url")

curl "${args[@]}"
```

### L3 follow-up discussion

Explain sparse arrays: element count is not necessarily the highest index plus one. Iterate indices when index identity matters.

### Production caution

Do not build a command as one string and execute it unquoted. Use an array to preserve argument boundaries.

---

## Q2. Explain associative arrays.

### Detailed answer

Associative arrays map string keys to string values and are declared with `declare -A`. They are useful for configuration lookup, counters, deduplication, and joins, but iteration order should not be assumed.

Keys and values still cannot contain NUL bytes.

### Example

```bash
declare -A status=(
  [api]=healthy
  [database]=degraded
)

status[cache]=healthy
for service in "${!status[@]}"; do
  printf '%s=%s\n' "$service" "${status[$service]}"
done
```

### L3 follow-up discussion

For deterministic output, copy keys to an indexed array and sort them using an external tool or known ordering.

### Production caution

Do not use unvalidated values as associative-array expressions in old or unusual Bash environments without understanding parsing and security implications.

---

## Q3. How can a command be constructed safely?

### Detailed answer

Represent the executable and every argument as separate array elements. This prevents word splitting and globbing and eliminates the usual need for `eval`.

Logging can use `printf '%q '` over the array to show a shell-readable diagnostic form.

### Example

```bash
cmd=(rsync -a --delete-delay)
[[ $dry_run == true ]] && cmd+=(--dry-run)
cmd+=(-- "$source/" "$destination/")

printf 'Executing: '
printf '%q ' "${cmd[@]}"
printf '\n'

"${cmd[@]}"
```

### L3 follow-up discussion

Discuss separating the displayed command from secret-bearing arguments. Redact tokens, passwords, or signed URLs.

### Production caution

Never use `eval "$command_string"` for values containing user or external data.

---

## Q4. How do `readarray` and `mapfile` work?

### Detailed answer

`mapfile` and `readarray` read lines into an indexed array without a loop. `-t` strips trailing delimiters, `-d` changes delimiter, `-n` limits count, and `-O` sets the starting index.

They are efficient for moderate input but store the entire result in memory.

### Example

```bash
mapfile -t services < <(systemctl list-unit-files --type=service --no-legend |
  awk '{print $1}')

printf 'Found %d services\n' "${#services[@]}"
```

### L3 follow-up discussion

For huge or unbounded input, stream through a `while read` loop instead of loading all records.

### Production caution

Command substitution or line-based mapfile cannot represent NUL-containing data inside variables; use NUL only as the read delimiter for filenames.

---

## Q5. How do you parse CSV, JSON, YAML, and XML in Bash?

### Detailed answer

Bash should orchestrate dedicated parsers rather than implement complex data-format parsing. CSV quoting rules, JSON escaping/nesting, YAML types/anchors, and XML namespaces make ad hoc `cut`, `awk`, or regex parsing unsafe.

Use tools such as `jq`, `yq` with a pinned implementation/version, Python, or application-specific clients. Validate required fields and types after parsing.

### Example

```bash
api_url=$(jq -er '.api.url | strings' config.json) || {
  printf 'Missing or invalid .api.url\n' >&2
  exit 2
}

mapfile -t nodes < <(jq -er '.nodes[] | strings' config.json)
```

### L3 follow-up discussion

Discuss parser exit codes, duplicate keys, numeric precision, null versus missing, and how secrets appear in debug logs.

### Production caution

Do not parse JSON with grep/sed or YAML by splitting on colon in production automation.

---

## Q6. How do you process NUL-delimited data?

### Detailed answer

NUL is the only byte forbidden in Unix pathnames, so NUL-delimited streams can represent arbitrary filenames. Use producers such as `find -print0`, consumers such as `xargs -0`, or `read -d ''`.

Keep each filename quoted after reading.

### Example

```bash
find "$root" -type f -name '*.log' -print0 |
while IFS= read -r -d '' file; do
  gzip -- "$file"
done
```

### L3 follow-up discussion

If loop variables must remain after the loop, redirect or use process substitution instead of a pipeline subshell.

### Production caution

Never convert a NUL-delimited stream to newline-delimited text before processing arbitrary filenames.

---

## Q7. What are here-documents and here-strings?

### Detailed answer

A here-document feeds multiline text to standard input. Quoting the delimiter, as in `<<'EOF'`, disables parameter, command, and arithmetic expansion inside. `<<-` strips leading tabs. A here-string `<<<"$value"` supplies a string plus a newline.

Here-documents are useful for templates and remote commands, but the expansion model must be explicit.

### Example

```bash
cat <<'CONFIG' >"$tmp"
literal=$HOME
command=$(date)
CONFIG

ssh "$host" 'bash -s' <<'REMOTE'
set -euo pipefail
hostname
df -h /
REMOTE
```

### L3 follow-up discussion

For generated configuration containing untrusted values, use a real template/serialization library or careful `printf`, not direct interpolation into executable text.

### Production caution

Unquoted remote here-documents may expand local variables before SSH, causing secrets or wrong values to be sent.

---

## Q8. How do file descriptors improve script design?

### Detailed answer

File descriptors allow separate logging, data, prompts, locks, and protocol channels. `exec 3>file` opens a descriptor for the shell lifetime; `command >&3` writes to it. Dynamic descriptors can be allocated with `exec {fd}>file`.

This avoids repeatedly opening files and helps keep stdout reserved for machine-readable data.

### Example

```bash
exec {log_fd}>>"$LOG_FILE"
printf '%s started\n' "$(date -Is)" >&"$log_fd"

# Preserve original stdout:
exec {original_stdout}>&1
some_command >result.txt
printf 'Done\n' >&"$original_stdout"
```

### L3 follow-up discussion

Discuss closing descriptors with `exec {fd}>&-`, inheritance into child processes, and `CLOEXEC` limitations in shell.

### Production caution

Leaked descriptors can keep pipes or files open and prevent readers from seeing EOF.

---

## Q9. How can Bash model records safely?

### Detailed answer

For simple internal records, use parallel arrays, associative arrays with compound keys, delimiter-separated values with a delimiter guaranteed absent, or one function invocation per record. For complex or persistent records, use JSON or another structured format with a parser.

Do not create dynamic variable names with `eval` when associative arrays or namerefs suffice.

### Example

```bash
declare -A host_role host_region
host_role[web01]=frontend
host_region[web01]=in
host_role[db01]=database
host_region[db01]=in

printf '%s %s\n' "${host_role[db01]}" "${host_region[db01]}"
```

### L3 follow-up discussion

Explain data ownership and validation at boundaries. Shell variables do not have schemas or native nested structures.

### Production caution

When the data model becomes nested or heavily transformed, Bash is no longer the best implementation language.

---

## Q10. How do you deduplicate values in Bash?

### Detailed answer

For moderate in-memory values, use an associative array as a set. For large streams, `sort -u` may be more memory-efficient and deterministic, depending on locale and input size.

Define whether comparison is case-sensitive and byte-based or locale-aware.

### Example

```bash
declare -A seen=()
unique=()
for item in "${items[@]}"; do
  [[ -v 'seen[$item]' ]] && continue
  seen[$item]=1
  unique+=("$item")
done
```

### L3 follow-up discussion

Set `LC_ALL=C` when bytewise deterministic sort order is required and document that choice.

### Production caution

Do not use `echo "$list" | tr ' ' '\n'` for arbitrary values; spaces may be part of an element.

---

## Recommended references

- GNU Bash Reference Manual: Arrays
- jq manual
- POSIX filename rules

---

<!-- Original file: 05. Files Processes Pipelines and Concurrency.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Files, Processes, Pipelines, and Concurrency

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** Temporary files, atomic updates, traversal, descriptors, pipelines, jobs, process control, and permissions.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. How should temporary files and directories be created?

### Detailed answer

Use `mktemp` with a restrictive umask, register cleanup immediately, and place temporary data on an appropriate filesystem. A private temporary directory is safer than many predictable files.

Do not construct names from PID alone because of races and symlink attacks.

### Example

```bash
umask 077
tmp_dir=$(mktemp -d "${TMPDIR:-/tmp}/mytool.XXXXXXXX") || exit 1
cleanup() { rm -rf -- "$tmp_dir"; }
trap cleanup EXIT

tmp_file=$tmp_dir/result
```

### L3 follow-up discussion

For sensitive data, consider whether the temporary filesystem is encrypted, persistent across crashes, or included in backups. Cleanup on EXIT cannot run after SIGKILL or power failure.

### Production caution

Never `rm -rf "$tmp_dir"` unless the variable was validated and created successfully. Use a readonly variable and defensive prefix checks for high-risk cleanup.

---

## Q2. How do you make file replacement atomic?

### Detailed answer

Write the complete new content to a temporary file on the same filesystem, set ownership and permissions, optionally fsync through an appropriate tool or language, then rename it over the target. Rename is atomic within one filesystem.

Readers see either old or new content, not a partially written file.

### Example

```bash
tmp=$(mktemp --tmpdir="$(dirname -- "$target")" ".${target##*/}.XXXXXX")
generate_config >"$tmp"
chmod --reference="$target" "$tmp" 2>/dev/null || chmod 0640 "$tmp"
mv -f -- "$tmp" "$target"
```

### L3 follow-up discussion

For durability across power loss, filesystem and directory fsync semantics matter; Bash alone does not provide a portable fsync primitive.

### Production caution

A temporary file in `/tmp` followed by `mv` to another filesystem becomes copy-and-delete and is not atomic.

---

## Q3. How do you safely traverse directories?

### Detailed answer

Use `find` with explicit roots, predicates, and NUL-delimited output. Avoid parsing `ls`. Decide whether to cross filesystems, follow symlinks, process hidden paths, and handle permission errors.

Use `--` before user-provided path arguments where supported and reject empty or dangerous roots.

### Example

```bash
root=/srv/app
[[ -d $root && $root != / ]] || exit 2

while IFS= read -r -d '' file; do
  process "$file"
done < <(find "$root" -xdev -type f -print0)
```

### L3 follow-up discussion

Discuss TOCTOU races: a path can change between validation and use. For high-security operations, Bash path checks may be insufficient compared with descriptor-based APIs in another language.

### Production caution

Never run recursive deletion against a variable that can be empty, `/`, or unexpectedly expanded.

---

## Q4. How do redirections order left to right?

### Detailed answer

Redirections are processed from left to right. `cmd >file 2>&1` sends stdout to the file and then duplicates stderr to the current stdout. `cmd 2>&1 >file` sends stderr to the original stdout, then redirects only stdout to the file.

Understanding descriptor duplication is critical for logging and pipelines.

### Example

```bash
command >combined.log 2>&1
command 2>&1 >stdout.log   # stderr still goes to original stdout
```

### L3 follow-up discussion

Explain Bash shorthand `&>file`, and why explicit POSIX-style redirections may be clearer in portable code.

### Production caution

Incorrect ordering can silently omit errors from logs or send secrets to an interactive terminal.

---

## Q5. How do pipelines execute and how is status captured?

### Detailed answer

Each pipeline component usually runs in its own process environment. The pipeline's status is the last command unless `pipefail` is enabled. `PIPESTATUS` contains each component's status immediately after the pipeline.

A downstream command such as `tee` can otherwise hide an upstream failure.

### Example

```bash
set -o pipefail
producer | transform | tee output.log
rcs=("${PIPESTATUS[@]}")
printf 'producer=%s transform=%s tee=%s\n' "${rcs[@]}" >&2
```

### L3 follow-up discussion

Capture `PIPESTATUS` immediately because any following command overwrites it. Decide whether SIGPIPE from an early-consuming command is expected.

### Production caution

`grep ... | head -1` can cause grep to receive SIGPIPE. With pipefail, that may appear as failure even when the first match was obtained.

---

## Q6. How do background jobs, `wait`, and `$!` work?

### Detailed answer

Appending `&` starts an asynchronous job; `$!` contains its PID. `wait PID` waits and returns that job's status. Modern Bash can use `wait -n` to wait for any child and `wait -p` to capture identity where supported.

A production script must limit concurrency, collect every status, propagate cancellation, and avoid orphaned children.

### Example

```bash
pids=()
for host in "${hosts[@]}"; do
  check_host "$host" &
  pids+=("$!")
done

rc=0
for pid in "${pids[@]}"; do
  if ! wait "$pid"; then
    rc=1
  fi
done
exit "$rc"
```

### L3 follow-up discussion

Discuss mapping PIDs back to work items and handling PID reuse only within the direct-child lifetime.

### Production caution

Starting thousands of jobs without a pool can exhaust PIDs, file descriptors, memory, sockets, or remote capacity.

---

## Q7. What is a subshell and when is it useful?

### Detailed answer

Parentheses `( commands )` run commands in a subshell environment. Directory changes, variables, traps, and options do not normally affect the parent. Braces `{ commands; }` group commands in the current shell.

Subshells are useful for isolated directory changes and scoped redirection.

### Example

```bash
(
  cd -- "$work_dir" || exit
  generate_artifacts
) >"$log_file" 2>&1
```

### L3 follow-up discussion

Command substitution also uses a subshell environment. `BASH_SUBSHELL` helps diagnostics but implementation details should not become business logic.

### Production caution

A subshell does not provide a security sandbox; it only isolates shell process state.

---

## Q8. How should process discovery and termination be implemented?

### Detailed answer

Prefer tracking PIDs of processes the script starts. When discovering external processes, use service managers, pidfiles with validation, cgroups, or exact process metadata. Name-only `pkill` can target unrelated processes.

Terminate gracefully with TERM, wait for a bounded period, then use KILL only if policy permits. Validate process identity before signaling.

### Example

```bash
terminate_pid() {
  local pid=$1
  [[ $pid =~ ^[1-9][0-9]*$ ]] || return 2
  kill -TERM "$pid" 2>/dev/null || return 0
  for _ in {1..10}; do
    kill -0 "$pid" 2>/dev/null || return 0
    sleep 1
  done
  kill -KILL "$pid"
}
```

### L3 follow-up discussion

`kill -0` checks signal permission/existence but does not prove the PID is still the original process after reuse. For long-lived pidfiles, validate start time or cgroup identity.

### Production caution

Never use `kill -9` as the first response; it prevents application cleanup and can corrupt state.

---

## Q9. How do FIFOs and coprocesses work?

### Detailed answer

A FIFO is a named pipe that allows unrelated processes to exchange byte streams. Bash `coproc` starts a command asynchronously with connected file descriptors. They can support streaming protocols, but require careful closure, buffering, blocking, and lifecycle management.

For complex IPC, a language with structured concurrency is usually safer.

### Example

```bash
coproc WORKER { while IFS= read -r line; do printf '%s\n' "${line^^}"; done; }

printf 'hello\n' >&"${WORKER[1]}"
IFS= read -r reply <&"${WORKER[0]}"
printf 'reply=%s\n' "$reply"

exec {WORKER[1]}>&-
wait "$WORKER_PID"
```

### L3 follow-up discussion

Explain deadlocks when both sides wait, and the need to close unused descriptors so EOF can propagate.

### Production caution

Named pipes with predictable paths can be replaced or opened by attackers unless created in a protected directory.

---

## Q10. How do you handle filesystem permissions and ownership safely?

### Detailed answer

Set a deliberate umask, create files with minimum permissions, and apply ownership only when required. Avoid parsing `ls -l`; use `stat`, `find`, or shell tests. Preserve metadata intentionally during replacement or deployment.

Running as root does not make ownership assumptions safe, especially on NFS, containers, or user namespaces.

### Example

```bash
umask 027
install -o root -g app -m 0640 -- "$source" "$destination"
stat -c 'mode=%a owner=%U group=%G' -- "$destination"
```

### L3 follow-up discussion

Discuss ACLs, SELinux labels, capabilities, and how `cp` versus `install` versus `rsync` preserve different metadata.

### Production caution

A script that `chmod -R 777` to fix access has failed its security design.

---

## Recommended references

- GNU Bash Reference Manual: Redirections and Pipelines
- GNU coreutils manuals
- Filesystem Hierarchy and POSIX process concepts

---

<!-- Original file: 06. Secure Bash Scripting and Privilege Boundaries.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Secure Scripting and Privilege Boundaries

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** Injection, eval, secrets, PATH, privilege, races, validation, SSH, option safety, and environment hardening.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. What is command injection in Bash and how is it prevented?

### Detailed answer

Command injection occurs when untrusted text is reinterpreted as shell syntax. Common causes are `eval`, `bash -c` with concatenated data, unquoted expansion, building commands as strings, and unsafe remote command construction.

Prevent it by keeping code and data separate: use arrays for local commands, positional arguments for `bash -c`, validated inputs, dedicated APIs, and strongly quoted SSH commands.

### Example

```bash
user_value='$(touch /tmp/pwned); *.txt'

# Safe local command:
args=(printf '%s\n' "$user_value")
"${args[@]}"

# Safe bash -c argument passing:
bash -c 'printf "%s\n" "$1"' _ "$user_value"
```

### L3 follow-up discussion

Explain that quoting data before concatenating it into an `eval` string is fragile. The design should avoid reinterpretation rather than attempt to sanitize every metacharacter.

### Production caution

Never pass an API response, filename, environment variable, or user input into `eval` or an unquoted `bash -c` string.

---

## Q2. Why is `eval` dangerous, and are there legitimate uses?

### Detailed answer

`eval` parses its arguments as new shell code after another round of expansion. This makes quoting extremely difficult and turns data into executable syntax. Most uses can be replaced by arrays, associative arrays, namerefs, `printf -v`, or explicit case statements.

Narrow legitimate uses include executing trusted output generated by Bash itself, such as restoring `shopt -p` state, but the trust boundary must be explicit.

### Example

```bash
# Prefer:
printf -v "$validated_variable_name" '%s' "$value"

# Narrow trusted case:
saved=$(shopt -p nullglob)
shopt -s nullglob
# ... work ...
eval "$saved"
```

### L3 follow-up discussion

A variable name used by `printf -v` or a nameref still requires validation and an allowlist to prevent modifying sensitive shell variables.

### Production caution

A comment saying “input is safe” is not a control. Show how it is generated, validated, and constrained.

---

## Q3. How do you handle secrets in Bash scripts?

### Detailed answer

Do not hard-code secrets, pass them on command lines when avoidable, print them in logs, store them in world-readable files, or export them broadly. Retrieve secrets from an approved manager, use short-lived credentials, restrictive file descriptors or temporary files, and redact diagnostics.

Remember that environment variables can be visible to child processes and sometimes process-inspection tools; command-line arguments are often visible in process listings.

### Example

```bash
umask 077
secret_file=$(mktemp)
trap 'rm -f -- "$secret_file"' EXIT

secret_manager read --field=password app/db >"$secret_file"
database_client --password-file="$secret_file"
```

### L3 follow-up discussion

Discuss secret rotation, overlapping credentials, revocation, crash artifacts, shell history, `set -x`, core dumps, CI logs, and support bundles.

### Production caution

Never enable `set -x` around secret retrieval or commands containing tokens. Use `set +x` before the sensitive section and restore only afterward.

---

## Q4. What are PATH attacks and how are they prevented?

### Detailed answer

If a privileged script relies on an uncontrolled PATH, an attacker may place a malicious executable earlier in the search order. Empty PATH elements and writable directories are especially dangerous.

Set a known PATH, use absolute paths for high-risk commands, validate dependencies, and avoid executing from the current directory implicitly.

### Example

```bash
readonly PATH='/usr/sbin:/usr/bin:/sbin:/bin'
export PATH

for cmd in awk sed grep; do
  command -v "$cmd" >/dev/null 2>&1 || exit 127
done
```

### L3 follow-up discussion

`command -v` proves what will execute at that moment, but files can change later. Package integrity and filesystem permissions are part of the control.

### Production caution

Do not include `.` in the PATH of privileged automation.

---

## Q5. How should privileged Bash scripts drop or obtain privilege?

### Detailed answer

Use least privilege and explicit privilege boundaries. Prefer systemd units, sudo rules for specific commands, capabilities, or service accounts rather than running an entire large script as root. Validate the real and effective user IDs and environment.

When invoking sudo, pass a controlled environment and explicit command arguments. Do not trust caller-provided PATH, HOME, BASH_ENV, CDPATH, or shell functions.

### Example

```bash
if (( EUID != 0 )); then
  printf 'This operation requires root privileges.\n' >&2
  exit 77
fi

readonly PATH=/usr/sbin:/usr/bin:/sbin:/bin
unset CDPATH BASH_ENV ENV
```

### L3 follow-up discussion

Split discovery from mutation: an unprivileged phase can collect/validate input, then a minimal privileged helper performs a narrowly defined change.

### Production caution

Do not run a user-editable script through sudo or source user-controlled configuration as root.

---

## Q6. What is a symlink race and how can Bash reduce the risk?

### Detailed answer

A script may validate a pathname and then use it after an attacker replaces it with a symlink or another object. Predictable temporary files and operations in shared writable directories are common targets.

Use private directories created with `mktemp -d`, restrictive permissions, atomic creation options such as noclobber where applicable, and avoid following untrusted paths. For high-assurance descriptor-relative operations, use a language with `openat`-style APIs.

### Example

```bash
umask 077
tmpdir=$(mktemp -d "${TMPDIR:-/tmp}/secure.XXXXXXXX") || exit 1
trap 'rm -rf -- "$tmpdir"' EXIT

printf '%s\n' "$data" >"$tmpdir/payload"
```

### L3 follow-up discussion

Explain why testing `[[ ! -e $file ]]` and then creating it is still a race. Atomic creation must combine the check and creation.

### Production caution

Bash cannot fully eliminate every TOCTOU issue when working through pathnames in attacker-controlled directories.

---

## Q7. How do you validate input without creating false confidence?

### Detailed answer

Validation should be allowlist-based and tied to the destination context. A username, hostname, integer, path, SQL value, and URL each require different rules. Validation does not remove the need for correct quoting or structured APIs.

Normalize only when semantics are well understood, and reject ambiguous input early with a usage error.

### Example

```bash
validate_port() {
  local value=$1
  [[ $value =~ ^[0-9]+$ ]] || return 1
  (( value >= 1 && value <= 65535 ))
}

validate_name() {
  [[ $1 =~ ^[a-zA-Z_][a-zA-Z0-9_.-]{0,63}$ ]]
}
```

### L3 follow-up discussion

Mention Unicode, locale, newline, leading dash, path traversal, encoded delimiters, and integer overflow as context-dependent concerns.

### Production caution

A regex that allows “safe characters” does not make it safe to concatenate the value into shell, SQL, JSON, or YAML syntax.

---

## Q8. How do you safely use SSH in scripts?

### Detailed answer

Use noninteractive authentication, host-key verification, bounded connection timeouts, explicit user/host, and controlled remote command construction. Prefer copying a script and invoking it with positional parameters or streaming a quoted script to `bash -s --`.

Treat local and remote expansion as separate parsing layers.

### Example

```bash
ssh_opts=(
  -o BatchMode=yes
  -o ConnectTimeout=10
  -o ServerAliveInterval=15
  -o ServerAliveCountMax=2
  -o StrictHostKeyChecking=yes
)

ssh "${ssh_opts[@]}" "$user@$host"   bash -s -- "$remote_path" <<'REMOTE'
set -Eeuo pipefail
path=$1
printf 'Remote path: %q\n' "$path"
REMOTE
```

### L3 follow-up discussion

Discuss known_hosts lifecycle, bastions, ControlMaster security, parallel connection limits, exit-status propagation, and how to collect stderr per host.

### Production caution

Never disable host-key checking globally in production automation.

---

## Q9. How do you avoid option injection from filenames?

### Detailed answer

Many commands interpret arguments beginning with `-` as options. Use `--` to mark the end of options, prefix relative paths with `./`, or use APIs/options that read NUL-delimited path lists.

Quote the filename as well; `--` does not replace quoting.

### Example

```bash
rm -- "$file"
grep -- "$pattern" "$file"
cp -- "$source" "$destination"

find . -maxdepth 1 -type f -name '-*' -print0
```

### L3 follow-up discussion

Not every command supports `--`; learn the target utility's contract or use an absolute/prefixed pathname.

### Production caution

A filename such as `--preserve-root=no` can become dangerous when passed to a command without an option terminator.

---

## Q10. What security risks come from exported functions and startup files?

### Detailed answer

Bash can import exported functions in some environments, and noninteractive shells may process files such as BASH_ENV. Privileged automation must sanitize environment variables and avoid inheriting arbitrary functions or startup code.

Shellshock demonstrated how parser behavior around imported functions can become code execution; patched Bash versions and environment control are both important.

### Example

```bash
unset BASH_ENV ENV CDPATH
export -n SHELLOPTS BASHOPTS 2>/dev/null || true

# For a tightly controlled child:
env -i PATH=/usr/bin:/bin HOME=/root   /bin/bash --noprofile --norc /opt/tool/run.sh
```

### L3 follow-up discussion

Discuss environment allowlisting in sudo, systemd, cron, CI runners, and container entrypoints.

### Production caution

Do not assume a noninteractive shell has no startup influence; BASH_ENV is specifically relevant.

---

## Recommended references

- ShellCheck security guidance
- sudoers manual
- OpenSSH client manual
- OWASP command injection guidance

---

<!-- Original file: 07. Logging Traps Observability and Operational Diagnostics.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Logging, Traps, Observability, and Diagnostics

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** Structured logs, journaling, tracing, traps, cleanup, metrics, audit, and failure reporting.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. How do you design structured logging in Bash?

### Detailed answer

Define consistent timestamp, severity, script, PID, operation, and message fields. Send diagnostics to stderr and keep stdout available for command output or machine-readable data. For centralized systems, emit JSON with a real encoder or use logger/systemd journal fields.

Include correlation IDs and target identifiers so parallel work can be reconstructed.

### Example

```bash
log() {
  local level=$1 message=$2
  printf '%s level=%s script=%q pid=%d message=%q\n'     "$(date -Is)" "$level" "${0##*/}" "$$" "$message" >&2
}

log INFO "backup started"
```

### L3 follow-up discussion

`%q` is useful for diagnostic shell escaping but is not JSON. Use `jq -n` or another encoder for JSON logs.

### Production caution

Never include passwords, tokens, private keys, full authorization headers, or sensitive payloads in logs.

---

## Q2. How do syslog and the systemd journal integrate with scripts?

### Detailed answer

`logger` sends messages to syslog or the journal through the system logger. Under systemd, stdout/stderr of a service can be captured automatically. `systemd-cat` can attach priority and identifier.

Prefer the platform's logging path so rotation, retention, forwarding, and access control are centralized.

### Example

```bash
logger --tag my-backup --priority user.info -- "Backup started for $dataset"

printf 'Backup failed\n' |
  systemd-cat --identifier=my-backup --priority=err
```

### L3 follow-up discussion

Discuss rate limiting, multiline logs, facility selection, journal fields, and how unit metadata can provide context automatically.

### Production caution

Do not log through both tee and the journal without considering duplicate ingestion and alert noise.

---

## Q3. How do you implement debug tracing safely?

### Detailed answer

`set -x` prints expanded commands and can expose secrets. Configure `PS4` with source, line, function, PID, and direct trace output to a protected descriptor using `BASH_XTRACEFD`.

Enable tracing only for the required scope and redact or disable it around sensitive commands.

### Example

```bash
exec {trace_fd}>>"${TRACE_FILE:-/var/log/tool.trace}"
chmod 0600 "${TRACE_FILE:-/var/log/tool.trace}" 2>/dev/null || true
export BASH_XTRACEFD=$trace_fd
export PS4='+ ${BASH_SOURCE}:${LINENO}:${FUNCNAME[0]} pid=$BASHPID: '

[[ ${DEBUG:-false} == true ]] && set -x
```

### L3 follow-up discussion

Explain that xtrace shows expanded values and command substitutions, so a protected file is still sensitive operational data.

### Production caution

Never enable global xtrace in CI where secret-masking behavior is uncertain.

---

## Q4. How should ERR, EXIT, INT, and TERM traps be designed?

### Detailed answer

`EXIT` handles cleanup regardless of normal or error exit. `ERR` records failures but has context exceptions and should not replace explicit handling. `INT` and `TERM` should request cancellation, propagate to children, and exit with a meaningful status.

Trap functions should be small, avoid recursive failure, preserve the original exit status, and be registered before resources are acquired.

### Example

```bash
cleanup() {
  local rc=$?
  trap - EXIT
  [[ -n ${tmp_dir:-} ]] && rm -rf -- "$tmp_dir"
  exit "$rc"
}

on_error() {
  local rc=$?
  printf 'ERROR rc=%d line=%d cmd=%q\n' "$rc" "$LINENO" "$BASH_COMMAND" >&2
}

trap on_error ERR
trap cleanup EXIT
trap 'exit 130' INT
trap 'exit 143' TERM
```

### L3 follow-up discussion

Discuss trap inheritance, `set -E`, command substitutions, subshells, and child process groups.

### Production caution

Do not perform complex network calls from a signal trap; traps execute between shell commands and can interrupt state unexpectedly.

---

## Q5. How do you preserve the original failure during cleanup?

### Detailed answer

Capture `$?` immediately at the start of the EXIT trap, perform best-effort cleanup without allowing its failures to replace the original status, then exit with the captured value.

Cleanup should be idempotent and safe when initialization only partially completed.

### Example

```bash
cleanup() {
  local rc=$?
  set +e
  [[ -n ${lock_fd:-} ]] && flock -u "$lock_fd"
  [[ -n ${tmp_dir:-} && -d $tmp_dir ]] && rm -rf -- "$tmp_dir"
  exit "$rc"
}
trap cleanup EXIT
```

### L3 follow-up discussion

Use flags such as `created_tmp=false` or initialized empty variables to distinguish resources that were actually acquired.

### Production caution

A failed `rm` or unlock should be logged, but it should not convert the original application failure into an unrelated cleanup exit code.

---

## Q6. How do you expose metrics from a Bash script?

### Detailed answer

For batch jobs, emit duration, success/failure, processed count, and last-success timestamp to logs, a monitoring API, or Prometheus node_exporter textfile collector. Write metric files atomically and use stable low-cardinality labels.

A heartbeat without success state can falsely report a broken job as alive.

### Example

```bash
metrics_dir=/var/lib/node_exporter/textfile_collector
tmp=$(mktemp "$metrics_dir/job.prom.XXXXXX")
{
  printf 'backup_last_run_timestamp_seconds %(%s)T\n' -1
  printf 'backup_last_run_success %d\n' "$success"
  printf 'backup_items_processed %d\n' "$count"
} >"$tmp"
mv -f -- "$tmp" "$metrics_dir/backup.prom"
```

### L3 follow-up discussion

Distinguish counters, gauges, timestamps, and duration. Include stale-file handling when the script stops running entirely.

### Production caution

Do not include hostname, filename, user ID, or request ID as unbounded metric labels.

---

## Q7. How do you create an audit trail for destructive operations?

### Detailed answer

Record who or what requested the action, target, approved change/ticket, dry-run result, timestamp, command version, before-state, action result, and validation. Store logs in a location the executing account cannot casually erase when compliance requires it.

Use immutable infrastructure or centralized audit where possible.

### Example

```bash
log AUDIT "ticket=$ticket action=delete target=$target requester=${SUDO_USER:-$USER}"
capture_before_state "$target"
perform_delete "$target"
validate_deleted "$target"
```

### L3 follow-up discussion

Command lines alone may not show the effective configuration or expanded target set. Record a normalized plan and checksums.

### Production caution

Do not print secret-bearing environment or configuration into audit records.

---

## Q8. How should scripts report partial success?

### Detailed answer

Batch scripts should distinguish total success, partial success, retryable failure, and fatal precondition failure. Record per-item status and produce a summary. Decide whether partial success returns nonzero based on the caller's contract.

A restart should skip or safely redo completed idempotent items.

### Example

```bash
failed=()
for item in "${items[@]}"; do
  if process "$item"; then
    log INFO "item=$item status=success"
  else
    failed+=("$item")
    log ERROR "item=$item status=failed"
  fi
done

((${#failed[@]} == 0)) || exit 4
```

### L3 follow-up discussion

Schedulers, monitoring, and humans need a documented exit-code contract. A separate result manifest may be better than squeezing all states into one integer.

### Production caution

Do not return zero merely because the loop completed when some business operations failed.

---

## Q9. How do correlation IDs help parallel automation?

### Detailed answer

A correlation ID links logs, remote calls, retries, temporary files, metrics, and support records for one execution. Generate or accept one, validate its format, and propagate it in headers and remote environment only where trusted.

Per-target child IDs can extend the root ID.

### Example

```bash
run_id=${RUN_ID:-$(date +%Y%m%dT%H%M%S)-$$}
[[ $run_id =~ ^[A-Za-z0-9._-]+$ ]] || exit 2

log INFO "run_id=$run_id operation=start"
curl -H "X-Correlation-ID: $run_id" "$url"
```

### L3 follow-up discussion

Use IDs that are unique enough for the environment but do not encode secrets or personal data.

### Production caution

Do not use a predictable run ID as an authentication token or security boundary.

---

## Q10. What should a failure report contain?

### Detailed answer

Include concise impact, failed phase, exit status, target, timestamp, recent change/config version, log location, retry state, cleanup state, and recommended next action. Avoid dumping huge logs into alerts.

For automated escalation, attach a stable error code that maps to a runbook.

### Example

```bash
die() {
  local code=$1 error_id=$2 message=$3
  log ERROR "error_id=$error_id code=$code message=$message"
  printf 'FAILED: %s. See %s. Error ID: %s\n'     "$message" "$LOG_FILE" "$error_id" >&2
  exit "$code"
}
```

### L3 follow-up discussion

Separate user-facing messages from detailed technical logs. Include enough context for L1/L2 to act without exposing credentials.

### Production caution

An alert saying only “script failed” creates avoidable mean time to recovery.

---

## Recommended references

- GNU Bash Reference Manual: Signals and Traps
- systemd journal documentation
- Prometheus textfile collector guidance

---

<!-- Original file: 08. Networking APIs SSH and Remote Automation.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Networking, APIs, SSH, and Remote Automation

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** curl, REST APIs, DNS/network failures, SSH fan-out, downloads, pagination, proxy, TLS, and idempotency.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. How should `curl` be used in production scripts?

### Detailed answer

Use failure-aware options, explicit timeouts, bounded retries only for safe transient failures, TLS verification, response-body handling, and status-code validation. Separate headers, body, and diagnostics and write downloads atomically.

`curl -f` treats HTTP 4xx/5xx as failure but may hide the error body unless captured deliberately. Modern curl options vary by version, so validate deployment capability.

### Example

```bash
tmp=$(mktemp)
http_code=$(
  curl --silent --show-error        --connect-timeout 5 --max-time 30        --retry 3 --retry-delay 1        --output "$tmp"        --write-out '%{http_code}'        "$url"
) || exit

[[ $http_code == 200 ]] || {
  printf 'HTTP status %s body=%s\n' "$http_code" "$(head -c 500 "$tmp")" >&2
  exit 1
}
mv -- "$tmp" "$output"
```

### L3 follow-up discussion

Discuss idempotent methods, Retry-After, proxy/no_proxy, CA bundles, mTLS, DNS, IPv4/IPv6, rate limits, and redaction of authorization headers.

### Production caution

Do not use `-k/--insecure` in production to bypass certificate validation.

---

## Q2. How do you call a JSON REST API safely?

### Detailed answer

Construct JSON with a real encoder, send an explicit content type, validate HTTP status, parse the response with `jq -e`, and distinguish transport, protocol, authentication, validation, and business errors.

Never interpolate raw values into JSON text.

### Example

```bash
payload=$(jq -n   --arg name "$name"   --argjson replicas "$replicas"   '{name:$name, replicas:$replicas}')

response=$(mktemp)
code=$(curl -sS -o "$response" -w '%{http_code}'   -H 'Content-Type: application/json'   -H "Authorization: Bearer $token"   --data-binary "$payload" "$api_url")

[[ $code == 201 ]] || exit 1
id=$(jq -er '.id | strings' "$response")
```

### L3 follow-up discussion

Discuss pagination, idempotency keys, optimistic concurrency/ETags, token expiry, schema versioning, and API rate limiting.

### Production caution

Do not put bearer tokens in URL query strings or debug logs.

---

## Q3. How should DNS and network failures be classified?

### Detailed answer

Differentiate name resolution, connection timeout, connection refused, TLS failure, HTTP failure, and application-level error. They have different retry and escalation behavior.

Use tool exit status and timing rather than parsing localized human messages when possible.

### Example

```bash
if ! output=$(curl -sS --connect-timeout 5 --max-time 20 "$url" 2>&1); then
  rc=$?
  case $rc in
    6) reason=dns ;;
    7) reason=connect ;;
    28) reason=timeout ;;
    35|60) reason=tls ;;
    *) reason=transport ;;
  esac
  printf 'reason=%s rc=%d detail=%s\n' "$reason" "$rc" "$output" >&2
fi
```

### L3 follow-up discussion

Exit-code meanings are tool-specific and version-sensitive. Wrap them in one library and test against deployed versions.

### Production caution

Do not retry certificate validation or authentication failures as if they were transient network loss.

---

## Q4. How do you automate SSH across many hosts?

### Detailed answer

Use a bounded worker pool, per-host logs/status, strict host verification, connection timeouts, and controlled concurrency. Preserve each remote exit status and produce a summary. Consider orchestration tools such as Ansible when inventory, idempotency, privilege, and reporting become substantial.

Avoid shared output races by using one result file per host in a private directory.

### Example

```bash
run_host() {
  local host=$1
  ssh "${ssh_opts[@]}" "$host" 'sudo /usr/local/sbin/health-check'
}

# A real implementation should use a bounded pool, not unbounded '&'.
```

### L3 follow-up discussion

Discuss fan-out limits, bastion saturation, SSH connection multiplexing, partial failure, retry policy, and host-key rotation.

### Production caution

A loop that reports only the final SSH status can hide dozens of failed hosts.

---

## Q5. How do you safely download and execute an artifact?

### Detailed answer

Prefer not to pipe network content directly into a shell. Download to a protected temporary file, verify TLS, cryptographic checksum or signature from a separately trusted channel, inspect metadata, then execute a pinned interpreter.

Record the artifact version and hash.

### Example

```bash
curl --fail --location --silent --show-error   --output "$tmp/artifact.sh" "$artifact_url"

printf '%s  %s\n' "$expected_sha256" "$tmp/artifact.sh" |
  sha256sum --check --status -

/bin/bash "$tmp/artifact.sh" --mode install
```

### L3 follow-up discussion

A checksum fetched from the same compromised location provides limited protection. Signature verification and trusted release metadata are stronger.

### Production caution

Never use `curl URL | bash` for privileged production deployment.

---

## Q6. How do you implement API pagination?

### Detailed answer

Read the API's pagination contract: page/limit, offset, cursor, continuation token, or Link header. Loop until the server indicates completion, validate every page, and protect against repeated tokens or infinite loops.

Stream or incrementally process large datasets.

### Example

```bash
cursor=
while :; do
  response=$(api_get_page "$cursor") || exit
  jq -e '.items | arrays' <<<"$response" >/dev/null || exit 2

  jq -c '.items[]' <<<"$response" |
  while IFS= read -r item; do
    process_item "$item"
  done

  next=$(jq -r '.next // empty' <<<"$response")
  [[ -n $next && $next != "$cursor" ]] || break
  cursor=$next
done
```

### L3 follow-up discussion

Pipeline subshells matter if the inner loop updates parent counters. Use process substitution or aggregate outside.

### Production caution

Do not assume a short page means the last page unless the API documents that behavior.

---

## Q7. How should proxy variables be handled?

### Detailed answer

HTTP clients may honor `HTTP_PROXY`, `HTTPS_PROXY`, `ALL_PROXY`, and `NO_PROXY` with case and tool-specific differences. A script should document whether it inherits, overrides, or clears them and avoid leaking proxy credentials.

NO_PROXY matching behavior varies across clients, especially for CIDRs and domains.

### Example

```bash
proxy_env=()
[[ -n ${HTTPS_PROXY:-} ]] && proxy_env+=(HTTPS_PROXY="$HTTPS_PROXY")
[[ -n ${NO_PROXY:-} ]] && proxy_env+=(NO_PROXY="$NO_PROXY")

env "${proxy_env[@]}" curl --fail "$url"
```

### L3 follow-up discussion

For controlled system services, define proxy settings in the service configuration rather than depending on an interactive user's environment.

### Production caution

Do not print proxy URLs when they contain embedded usernames or passwords.

---

## Q8. How do you handle TLS certificates in automation?

### Detailed answer

Use the system trust store or an approved CA bundle, verify hostname/SAN and validity, and support mTLS with protected key files when required. Distinguish server certificate failure, client certificate failure, and unsupported protocol/cipher.

Certificate renewal should be monitored before expiry.

### Example

```bash
curl --fail --cacert "$ca_bundle"   --cert "$client_cert" --key "$client_key"   "$url"

openssl s_client -connect "$host:$port" -servername "$host" </dev/null
```

### L3 follow-up discussion

Do not parse `openssl s_client` output as the only production health check; use client exit status and proper verification options.

### Production caution

Private keys must have restrictive permissions and should not be copied to temporary locations unnecessarily.

---

## Q9. How do you make network operations idempotent?

### Detailed answer

Use GET for reads, conditional creation, PUT/PATCH with known desired state, idempotency keys where supported, and post-operation verification. Before retrying, determine whether the server may have completed the first request despite a lost response.

Store remote operation identifiers and reconcile rather than blindly resubmitting.

### Example

```bash
current=$(api_get_resource "$name") || exit
desired=$(build_desired_json)

if ! diff -q <(jq -S . <<<"$current") <(jq -S . <<<"$desired") >/dev/null; then
  api_update_resource "$name" "$desired"
fi
```

### L3 follow-up discussion

Discuss optimistic concurrency using resource versions or ETags to avoid overwriting another actor's change.

### Production caution

A timeout after POST does not prove the server created nothing. Blind retry can create duplicate resources.

---

## Q10. When should Bash not be used for network automation?

### Detailed answer

Move to Python, Go, or a supported SDK when the workflow needs complex authentication, streaming protocols, high concurrency, rich schemas, persistent state, advanced retry/circuit-breaking, binary data, or extensive unit testing.

Bash remains excellent for orchestration of stable command-line tools and small, transparent workflows.

### Example

```bash
# A useful decision check:
# - Is shell mostly connecting existing reliable CLIs? Bash may fit.
# - Is shell implementing a client library or state machine? Use another language.
```

### L3 follow-up discussion

An L3 engineer should be willing to stop adding abstractions to Bash when operational risk and maintenance cost exceed its simplicity.

### Production caution

Do not choose Bash only because the first prototype is short; consider the expected five-year lifecycle and support team.

---

## Recommended references

- curl manual
- OpenSSH client manual
- jq manual
- HTTP semantics specifications

---

<!-- Original file: 09. Linux System Administration Automation.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Linux System Administration Automation

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** systemd, configuration, packages, users, mounts, LVM, schedulers, sysctl, and container platforms.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. How should Bash interact with systemd services?

### Detailed answer

Use `systemctl` for state changes and machine-readable properties rather than parsing decorated status output. Check unit existence, active state, result, and job outcome. For long operations, use timeouts and journal correlation.

Prefer drop-in files for managed overrides and run `systemctl daemon-reload` only when unit definitions changed.

### Example

```bash
if systemctl is-active --quiet "$unit"; then
  printf '%s is active\n' "$unit"
fi

systemctl show "$unit"   --property=LoadState,ActiveState,SubState,Result   --value
```

### L3 follow-up discussion

Discuss oneshot service semantics, restart policy, start-limit behavior, dependencies, and using systemd to own scheduling, credentials, limits, and logging.

### Production caution

Do not parse `systemctl status` text for automation; it is intended for humans and can vary.

---

## Q2. How do you write a safe service restart script?

### Detailed answer

Validate the target and current health, check maintenance policy, capture before-state, restart or reload using the least disruptive method, wait for a bounded readiness condition, and roll back or escalate on failure.

A successful `systemctl restart` only means the manager accepted and completed the job; application readiness may still fail.

### Example

```bash
systemctl reload-or-restart "$unit"
deadline=$((SECONDS + 60))
until health_check; do
  (( SECONDS < deadline )) || {
    journalctl -u "$unit" --since '-5 min' >&2
    exit 1
  }
  sleep 2
done
```

### L3 follow-up discussion

Use application-specific health checks and dependency checks. Consider connection draining and cluster quorum before restart.

### Production caution

Never restart all replicas or cluster members in parallel without an availability design.

---

## Q3. How do you modify configuration files safely?

### Detailed answer

Prefer application-native commands, drop-ins, or configuration-management tools. If Bash must edit a file, parse only a format that is safe and simple, create an atomic replacement, preserve metadata, validate the new configuration, then reload and verify.

Do not use blind regex replacement on complex syntax.

### Example

```bash
tmp=$(mktemp --tmpdir="$(dirname "$config")" .config.XXXXXX)
awk -v value="$new_value" '
  BEGIN { found=0 }
  /^MaxClients[[:space:]]/ { print "MaxClients " value; found=1; next }
  { print }
  END { if (!found) print "MaxClients " value }
' "$config" >"$tmp"

app --check-config "$tmp"
install -o root -g app -m 0640 "$tmp" "$config"
systemctl reload app
```

### L3 follow-up discussion

Use a configuration parser or template when comments, includes, quoting, sections, duplicate keys, or order have semantics.

### Production caution

A successful sed command can still produce an invalid or semantically wrong configuration.

---

## Q4. How do you automate package management safely?

### Detailed answer

Use the platform package manager noninteractively with repository and version controls, but separate planning from mutation. Check lock/contention, available disk, signature verification, transaction result, required reboot, and service impact.

For fleets, use Ansible, image pipelines, or lifecycle tools rather than independent ad hoc scripts.

### Example

```bash
if rpm -q "$package" >/dev/null 2>&1; then
  current=$(rpm -q --qf '%{VERSION}-%{RELEASE}\n' "$package")
fi

dnf -y --setopt=install_weak_deps=False install "$package"
rpm -V "$package" || true
```

### L3 follow-up discussion

Discuss pinned versions, rollback limitations, dependency changes, kernel updates, and repository snapshots.

### Production caution

Do not run package updates concurrently with another package manager process or during an uncontrolled production window.

---

## Q5. How do you automate user and permission management?

### Detailed answer

Use authoritative identity systems where possible. For local accounts, validate names and IDs, check existing state, use idempotent commands, set expiry/locking policy, secure home and SSH keys, and log changes.

Avoid editing `/etc/passwd`, `/etc/shadow`, or sudoers with raw text operations.

### Example

```bash
if ! getent passwd "$user" >/dev/null; then
  useradd --create-home --shell /bin/bash -- "$user"
fi

install -d -o "$user" -g "$user" -m 0700 "/home/$user/.ssh"
visudo -cf "$sudoers_candidate"
```

### L3 follow-up discussion

Discuss UID conflicts across NFS, centralized groups, SELinux contexts, account removal, process ownership, and data retention.

### Production caution

Never overwrite sudoers without syntax validation and a recovery path.

---

## Q6. How do you inspect and manage mounts?

### Detailed answer

Use `findmnt` for machine-readable mount information and `/etc/fstab` tools or systemd mount units for persistence. Validate source identity by UUID/LABEL, filesystem type, options, target ownership, and application quiescence.

A mounted path can hide existing files underneath.

### Example

```bash
if findmnt --mountpoint "$mountpoint" >/dev/null; then
  findmnt --json --mountpoint "$mountpoint"
fi

mount --all --fake --verbose
```

### L3 follow-up discussion

For production changes, validate with `findmnt --verify`, backup fstab, mount in a test path where possible, and consider network filesystem timeouts and boot dependencies.

### Production caution

Do not identify disks only by `/dev/sdX`; enumeration can change across boots.

---

## Q7. How do you automate LVM or storage changes?

### Detailed answer

Inventory the block device, filesystem, mount, free extents, snapshots, multipath, and backups. Use idempotent checks and application-aware procedures. Extending is usually easier than shrinking; filesystem and LV operations have separate requirements.

Record before-state and validate the new block and filesystem sizes.

### Example

```bash
lvs --reportformat json
vgs --reportformat json
findmnt --json "$mountpoint"

# Example only after validation:
lvextend -L +10G /dev/vg/data
xfs_growfs "$mountpoint"
```

### L3 follow-up discussion

Discuss thin-pool capacity, snapshots, udev settlement, multipath, XFS non-shrinkability, and rollback limitations.

### Production caution

A script must never guess a device name or resize storage without confirming backups, ownership, and filesystem support.

---

## Q8. How do cron and systemd timers differ for Bash jobs?

### Detailed answer

Cron is simple but has a minimal environment, limited dependency semantics, and basic logging. systemd timers provide unit dependencies, randomized delay, persistent catch-up, resource controls, credentials, journal logging, and clear status.

Whichever scheduler is used, the script needs concurrency control, bounded runtime, and observable success.

### Example

```bash
# In a systemd service:
# ExecStart=/usr/local/sbin/job.sh
# User=jobuser
# RuntimeMaxSec=15min
# Nice=10
# IOSchedulingClass=best-effort
```

### L3 follow-up discussion

Explain timezone, missed runs, daylight-saving changes, overlapping executions, and maintenance blackout windows.

### Production caution

Do not assume cron's PATH, HOME, shell, or working directory matches an interactive session.

---

## Q9. How do you manage kernel parameters safely?

### Detailed answer

Use sysctl configuration files or approved system roles rather than one-off `echo` to `/proc/sys`. Validate parameter existence, current and desired values, platform support, application impact, and persistence.

Some settings are namespaced, boot-time-only, or managed by tuned or orchestration.

### Example

```bash
current=$(sysctl -n net.ipv4.ip_forward)
printf 'net.ipv4.ip_forward = 1\n' > /etc/sysctl.d/90-myapp.conf
sysctl --system
[[ $(sysctl -n net.ipv4.ip_forward) == 1 ]]
```

### L3 follow-up discussion

Discuss configuration precedence across `/usr/lib`, `/run`, and `/etc`, and how rollback removes or changes the owning file.

### Production caution

Do not apply internet tuning snippets without workload evidence and kernel/version review.

---

## Q10. How should a Bash script integrate with containers or Kubernetes?

### Detailed answer

Treat CLIs such as podman, docker, kubectl, and oc as APIs with versioned output. Request JSON and parse with `jq` or JSONPath rather than human tables. Use explicit contexts/namespaces, timeouts, labels, and idempotent declarative manifests.

Avoid embedding long-lived cluster credentials.

### Example

```bash
namespace=payments
pod_json=$(oc get pods -n "$namespace" -l app=api -o json)
not_ready=$(jq '[.items[] | select(any(.status.containerStatuses[]?; .ready == false))] | length' <<<"$pod_json")
(( not_ready == 0 )) || exit 1
```

### L3 follow-up discussion

Discuss server-side apply ownership, resourceVersion conflicts, rollout readiness, and avoiding `oc get | grep` parsing.

### Production caution

A successful CLI exit can mean the API accepted desired state, not that workloads became ready.

---

## Recommended references

- systemd manuals
- GNU/Linux administration command manuals
- LVM and util-linux documentation

---

<!-- Original file: 10. Portability Performance and Shell Selection.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Portability, Performance, and Shell Selection

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** POSIX versus Bash, platform differences, locale/time, profiling, scale, deterministic output, and dependencies.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. How do you decide between Bash and POSIX sh?

### Detailed answer

Use POSIX sh when the deployment requires broad minimal-shell portability and the script is simple enough to avoid arrays, `[[ ]]`, process substitution, associative maps, and Bash-specific introspection. Use Bash when those features materially improve correctness and maintainability and Bash is an explicit dependency.

Do not write Bash syntax with a `/bin/sh` shebang.

### Example

```bash
# POSIX:
#!/bin/sh
if [ -f "$file" ]; then
  printf '%s\n' "$file"
fi

# Bash:
#!/bin/bash
files=("$dir"/*.log)
[[ -e ${files[0]} ]] || files=()
```

### L3 follow-up discussion

Portability also includes external utilities: GNU and BSD/macOS options differ even when the shell syntax is portable.

### Production caution

“Runs on my RHEL server” is not evidence of POSIX portability.

---

## Q2. What portability problems commonly occur across Linux, macOS, and Unix?

### Detailed answer

Differences include `sed -i`, `date`, `stat`, `readlink -f`, `xargs`, `find`, `mktemp`, `timeout`, `realpath`, grep regex options, and Bash version. macOS may ship an older Bash due to licensing history.

Detect capabilities, use standard subsets, vendor a runtime, or define supported platforms explicitly.

### Example

```bash
require_gnu_date() {
  date --version >/dev/null 2>&1 || {
    printf 'GNU date is required\n' >&2
    return 1
  }
}
```

### L3 follow-up discussion

Prefer capability tests over operating-system-name branching where practical. CI should test every supported environment.

### Production caution

Do not silently use a different command syntax on an untested platform.

---

## Q3. How do locale and timezone affect scripts?

### Detailed answer

Locale affects sorting, character classes, case conversion, decimal formatting, and human-readable command output. Timezone affects parsing and presentation of dates. Set `LC_ALL=C` for deterministic byte-oriented processing when appropriate, but preserve Unicode requirements where needed.

Use ISO 8601 timestamps with offsets and avoid ambiguous local time.

### Example

```bash
export LC_ALL=C
timestamp=$(date -u +'%Y-%m-%dT%H:%M:%SZ')
printf '%s\n' "$timestamp"
```

### L3 follow-up discussion

DST can produce repeated or missing local times. Epoch timestamps are useful for duration and ordering; monotonic time is better for timeouts but not directly exposed portably in Bash.

### Production caution

Changing locale globally may alter application commands that expect UTF-8; scope it to the command where possible.

---

## Q4. How can Bash performance be improved?

### Detailed answer

Avoid spawning external processes inside hot loops when parameter expansion, arithmetic, or builtins suffice. Batch work, stream data, reduce repeated file reads, use arrays, and move large text processing into one awk/jq invocation rather than thousands of grep/sed calls.

Measure wall time, CPU, process count, I/O, and scale behavior before optimizing.

### Example

```bash
# Slow style:
# while ...; do basename "$path"; done

# Builtin expansion:
name=${path##*/}

# Measure:
time ./script.sh
/usr/bin/time -v ./script.sh
```

### L3 follow-up discussion

The largest performance improvement may be algorithmic or replacing Bash with awk/Python for data-heavy processing.

### Production caution

Do not sacrifice quoting and correctness for tiny micro-optimizations.

---

## Q5. What are common fork-heavy anti-patterns?

### Detailed answer

Examples include `cat file | grep`, one `sed` per line, repeated `date`, `basename`, or `dirname` in loops, and invoking `awk` for simple arithmetic. Each external command creates process overhead.

Some pipelines are still clearer and sufficiently efficient; optimize based on profile and input size.

### Example

```bash
# Instead of:
# value=$(echo "$line" | cut -d: -f1)

IFS=: read -r value _ <<<"$line"
```

### L3 follow-up discussion

Bash builtins reduce forks, but complex parsing may be more reliable in one dedicated external process.

### Production caution

Avoid replacing a correct single awk program with a complicated Bash loop merely to remove one process.

---

## Q6. How do you profile a Bash script?

### Detailed answer

Use `time`, `/usr/bin/time -v`, xtrace timestamps, `strace -f -c`, `perf` where appropriate, process counts, and targeted timing around phases. Generate representative data and separate external-command time from shell overhead.

A custom `SECONDS` or nanosecond timestamp can provide coarse phase duration.

### Example

```bash
start=$SECONDS
phase_one
printf 'phase=one duration_seconds=%d\n' "$((SECONDS-start))" >&2

strace -f -c ./script.sh
```

### L3 follow-up discussion

For xtrace profiling, route trace to a file and include epoch or high-resolution timestamps, but consider overhead and secrets.

### Production caution

Profiling data collected with `set -x` may alter timing significantly and expose sensitive values.

---

## Q7. How do you handle very large input?

### Detailed answer

Stream instead of command-substituting or mapfile-loading it. Use one-pass tools, avoid quadratic string concatenation, bound concurrency, and store intermediate state on disk or in a database when necessary.

Bash variables are not suitable for multi-gigabyte payloads or binary data.

### Example

```bash
producer |
awk '...single-pass transformation...' |
consumer
```

### L3 follow-up discussion

Watch pipe backpressure, disk space, temporary-file location, partial output, restartability, and checksums.

### Production caution

Do not write `data=$(cat huge_file)`; it consumes memory, removes trailing newlines, and cannot preserve NUL bytes.

---

## Q8. How do you make output deterministic?

### Detailed answer

Control locale, sort order, timestamps, random values, iteration over associative arrays, environment, and command versions. Use stable serialization and explicit field ordering.

Determinism improves tests, diffs, audit, and idempotency.

### Example

```bash
export LC_ALL=C
mapfile -t keys < <(printf '%s\n' "${!map[@]}" | sort)
for key in "${keys[@]}"; do
  printf '%s=%s\n' "$key" "${map[$key]}"
done
```

### L3 follow-up discussion

Separate volatile metadata from deterministic content so configuration checksums do not change on every run.

### Production caution

Do not rely on filesystem directory order or associative-array iteration order.

---

## Q9. How do Bash version differences affect scripts?

### Detailed answer

Features such as associative arrays, `mapfile`, namerefs, `wait -n`, `${var@Q}`, glob behavior, and bug fixes vary by Bash release. Define a minimum version and fail clearly or implement a tested fallback.

Check major/minor components using `BASH_VERSINFO`, not string comparison.

### Example

```bash
require_bash() {
  local min_major=5 min_minor=0
  if (( BASH_VERSINFO[0] < min_major ||
        (BASH_VERSINFO[0] == min_major && BASH_VERSINFO[1] < min_minor) )); then
    printf 'Bash %d.%d or newer is required\n' "$min_major" "$min_minor" >&2
    exit 2
  fi
}
```

### L3 follow-up discussion

A distribution may backport fixes without changing features exactly as upstream expectations suggest. Test the deployed runtime.

### Production caution

Do not assume `/bin/bash` on every platform supports modern features because your development machine does.

---

## Q10. How do you manage external command dependencies?

### Detailed answer

Inventory required commands, versions or capabilities, paths, privileges, and output formats. Validate early, use machine-readable output, and pin or containerize dependencies where reproducibility matters.

Optional dependencies should be detected and tied to explicit feature degradation.

### Example

```bash
require_cmd() {
  command -v "$1" >/dev/null 2>&1 || {
    printf 'Required command not found: %s\n' "$1" >&2
    return 127
  }
}

for cmd in jq curl flock; do
  require_cmd "$cmd" || exit
done
```

### L3 follow-up discussion

Version checks should be capability-oriented when vendor versions differ. Include dependency output in support diagnostics.

### Production caution

Do not discover a missing critical command after the script has already performed partial changes.

---

## Recommended references

- POSIX Shell Command Language
- GNU Bash compatibility notes
- ShellCheck portability checks

---

<!-- Original file: 11. Testing ShellCheck CI Quality and Reliability.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Testing, ShellCheck, CI Quality, and Reliability

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** Unit tests, mocks, boundary cases, signals, concurrency, idempotency, integration, and quality gates.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. How do you unit-test Bash functions?

### Detailed answer

Keep logic inside functions, minimize global state, and place `main "$@"` behind a direct-execution guard so tests can source the file. Replace external dependencies through PATH stubs, wrapper functions, or injected command variables.

Each test should arrange inputs, execute one behavior, assert stdout/stderr/status/side effects, and clean up independently.

### Example

```bash
# script.sh
normalize_name() {
  local value=$1
  printf '%s\n' "${value// /_}"
}

if [[ ${BASH_SOURCE[0]} == "$0" ]]; then
  main "$@"
fi

# test
source ./script.sh
actual=$(normalize_name 'one value')
[[ $actual == one_value ]]
```

### L3 follow-up discussion

Use Bats or another framework for fixtures and reporting, but understand how it executes tests, captures status, and handles subshells.

### Production caution

Do not run destructive production defaults during test sourcing. Top-level code must be inert.

---

## Q2. What should be tested besides the happy path?

### Detailed answer

Test missing dependencies, invalid input, empty input, filenames with spaces/newlines/leading dashes, command failures, timeouts, interrupted execution, partial state, permission denial, full disk, concurrency conflict, stale locks, malformed API responses, and cleanup failure.

Also test reruns after a partial failure to prove idempotency.

### Example

```bash
cases=(
  ''
  'normal'
  'with spaces'
  '-leading-dash'
  $'contains\nnewline'
  '*?[glob]'
)

for value in "${cases[@]}"; do
  run_test_case "$value"
done
```

### L3 follow-up discussion

Production bugs cluster at boundaries and recovery paths, not only normal transformations.

### Production caution

A test suite that mocks every command as success proves very little about operational behavior.

---

## Q3. How do you mock external commands?

### Detailed answer

Create a temporary `bin` directory placed first in PATH and add executable stubs that record arguments and return controlled statuses. Alternatively, wrap commands in functions that tests redefine.

Mocks should preserve argument boundaries and expose calls in a structured log.

### Example

```bash
test_bin=$(mktemp -d)
cat >"$test_bin/curl" <<'STUB'
#!/bin/bash
printf '%q\n' "$@" >>"${CALL_LOG:?}"
printf '{"status":"ok"}'
STUB
chmod +x "$test_bin/curl"

PATH="$test_bin:$PATH" CALL_LOG="$tmp/calls" ./script.sh
```

### L3 follow-up discussion

Use integration tests with real tools as well; mocks can diverge from actual option parsing and error behavior.

### Production caution

Do not accidentally let a test invoke the real `rm`, cloud CLI, or production API because the stub path was not applied.

---

## Q4. How do you assert command arguments accurately?

### Detailed answer

Record each argument separately or NUL-delimited rather than joining with spaces. This proves quoting and array construction for spaces, empty strings, and wildcard characters.

A human-readable `%q` trace is useful, but machine assertions should preserve boundaries.

### Example

```bash
# Stub:
printf '%s\0' "$@" >"$ARGS_FILE"

# Test:
mapfile -d '' -t args <"$ARGS_FILE"
[[ ${args[0]} == --output ]]
[[ ${args[1]} == 'file with space' ]]
```

### L3 follow-up discussion

Include tests for empty arguments and leading-dash values, which are easily lost in string-based mocks.

### Production caution

Comparing a single flattened command string can report success while the real command receives the wrong number of arguments.

---

## Q5. How do ShellCheck and formatting tools fit into quality gates?

### Detailed answer

ShellCheck statically detects common quoting, test, pipeline, array, and portability defects. A formatter such as shfmt enforces consistent style. CI should run both with a pinned version and an explicit shell dialect.

Suppress warnings narrowly with a reason; do not disable broad classes just to make CI green.

### Example

```bash
shellcheck --shell=bash --external-sources scripts/*.sh
shfmt -d -i 2 -ci scripts/
```

### L3 follow-up discussion

ShellCheck cannot understand every runtime trust boundary, data format, command side effect, or distributed failure. It complements tests and review.

### Production caution

A clean ShellCheck report does not prove a script is secure or production-ready.

---

## Q6. How do you test signal handling?

### Detailed answer

Start the script in a controlled process group, wait until it reaches a known checkpoint, send TERM or INT, then assert exit status, child termination, lock release, and temporary-file cleanup. Use deterministic synchronization rather than arbitrary sleeps when possible.

Test SIGKILL expectations separately because traps cannot handle it.

### Example

```bash
./long_job.sh &
pid=$!

wait_for_file "$tmp/started"
kill -TERM "$pid"

if wait "$pid"; then
  printf 'Expected nonzero status\n' >&2
  exit 1
fi

[[ ! -e $tmp/resource.lock ]]
```

### L3 follow-up discussion

Use containers or disposable namespaces for tests that manipulate processes and mounts.

### Production caution

Do not assume killing the parent kills grandchildren; test process-group behavior explicitly.

---

## Q7. How do you test concurrent execution and locks?

### Detailed answer

Launch several instances simultaneously and assert only the permitted number enters the critical section. Test stale lock recovery, timeout behavior, and lock release after failure or signal.

Prefer kernel-backed `flock` where available over pidfile-only schemes.

### Example

```bash
lock_file=$tmp/job.lock
entered=$tmp/entered

for _ in {1..10}; do
  LOCK_FILE=$lock_file ENTERED=$entered ./job.sh &
done
wait

[[ $(wc -l <"$entered") -eq 1 ]]
```

### L3 follow-up discussion

For a worker pool, assert the maximum active count never exceeds the limit and every item has exactly one terminal status.

### Production caution

A lock test that starts jobs sequentially does not test race behavior.

---

## Q8. How do you test idempotency?

### Detailed answer

Run the operation twice against the same initial target and assert the second run makes no unintended changes, returns a defined status, and produces a stable result. Also test recovery from a deliberately interrupted first run.

Compare normalized state rather than volatile timestamps.

### Example

```bash
setup_fixture
./configure.sh
state1=$(capture_normalized_state)

./configure.sh
state2=$(capture_normalized_state)

diff -u <(printf '%s\n' "$state1") <(printf '%s\n' "$state2")
```

### L3 follow-up discussion

Idempotency can include “safe repeat” rather than zero work, but side effects such as duplicate accounts, API resources, or backup records must be controlled.

### Production caution

A dry-run that prints nothing does not prove the real second execution is idempotent.

---

## Q9. How do you build integration tests for system scripts?

### Detailed answer

Use disposable virtual machines, containers where system behavior is representative, or ephemeral cloud instances. Seed known state, execute the real script with actual dependencies, validate system state and logs, then destroy the environment.

Test each supported OS and Bash version in CI or a scheduled pipeline.

### Example

```bash
# Example test phases:
# 1. Provision clean VM image
# 2. Install dependencies
# 3. Copy script
# 4. Execute
# 5. Reboot if required
# 6. Validate service, files, permissions, and rerun
```

### L3 follow-up discussion

Containers may not faithfully reproduce systemd, kernel, mount, LVM, SELinux, or reboot behavior. Choose the environment according to the operation.

### Production caution

Never run integration tests for storage deletion or firewall changes on shared production infrastructure.

---

## Q10. What coverage and evidence should a Bash project maintain?

### Detailed answer

Track requirements and failure modes to tests rather than relying only on line coverage. Maintain unit, integration, security, portability, and recovery tests. Store test reports, lint results, supported runtime matrix, and known exclusions.

Coverage tools for shell can be useful but may distort execution and still miss concurrency or environment paths.

### Example

```bash
# Quality gate example:
shellcheck scripts/*.sh
shfmt -d scripts/
bats test/unit
bats test/integration
```

### L3 follow-up discussion

A risk-based test matrix should prioritize privileged, destructive, networked, and recovery code.

### Production caution

One hundred percent line coverage can still omit meaningful assertions and operational failure conditions.

---

## Recommended references

- Bats documentation
- ShellCheck documentation
- shfmt documentation

---

<!-- Original file: 12. Advanced Text Processing Patterns and Bash Internals.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Advanced Text Processing, Patterns, and Internals

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** awk, sed, stable output, globs, printf, time/version handling, procfs, binary data, and diagnostics.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. When should `awk` be preferred over a Bash loop?

### Detailed answer

Use awk for record-oriented text processing, field calculations, grouping, aggregation, and one-pass transformations. It avoids spawning multiple utilities per record and has a clearer data-processing model.

Use Bash to orchestrate files and commands around the awk program rather than reimplementing the transformation line by line.

### Example

```bash
awk -F: '
  $3 >= 1000 {
    count++
    shell[$7]++
  }
  END {
    print "users", count
    for (s in shell) print s, shell[s]
  }
' /etc/passwd
```

### L3 follow-up discussion

Pass data with `-v name="$value"` rather than interpolating shell text into awk source. Understand numeric/string comparison and locale.

### Production caution

Do not use awk to parse formats whose grammar needs a dedicated parser, such as JSON or YAML.

---

## Q2. How do you use sed safely?

### Detailed answer

Use sed for simple, well-defined stream substitutions and selections. Quote the script so shell expansion is intentional, escape delimiter and replacement metacharacters, and validate output before replacing configuration.

Portable in-place editing differs across implementations; writing to a temporary file is safer.

### Example

```bash
escaped=${replacement//\/\\}
escaped=${escaped//&/\&}
escaped=${escaped//|/\|}

sed "s|^Key=.*$|Key=$escaped|" "$config" >"$tmp"
validate_config "$tmp"
mv -- "$tmp" "$config"
```

### L3 follow-up discussion

The pattern side and replacement side have different escaping rules. Complex config changes should use a parser/template.

### Production caution

Never assume arbitrary user text can be inserted into a sed expression without escaping.

---

## Q3. What are the risks of parsing human-readable command output?

### Detailed answer

Human output can change with version, locale, terminal width, color, warnings, and data. Use machine-readable modes such as JSON, key-value, null-delimited, custom format strings, or stable APIs.

If no machine format exists, set locale and parse the narrowest documented output with tests for every supported version.

### Example

```bash
# Prefer:
lsblk --json
findmnt --json
systemctl show --property=ActiveState --value
rpm -q --qf '%{NAME}\t%{VERSION}-%{RELEASE}\n'
```

### L3 follow-up discussion

Document the exact external command version/capability in the dependency contract.

### Production caution

Parsing `ps`, `df -h`, `ls -l`, or `systemctl status` visual columns is fragile.

---

## Q4. How do extended globs and `globstar` work?

### Detailed answer

With `extglob`, Bash supports operators such as `@(p1|p2)`, `?(p)`, `+(p)`, `*(p)`, and `!(p)`. With `globstar`, `**` can recursively match directories. `nullglob`, `dotglob`, and `failglob` further change behavior.

These options are powerful but Bash-specific and should be set/restored deliberately.

### Example

```bash
shopt -s extglob nullglob globstar

logs=(/var/log/**/@(app|api)-+([0-9]).log)
printf '%s\n' "${logs[@]}"
```

### L3 follow-up discussion

Pattern parsing can depend on whether extglob was enabled before the containing construct was parsed, especially in functions or compound commands.

### Production caution

Recursive globs may traverse huge trees or unexpected mounts; use explicit roots and constraints.

---

## Q5. Explain `printf` versus `echo`.

### Detailed answer

`printf` has defined formatting and handles leading dashes and backslash sequences predictably when the format is constant. `echo` behavior varies for `-n`, `-e`, and backslashes across implementations.

Use `printf '%s\n' "$value"` for arbitrary strings. Never let untrusted input become the format string.

### Example

```bash
printf '%s\n' "$value"
printf 'count=%d status=%q\n' "$count" "$status"
```

### L3 follow-up discussion

Bash `printf -v var` writes to a variable without command substitution, and `%q` produces a reusable diagnostic shell representation.

### Production caution

`printf "$user_value"` is a format-string bug. Always supply a constant format.

---

## Q6. How do you compare timestamps and durations?

### Detailed answer

For durations, capture start and end using `SECONDS` for coarse measurements or an external monotonic-capable tool for precision. For absolute timestamps, use epoch seconds or a canonical ISO format. Parsing human date strings is platform-dependent and ambiguous.

Do not subtract local formatted timestamps across DST changes.

### Example

```bash
start=$SECONDS
run_task
duration=$((SECONDS - start))
printf 'duration_seconds=%d\n' "$duration"

epoch=$(date +%s)
utc=$(date -u +'%Y-%m-%dT%H:%M:%SZ')
```

### L3 follow-up discussion

Bash 5 has `EPOCHSECONDS` and `EPOCHREALTIME` in many environments, but define the minimum version or fallback.

### Production caution

System wall-clock adjustments can distort duration measured from epoch time.

---

## Q7. How do you compare version strings correctly?

### Detailed answer

Version comparison depends on the versioning scheme. Lexical comparison is wrong for values such as 2.10 and 2.9. For RPM/DEB packages, use the package manager's version algorithm. For semantic versions, use a tested parser or sort option only if it matches the specification.

Do not invent one universal version comparator.

### Example

```bash
# RPM:
rpm --compare-versions '1.2-3' '>=' '1.2-2'

# GNU version sort may help for simple dotted versions:
printf '%s\n' 2.9 2.10 | sort -V
```

### L3 follow-up discussion

Pre-release, epoch, release, vendor suffix, and leading-zero semantics differ across ecosystems.

### Production caution

A homemade `IFS=.` numeric loop often mishandles suffixes and missing components.

---

## Q8. How do you read from `/proc` and `/sys` safely?

### Detailed answer

Treat procfs and sysfs files as kernel APIs with version, permission, namespace, and lifecycle semantics. Read exact documented files, validate numeric content, and expect processes/devices to disappear between operations.

Writes can immediately change system behavior and should use approved interfaces and rollback.

### Example

```bash
read -r load1 load5 load15 _ </proc/loadavg
[[ $load1 =~ ^[0-9]+([.][0-9]+)?$ ]] || exit 1

if [[ -r /sys/class/net/eth0/operstate ]]; then
  read -r state </sys/class/net/eth0/operstate
fi
```

### L3 follow-up discussion

Do not recursively parse all of `/proc` or assume files are regular, seekable, or stable.

### Production caution

Never write arbitrary tuning values to sysfs from unvalidated input.

---

## Q9. How do you handle binary or encoded data?

### Detailed answer

Bash variables cannot contain NUL bytes and are not a binary-safe data store. Stream binary data through files, pipes, base64/hex encoders, or dedicated tools. Validate checksums and sizes.

Encoding expands data and does not provide encryption.

### Example

```bash
base64 <"$binary_file" >"$encoded_file"
base64 --decode <"$encoded_file" >"$decoded_file"
cmp --silent "$binary_file" "$decoded_file"
```

### L3 follow-up discussion

Be aware that base64 command options differ across GNU and BSD implementations. For large data, stream rather than command-substitute.

### Production caution

Do not store private keys or binary archives in shell variables.

---

## Q10. What Bash features are useful for advanced diagnostics?

### Detailed answer

`BASH_SOURCE`, `FUNCNAME`, `BASH_LINENO`, `BASH_COMMAND`, `BASHPID`, `BASH_SUBSHELL`, `PIPESTATUS`, `SECONDS`, `caller`, `declare -p`, and `compgen` help inspect execution.

Use them in controlled debug output and error reports, with secret redaction.

### Example

```bash
debug_stack() {
  local i
  for ((i=0; i<${#FUNCNAME[@]}-1; i++)); do
    printf 'frame=%d file=%q line=%s function=%q\n'       "$i" "${BASH_SOURCE[i+1]}" "${BASH_LINENO[i]}" "${FUNCNAME[i+1]}" >&2
  done
}
```

### L3 follow-up discussion

`declare -p` produces shell syntax and can expose secrets. Maintain an allowlist of safe variables for diagnostics.

### Production caution

Never dump the entire environment or all shell variables into a support log by default.

---

## Recommended references

- GNU awk manual
- GNU sed manual
- GNU Bash Reference Manual

---

<!-- Original file: 13. Bash in CI CD DevOps and Release Automation.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — CI/CD, DevOps, and Release Automation

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** Pipeline design, secrets, reproducibility, gates, promotion, rollback, concurrency, and artifact evidence.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. How should Bash be used in CI/CD pipelines?

### Detailed answer

Use Bash as a thin orchestration layer around build, test, security, packaging, and deployment tools. Enable strict error handling, pin dependencies, isolate workspaces, protect credentials, and emit artifacts and logs with clear exit codes.

Complex pipeline state should live in the CI system or a purpose-built tool rather than hidden global shell variables.

### Example

```bash
#!/usr/bin/env bash
set -Eeuo pipefail

main() {
  validate_environment
  run_lint
  run_tests
  build_artifact
  verify_artifact
  publish_artifact
}

main "$@"
```

### L3 follow-up discussion

Discuss cancellation, job timeout, retry ownership, artifact retention, branch protection, and reproducible local execution.

### Production caution

Do not put all deployment logic inside one unreadable inline YAML command block.

---

## Q2. How do you prevent secret leakage in CI?

### Detailed answer

Use the platform's secret store, short-lived tokens, protected runners, environment allowlists, masked logs, and scoped credentials. Disable xtrace before secret access and avoid passing secrets as command arguments.

Assume artifacts, caches, process listings, debug logs, and failure bundles may be retained.

### Example

```bash
set +x
token=${DEPLOY_TOKEN:?}
curl -H "Authorization: Bearer $token" ...
unset token
```

### L3 follow-up discussion

Secret masking is not guaranteed for encoded, transformed, substring, multiline, or command-generated values. Design for no output rather than relying only on masking.

### Production caution

Never print `env`, `set`, or request headers in a secret-bearing job.

---

## Q3. How do you make pipeline scripts reproducible?

### Detailed answer

Pin tool images or versions, lock dependencies, control locale/timezone, record source commit and artifact hashes, and avoid mutable latest tags. Use the same script locally and in CI where possible.

Separate environment-specific configuration from build logic.

### Example

```bash
printf 'commit=%s\n' "${GIT_COMMIT:?}"
printf 'bash=%s\n' "$BASH_VERSION"
sha256sum dist/*
```

### L3 follow-up discussion

A reproducible build also depends on compiler, package repository snapshots, timestamps, network inputs, and base images, not only Bash.

### Production caution

Do not download unpinned tools during every pipeline run from an unverified latest URL.

---

## Q4. How do you implement deployment gates?

### Detailed answer

Validate artifact signature/hash, target environment, approval, maintenance window, health baseline, capacity, backup, and change identifier before mutation. Separate plan and apply and require explicit environment selection.

After deployment, run readiness and business validation and record the result.

### Example

```bash
[[ ${ENVIRONMENT:-} == production ]] || exit 2
[[ -n ${CHANGE_ID:-} ]] || exit 2
verify_artifact "$artifact"
check_platform_health
deploy "$artifact"
verify_business_transaction
```

### L3 follow-up discussion

The CI platform should enforce protected environments and approvals; the script provides a second technical guard.

### Production caution

A string variable named `APPROVED=true` is not sufficient authorization if any user can set it.

---

## Q5. How should artifacts be promoted?

### Detailed answer

Build once, verify, sign, and promote the same immutable artifact through environments. Do not rebuild independently for production. Record digest, source commit, test evidence, configuration version, and approver.

Bash can coordinate registry or repository promotion with API clients.

### Example

```bash
digest=$(sha256sum "$artifact" | awk '{print $1}')
publish_to_repo "$artifact" "$digest"
promote_digest "$digest" test staging production
```

### L3 follow-up discussion

For container images, deploy by digest. Environment-specific configuration should be separate from the binary/image.

### Production caution

Mutable tags can point to different content between validation and deployment.

---

## Q6. How do you handle rollback in deployment scripts?

### Detailed answer

Define rollback before deployment: previous artifact digest, configuration, schema compatibility, data backup, and traffic strategy. Automatically roll back only when the failure is well understood and rollback itself is safe.

Some database or state migrations are irreversible; in those cases use forward-fix or restore.

### Example

```bash
previous=$(get_current_release)
deploy "$new_release"

if ! verify_release; then
  log ERROR "Validation failed; restoring $previous"
  deploy "$previous"
  verify_release || exit 5
fi
```

### L3 follow-up discussion

Record which phase completed and whether rollback restored both code and configuration. Preserve failed release evidence.

### Production caution

Do not automatically roll back application code after an irreversible data migration.

---

## Q7. How do you handle parallel CI jobs writing shared resources?

### Detailed answer

Use unique workspaces, artifact names, namespaces, and correlation IDs. For intentionally shared resources, use a lock, lease, or CI concurrency group with expiration and ownership.

Cleanup must not delete resources belonging to another run.

### Example

```bash
run_id=${CI_PIPELINE_ID:?}-${CI_JOB_ID:?}
workspace=$WORK_ROOT/$run_id
mkdir -p -- "$workspace"

resource_name="test-${run_id//[^A-Za-z0-9-]/-}"
```

### L3 follow-up discussion

Use labels/tags containing the run ID and ownership for safe garbage collection. Test cancellation and stale-resource cleanup.

### Production caution

A cleanup command using a broad prefix can delete resources from active pipelines.

---

## Q8. How should a pipeline publish test and diagnostic results?

### Detailed answer

Produce machine-readable test reports, lint output, artifact manifests, checksums, and concise logs. On failure, retain only necessary diagnostics under access controls and publish a clear summary with next action.

Separate build artifacts from debug artifacts and define retention.

### Example

```bash
mkdir -p artifacts/reports
shellcheck -f checkstyle scripts/*.sh >artifacts/reports/shellcheck.xml
bats --formatter junit test >artifacts/reports/tests.xml
sha256sum dist/* >artifacts/SHA256SUMS
```

### L3 follow-up discussion

Ensure the pipeline preserves the original failure status even when artifact upload also fails.

### Production caution

Do not upload `.env`, temporary credentials, private keys, or complete workspace archives blindly.

---

## Q9. How do Git hooks compare with CI checks?

### Detailed answer

Local hooks provide fast feedback but can be bypassed and vary by developer machine. CI is the authoritative gate. Share the same scripts/configuration so hooks and CI do not diverge.

Pre-commit can run formatting and lightweight lint; CI runs full tests and security checks.

### Example

```bash
./ci/lint.sh
./ci/test.sh

# Both hooks and pipeline call these same scripts.
```

### L3 follow-up discussion

Pin hook tool versions and make setup easy, but never rely on hooks for mandatory compliance.

### Production caution

A passed local hook does not prove the committed content was tested by a protected CI runner.

---

## Q10. When should pipeline Bash be replaced by another tool?

### Detailed answer

Replace large Bash pipeline logic when it implements complex dependency graphs, rich state, API clients, business rules, parallel orchestration, or reusable libraries that need types and deeper testing. Use the CI platform, Python/Go, Terraform, Ansible, Helm, or a domain-specific deployment controller.

Keep Bash for transparent command composition and small glue.

### Example

```bash
# Warning signals:
# - Hundreds of lines of argument parsing
# - Nested JSON/YAML manipulation
# - Many retries and asynchronous states
# - Persistent checkpoints
# - Multiple providers/APIs
```

### L3 follow-up discussion

Migration can be incremental: retain a small Bash wrapper while moving core logic into a tested executable.

### Production caution

Do not keep extending Bash solely because replacing it now feels more expensive; measure ongoing incident and maintenance cost.

---

## Recommended references

- CI platform security guidance
- Supply-chain artifact signing guidance
- ShellCheck documentation

---

<!-- Original file: 14. Corporate Production Scenario Questions.md -->

# Corporate-Level Bash Scripting L3 Interview Q&A — Production Scenarios

> **Level:** Corporate / Senior / L3  
> **Shell baseline:** GNU Bash 5.x concepts, with POSIX portability notes where relevant  
> **Scope:** Real incidents involving data loss, concurrency, APIs, cron, SSH, configuration, pipelines, portability, and recovery.

## How to answer at L3 level

A strong answer should explain the mechanism, show a safe implementation, discuss failure modes, security, portability, performance, observability, rollback, and production validation.

---

## Q1. A backup script reports success, but the compressed backup is corrupt.

### Detailed answer

The script likely checked only the final command or file existence. With a pipeline such as database dump piped to gzip, gzip may succeed after the dump command fails unless `pipefail` or explicit `PIPESTATUS` checking is used. A nonempty file is also not proof of recoverability.

### Example

```bash
Reproduce with logs, enable `set -o pipefail`, capture each pipeline status, inspect source command errors, validate archive integrity, checksum it, and perform a restore test.
```

### L3 follow-up discussion

Correct status propagation, write to a temporary file, validate, then atomically publish. Expose backup success and last verified restore metrics.

### Production caution

Do not delete the previous known-good backup until the new backup has passed integrity and recovery checks.

---

## Q2. A script works manually but fails from cron.

### Detailed answer

Cron has a minimal environment, different PATH, HOME, working directory, shell, locale, umask, and credentials. Relative paths and interactive assumptions commonly fail.

### Example

```bash
Capture `env`, current directory, user, PATH, and stderr in a controlled test. Use absolute paths, explicit environment, and a dedicated log.
```

### L3 follow-up discussion

Move the job to a systemd service/timer when stronger dependencies, timeouts, credentials, and observability are needed.

### Production caution

Do not solve the issue by copying the entire interactive environment, which may include secrets and unstable settings.

---

## Q3. A cleanup script deleted the wrong directory.

### Detailed answer

The root cause is usually an empty or malformed variable, unquoted expansion, unsafe glob, failed `cd`, path traversal, or inadequate target validation. The incident requires immediate containment and evidence preservation.

### Example

```bash
Stop automation, capture script version, arguments, environment, logs, shell options, and affected paths. Verify backups and filesystem recovery options.
```

### L3 follow-up discussion

Require a validated absolute root, reject `/` and empty values, use dry-run manifests, approval for destructive scope, `--`, quoted paths, and deletion limited to an allowlisted directory.

### Production caution

Do not rerun the script to “see what happens.” Preserve state and initiate recovery.

---

## Q4. A `while read` loop updates a counter, but the counter is zero afterward.

### Detailed answer

The loop probably ran in a subshell because input arrived through a pipeline. Variable updates occurred in the child shell and disappeared when it exited.

### Example

```bash
Inspect the construct and `BASH_SUBSHELL`. Replace the pipeline with input redirection or process substitution.
```

### L3 follow-up discussion

Use `while ...; done <file` or `done < <(producer)` in Bash. Alternatively, perform aggregation in awk.

### Production caution

Enabling `lastpipe` changes behavior only under specific conditions and can make scripts harder to reason about across environments.

---

## Q5. A script with `set -e` continued after a failed command.

### Detailed answer

Errexit has exceptions in conditions, `&&`/`||` lists, pipelines without pipefail, negation, command substitutions, and certain function contexts. The failed command may have occurred where Bash suppresses automatic exit.

### Example

```bash
Create a minimal reproducer, inspect shell options, pipeline status, and calling context. Add explicit status handling around critical operations.
```

### L3 follow-up discussion

Use `if command; then ... else ... fi`, `pipefail`, and defined wrappers for important changes. Treat strict mode as a guard, not a full error model.

### Production caution

Do not add `|| true` broadly; it converts real failures into silent success.

---

## Q6. A script prints secrets after debug mode is enabled.

### Detailed answer

`set -x` prints expanded commands, including variables, command substitutions, and headers. CI masking may not recognize transformed secrets.

### Example

```bash
Revoke exposed credentials, restrict and review logs/artifacts, identify all trace outputs, and determine downstream exposure.
```

### L3 follow-up discussion

Disable tracing around secrets, use protected `BASH_XTRACEFD`, redact command logging, and prefer file-descriptor or credential APIs.

### Production caution

Deleting the visible log is not enough if it was forwarded, cached, or included in support bundles.

---

## Q7. An SSH fan-out script leaves hundreds of orphaned remote jobs.

### Detailed answer

The local script probably timed out or was killed without propagating cancellation, while remote commands detached or continued after the SSH connection closed. Unbounded concurrency may also obscure PIDs and statuses.

### Example

```bash
Inventory remote processes by correlation ID, user, cgroup, and start time. Stop safely, capture per-host results, and review SSH/client timeout behavior.
```

### L3 follow-up discussion

Use bounded concurrency, remote systemd-run scopes or job IDs, traps that signal children, and explicit cancellation/reconciliation.

### Production caution

Do not use a broad `pkill` by command name across production hosts.

---

## Q8. A script hangs indefinitely on a network call.

### Detailed answer

The client may lack DNS, connect, transfer, read, or total timeouts. A child may wait on input or a pipe may remain open because a descriptor leaked.

### Example

```bash
Use process state, `strace`, socket inspection, child tree, file descriptors, and client verbose timing. Determine DNS, connect, TLS, server, or pipe wait.
```

### L3 follow-up discussion

Apply native and outer timeouts, close unused descriptors, define retry policy, and emit phase timing.

### Production caution

Killing only the parent may leave child or grandchild processes running.

---

## Q9. Two scheduled instances corrupt the same state file.

### Detailed answer

There is no concurrency control or the lock protects only part of the critical section. Both writers may truncate or interleave content.

### Example

```bash
Examine timestamps, process IDs, lock implementation, open files, and state-file history. Stop new runs and recover the last valid state.
```

### L3 follow-up discussion

Use `flock`, a private lock file descriptor, atomic same-filesystem replacement, bounded lock wait, and one owner for read-modify-write.

### Production caution

A pidfile without atomic creation and stale-process validation is not a reliable lock.

---

## Q10. A lock file remains after a crash and blocks all future runs.

### Detailed answer

The design likely treats file existence as a lock. Files do not disappear automatically on process death. Kernel-backed `flock` releases when descriptors close.

If a pidfile is required, it must include identity and stale validation.

### Example

```bash
Verify whether the recorded PID exists and belongs to the same executable/start time. Check for a currently held kernel lock before removing anything.
```

### L3 follow-up discussion

Migrate to `flock` where available or implement a lease with owner and expiry.

### Production caution

Never delete a lock merely because it is old; a legitimate long-running job may still own it.

---

## Q11. A script parses `df -h` and breaks after a locale or version change.

### Detailed answer

Human-readable output is unstable and localized. Column widths and labels can change, and mounted paths may contain spaces.

### Example

```bash
Set a deterministic locale for diagnosis and compare with machine-readable or explicit-format alternatives such as `df -P` or `findmnt --json`.
```

### L3 follow-up discussion

Replace visual-column parsing with documented fields and tests for supported platforms.

### Production caution

Do not merely change the awk field number; the underlying contract remains fragile.

---

## Q12. An API creation request times out, and retry creates duplicates.

### Detailed answer

The server may have committed the first POST but the response was lost. A timeout is an unknown outcome, not proof of failure.

### Example

```bash
Query by idempotency key, client-generated unique name, correlation ID, or remote list/search. Review server logs and request IDs.
```

### L3 follow-up discussion

Use idempotency keys, conditional PUT, or reconciliation before retry. Persist the request identifier locally.

### Production caution

Blind POST retry can create duplicate infrastructure and financial cost.

---

## Q13. A JSON payload fails only when a value contains quotes or newlines.

### Detailed answer

The script probably built JSON through string interpolation instead of an encoder. Quotes, backslashes, control characters, and Unicode require JSON escaping.

### Example

```bash
Capture a redacted payload, validate with `jq -e`, and reproduce with boundary values.
```

### L3 follow-up discussion

Generate payloads using `jq -n --arg/--argjson` or a real language client and validate types.

### Production caution

Do not attempt to escape JSON with a few sed replacements.

---

## Q14. A script loses the last line of a file.

### Detailed answer

The final line may not end with a newline, so `read` returns failure after assigning data and the loop body does not run unless the condition handles nonempty content.

### Example

```bash
Inspect the file with a binary-aware view and reproduce with an unterminated last line.
```

### L3 follow-up discussion

Use `while IFS= read -r line || [[ -n $line ]]; do ...; done`.

### Production caution

Do not append a newline to source data automatically unless changing the file is permitted.

---

## Q15. A filename beginning with `-` is interpreted as an option.

### Detailed answer

The script quoted the filename but did not terminate option parsing. Quoting preserves one argument but does not change its leading characters.

### Example

```bash
Reproduce with a test file such as `-rf`, inspect command arguments, and review utility support for `--`.
```

### L3 follow-up discussion

Use `command -- "$file"`, an absolute path, or `./-name` as appropriate. Add boundary tests.

### Production caution

Do not rename user data silently just to avoid correct option handling.

---

## Q16. A package-install script runs twice and starts conflicting transactions.

### Detailed answer

There is no global package-manager concurrency control, or two schedulers overlap. Package managers may have their own locks, but waiting or failure behavior must be handled.

### Example

```bash
Inspect package-manager processes, locks, journal/transaction history, and scheduler overlap.
```

### L3 follow-up discussion

Acquire an application lock, use a bounded wait, serialize changes, and prefer fleet orchestration with maintenance control.

### Production caution

Do not remove package-manager lock files while another transaction is active.

---

## Q17. A systemd restart command succeeds but the service is unusable.

### Detailed answer

The service manager completed the restart job, but the application may be starting, degraded, unable to reach dependencies, or failing its business endpoint. Active state is not equivalent to readiness.

### Example

```bash
Inspect `systemctl show`, journal, listener, dependency health, and application-specific readiness/business test.
```

### L3 follow-up discussion

Add bounded readiness validation and rollback/escalation. Use reload where safe to reduce disruption.

### Production caution

Do not restart repeatedly; start-limit and cascading dependency failures can worsen the incident.

---

## Q18. A sed-based config change duplicates the setting on every run.

### Detailed answer

The edit is not idempotent or does not recognize existing variants, comments, whitespace, includes, or duplicate-key semantics.

### Example

```bash
Compare before/after files, validate with the application's parser, and identify which occurrence is authoritative.
```

### L3 follow-up discussion

Use a template, native configuration command, or parser. If the format is simple, replace or insert exactly once and test reruns.

### Production caution

Do not delete all matching lines unless duplicate semantics and comments are understood.

---

## Q19. A pipeline returns success although an early command failed.

### Detailed answer

Without `pipefail`, the status is normally that of the last command. A successful `tee`, `cat`, or compressor can hide failure upstream.

### Example

```bash
Inspect `PIPESTATUS`, enable pipefail in a reproducer, and validate output completeness.
```

### L3 follow-up discussion

Enable `set -o pipefail`, capture statuses immediately, and avoid pipelines where per-stage recovery is required.

### Production caution

Some expected SIGPIPE cases need explicit handling rather than blindly treating every nonzero pipeline component as fatal.

---

## Q20. A command substitution removes required trailing newlines.

### Detailed answer

Bash command substitution strips all trailing newline characters. If trailing newlines are meaningful, a variable is the wrong transport.

### Example

```bash
Compare raw file bytes and the command-substituted value. Reproduce with multiple trailing newlines.
```

### L3 follow-up discussion

Use a file, pipe, or sentinel technique only when carefully designed. Prefer streaming data directly to the consumer.

### Production caution

Bash variables also cannot preserve NUL, so they are not general byte containers.

---

## Q21. A remote command uses the wrong local value.

### Detailed answer

Local and remote shells parsed different parts of the SSH command. Incorrect quoting caused a variable to expand locally when it was intended for the remote side, or vice versa.

### Example

```bash
Log a redacted `%q` representation of SSH arguments and create a minimal test showing each parse layer.
```

### L3 follow-up discussion

Pass values as positional parameters to `bash -s --` or use a copied script with a structured input file.

### Production caution

Nested quote construction becomes unreviewable quickly and is a strong signal to redesign.

---

## Q22. A cleanup trap changes the script's exit code to zero.

### Detailed answer

The trap executed a successful command after the original failure and exited implicitly, or explicitly returned zero. `$?` was not captured immediately.

### Example

```bash
Add trace around the EXIT trap and reproduce a failing main command plus cleanup.
```

### L3 follow-up discussion

Capture `local rc=$?` first, run best-effort cleanup under `set +e`, then `exit "$rc"`.

### Production caution

Cleanup errors should be logged separately without hiding the primary failure.

---

## Q23. A Bash array command works until an optional argument is empty.

### Detailed answer

The script may append a flag and empty value incorrectly, use unquoted `${array[*]}`, or rely on a variable that disappears under nounset. Empty string is a valid argument and differs from no argument.

### Example

```bash
Use a stub that records NUL-delimited arguments and test empty, unset, and absent cases.
```

### L3 follow-up discussion

Append optional pairs only when enabled and execute with `"${args[@]}"`.

### Production caution

Do not flatten arrays into strings for logging and then execute the log representation.

---

## Q24. A disk-cleanup script is too slow on millions of files.

### Detailed answer

The script may spawn external commands per file, sort unnecessarily, traverse extra filesystems, or perform repeated metadata calls. Bash loop overhead becomes dominant.

### Example

```bash
Profile with time/strace, count processes, inspect filesystem I/O, and test `find` batching or one awk/perl/python process.
```

### L3 follow-up discussion

Use `find` predicates and `-exec ... +`, NUL-delimited batching, one-pass processing, and consider a more suitable language.

### Production caution

Performance optimization must retain path safety and deletion validation.

---

## Q25. A script works on RHEL but fails on macOS.

### Detailed answer

It probably relies on GNU-specific options, newer Bash features, `/bin/bash` version, `readlink -f`, `sed -i`, `date -d`, or `stat -c`.

### Example

```bash
Run lint with portability checks, capture tool versions, and test in a macOS CI runner.
```

### L3 follow-up discussion

Define supported platforms, use compatible command forms or capability adapters, or ship a known runtime/container.

### Production caution

Do not claim portability after changing only the shebang.

---

## Recommended references

- GNU Bash Reference Manual
- ShellCheck documentation
- Linux operational command manuals

---

<!-- Original file: 15. Command and Coding Interview Lab.md -->

# Corporate-Level Bash Scripting L3 — Command and Coding Interview Lab

> **Baseline:** GNU Bash 5.x concepts  
> Run destructive examples only in a disposable lab.

## 1. Strict mode and error tracing

```bash
#!/usr/bin/env bash
set -Eeuo pipefail
IFS=$'\n\t'

trap 'rc=$?; printf "ERROR rc=%d file=%q line=%d function=%q command=%q\n" \
  "$rc" "${BASH_SOURCE[0]}" "$LINENO" "${FUNCNAME[0]:-main}" "$BASH_COMMAND" >&2' ERR
```

**Interview expectation:** Explain every option, the contexts where `errexit` is suppressed, why `pipefail` matters, and why an ERR trap does not replace explicit error handling.

## 2. Safe argument forwarding

```bash
wrapper() {
  command_to_run "$@"
}
```

**Interview expectation:** Explain why `"$@"` preserves argument boundaries and why `$*`, `$@`, or `"$*"` are usually wrong for forwarding.

## 3. Dynamic commands with arrays

```bash
args=(rsync -a)
[[ ${DRY_RUN:-false} == true ]] && args+=(--dry-run)
args+=(-- "$source/" "$destination/")

printf 'Executing: '
printf '%q ' "${args[@]}"
printf '\n'

"${args[@]}"
```

**Interview expectation:** No `eval`, no string-built command, correct handling of spaces, wildcards, empty values, and leading dashes.

## 4. Safe line reading

```bash
while IFS= read -r line || [[ -n $line ]]; do
  printf '%s\n' "$line"
done <"$input"
```

**Interview expectation:** Explain `IFS=`, `-r`, the unterminated final line, and why `for line in $(cat file)` is unsafe.

## 5. Arbitrary filenames

```bash
find "$root" -type f -print0 |
while IFS= read -r -d '' file; do
  printf '%q\n' "$file"
done
```

**Interview expectation:** NUL is the only byte forbidden in a Unix pathname. Newline-delimited filename processing is not fully general.

## 6. Atomic configuration update

```bash
target=/etc/myapp.conf
tmp=$(mktemp --tmpdir="$(dirname -- "$target")" ".${target##*/}.XXXXXX") || exit 1
trap 'rm -f -- "$tmp"' EXIT

generate_config >"$tmp"
myapp --check-config "$tmp"
chmod --reference="$target" "$tmp" 2>/dev/null || chmod 0640 "$tmp"
mv -f -- "$tmp" "$target"
trap - EXIT
```

**Interview expectation:** Same-filesystem rename, validation before publish, metadata, cleanup, and the difference between atomic visibility and crash durability.

## 7. Locking with `flock`

```bash
exec {lock_fd}>/run/lock/myjob.lock
if ! flock -n "$lock_fd"; then
  printf 'Another instance is active\n' >&2
  exit 75
fi
```

**Interview expectation:** Kernel-backed lock lifetime, file descriptor ownership, bounded waiting, and why existence of a pidfile is not a reliable lock.

## 8. Cleanup preserving exit status

```bash
cleanup() {
  local rc=$?
  set +e
  [[ -n ${tmp_dir:-} && -d $tmp_dir ]] && rm -rf -- "$tmp_dir"
  exit "$rc"
}
trap cleanup EXIT
```

**Interview expectation:** Capture `$?` first, tolerate cleanup failure, handle partially initialized resources, and understand that SIGKILL cannot be trapped.

## 9. Pipeline status

```bash
set -o pipefail
producer | transform | tee "$output"
statuses=("${PIPESTATUS[@]}")
printf 'producer=%s transform=%s tee=%s\n' "${statuses[@]}"
```

**Interview expectation:** Capture `PIPESTATUS` immediately, understand SIGPIPE, and distinguish file integrity from command success.

## 10. Bounded retry

```bash
retry() {
  local max=$1 delay=$2
  shift 2

  local attempt rc
  for ((attempt=1; attempt<=max; attempt++)); do
    if "$@"; then
      return 0
    else
      rc=$?
    fi

    (( attempt == max )) && return "$rc"
    sleep "$delay"
    delay=$((delay * 2))
  done
}
```

**Interview expectation:** Retry only transient idempotent work, add jitter/deadline, and do not retry validation or unknown-outcome POST requests blindly.

## 11. Curl with status and body separation

```bash
body=$(mktemp)
code=$(
  curl --silent --show-error \
       --connect-timeout 5 \
       --max-time 30 \
       --output "$body" \
       --write-out '%{http_code}' \
       "$url"
) || exit

case $code in
  200) process_response "$body" ;;
  401|403) printf 'Authentication or authorization failure\n' >&2; exit 77 ;;
  429) printf 'Rate limited\n' >&2; exit 75 ;;
  5??) printf 'Remote service error\n' >&2; exit 75 ;;
  *) printf 'Unexpected HTTP status %s\n' "$code" >&2; exit 1 ;;
esac
```

## 12. JSON construction and parsing

```bash
payload=$(jq -n \
  --arg name "$name" \
  --argjson enabled "$enabled" \
  '{name:$name, enabled:$enabled}')

id=$(jq -er '.id | strings' response.json)
```

**Interview expectation:** Use an encoder, validate type, distinguish missing/null/empty, and never build JSON through string interpolation.

## 13. Secure SSH argument passing

```bash
ssh "$user@$host" bash -s -- "$remote_path" <<'REMOTE'
set -Eeuo pipefail
remote_path=$1
printf 'Path: %q\n' "$remote_path"
REMOTE
```

**Interview expectation:** Explain local parsing, SSH argument transport, remote parsing, host-key verification, timeouts, and per-host status.

## 14. Argument parsing

```bash
usage() {
  printf 'Usage: %s [-n] -f FILE\n' "${0##*/}"
}

dry_run=false
file=

while getopts ':nf:h' opt; do
  case $opt in
    n) dry_run=true ;;
    f) file=$OPTARG ;;
    h) usage; exit 0 ;;
    :) printf 'Option -%s needs a value\n' "$OPTARG" >&2; exit 2 ;;
    \?) printf 'Unknown option -%s\n' "$OPTARG" >&2; exit 2 ;;
  esac
done
shift "$((OPTIND - 1))"

[[ -n $file ]] || { usage >&2; exit 2; }
```

## 15. Safe debug trace

```bash
exec {trace_fd}>>/var/log/mytool.trace
chmod 0600 /var/log/mytool.trace
export BASH_XTRACEFD=$trace_fd
export PS4='+ ${BASH_SOURCE}:${LINENO}:${FUNCNAME[0]} pid=$BASHPID: '

set -x
# non-secret operations
set +x
# secret operations
```

## 16. Test command arguments with a stub

```bash
cat >"$test_bin/tool" <<'STUB'
#!/usr/bin/env bash
printf '%s\0' "$@" >"${ARGS_FILE:?}"
STUB
chmod +x "$test_bin/tool"

PATH="$test_bin:$PATH" ARGS_FILE="$tmp/args" ./script.sh
mapfile -d '' -t args <"$tmp/args"
```

## 17. Commands and patterns that require strong justification

```bash
eval "$text"
bash -c "$untrusted"
rm -rf "$variable"
curl -k "$url"
ssh -o StrictHostKeyChecking=no "$host"
chmod -R 777 "$path"
kill -9 "$pid"
source "$untrusted_config"
for file in $(find ...)
```

For each, the candidate should explain the risk, safer design, prerequisites, and the rare context in which it might be acceptable.

---

<!-- Original file: 16. Mock Interview and Scoring Sheet.md -->

# Corporate-Level Bash Scripting L3 — Mock Interview and Scoring Sheet

## Scoring

Score every response from **0 to 5**:

- **0:** No answer or dangerous recommendation
- **1:** Syntax-only answer
- **2:** Basic correct explanation
- **3:** Correct implementation with common failure handling
- **4:** Production-safe design with security, observability, tests, and rollback
- **5:** Adds portability, scale, incident reasoning, governance, and clear trade-offs

A strong L3 candidate should average **4 or higher** and must not give unsafe answers for deletion, privilege, secrets, concurrency, remote execution, or backup recovery.

## Round 1 — Bash execution model

1. Explain parsing, expansion, splitting, globbing, and quote removal.
2. Explain `set -Eeuo pipefail` and its limitations.
3. Compare executing and sourcing.
4. Explain `"$@"`, `"$*"`, `$@`, and `$*`.
5. Explain command substitution versus process substitution.

## Round 2 — Functions and data

6. How should a function return a string, array, and exit status?
7. Explain dynamic scope and `local`.
8. Build a dynamic command safely.
9. Explain indexed versus associative arrays.
10. Parse arbitrary filenames safely.

## Round 3 — Files and processes

11. Create and clean a secure temporary directory.
12. Replace a configuration atomically.
13. Explain redirection order.
14. Capture all statuses in a pipeline.
15. Run ten tasks with a concurrency limit and collect every status.

## Round 4 — Security

16. Show command injection through `eval` and replace it safely.
17. Protect secrets from xtrace, process listings, and logs.
18. Explain PATH attacks in privileged scripts.
19. Pass untrusted data to a remote Bash script over SSH.
20. Explain symlink races and Bash's limitations.

## Round 5 — Reliability and observability

21. Design bounded retries and timeouts.
22. Preserve original status during cleanup.
23. Implement structured logging and correlation IDs.
24. Expose batch-job metrics safely.
25. Diagnose a script that hangs.

## Round 6 — APIs and system administration

26. Call a JSON API and validate response status/schema.
27. Safely restart and validate a systemd service.
28. Modify configuration idempotently.
29. Explain cron versus systemd timers.
30. Automate LVM extension with safety checks.

## Round 7 — Testing and CI/CD

31. Unit-test a function that calls curl.
32. Test signals and cleanup.
33. Test concurrency and locking.
34. Prevent CI secret leakage.
35. Design immutable artifact promotion and rollback.

## Round 8 — Production scenarios

36. A backup file exists but cannot be restored.
37. A cleanup script deleted `/`.
38. A loop counter disappears after a pipeline.
39. An API timeout creates duplicate resources.
40. A script works on RHEL but fails on macOS.

## Mandatory red flags

Heavily downgrade or reject answers recommending these without strict prerequisites:

- `eval` for normal dynamic command construction.
- Unquoted variable expansion for filenames or arguments.
- `curl -k` or disabled SSH host-key checking in production.
- `chmod -R 777` as a permission fix.
- `rm -rf "$var"` without strong path validation and dry-run controls.
- `kill -9` as the first termination step.
- Parsing `ls` output or using `for x in $(command)` for arbitrary records.
- Embedding passwords or tokens in source code or command lines.
- Unlimited background jobs.
- Retrying non-idempotent requests after unknown outcomes.
- Treating file existence as proof of successful backup.
- Relying only on `set -e` for error handling.
- Building JSON, SQL, YAML, or remote commands through raw string interpolation.

## Candidate answer template

For every scenario, structure the answer as:

1. **Mechanism**
2. **Impact and scope**
3. **Evidence**
4. **Likely causes**
5. **Safe remediation**
6. **Rollback or recovery**
7. **Validation**
8. **Prevention and testing**
