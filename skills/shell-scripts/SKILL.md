---
name: shell-scripts
description: Write, review, debug, or refactor shell scripts — including POSIX sh, bash, zsh, and related formats. Use this skill whenever the user asks to create a .sh file, write a shell function, debug a script error, check POSIX compliance, review shebang lines, handle environment variables in shell, or produce cross-platform shell code. Also trigger for questions about shell quoting, variable expansion, command substitution, exit codes, and script portability across ash/dash/bash/zsh/GNU/Linux/BSD/macOS/BusyBox.
---

# Shell Scripts

Apply these rules whenever writing or reviewing shell script files.

**Default target: maximum portability** — scripts must work on GNU/Linux, BSDs (FreeBSD, OpenBSD, NetBSD), macOS, and BusyBox/Alpine without modification, unless the user explicitly restricts the target platform.

## Cross-Platform Portability

Scripts must work on all of these without modification unless a target is explicitly narrowed:

| Platform | Shell | Notes |
|---|---|---|
| GNU/Linux (glibc) | dash or bash as `sh` | GNU coreutils; `date`, `sed`, `awk` are GNU-flavored |
| Alpine / BusyBox | ash | Minimal builtins; no GNU extensions |
| macOS | bash 3.2 or zsh as `sh` | BSD coreutils; `sed`, `date`, `stat` differ from GNU |
| FreeBSD / OpenBSD / NetBSD | sh (pdksh/ash variant) | BSD coreutils; POSIX-strict |

### Coreutils gotchas

Many commands have incompatible flags between GNU and BSD. Always use the portable form:

**`sed`**
```sh
# GNU: sed -i 's/a/b/' file
# BSD: sed -i '' 's/a/b/' file
# Portable: use a temp file
sed 's/a/b/' "$file" > "$file.tmp" && mv "$file.tmp" "$file"
```

**`date`**
```sh
# Only use format flags common to both GNU and BSD
date '+%Y-%m-%d'   # safe everywhere
# Avoid: date -d (GNU-only), date -v (BSD-only)
```

**`stat`**
```sh
# GNU: stat -c '%s' file
# BSD/macOS: stat -f '%z' file
# Portable alternative:
wc -c < "$file" | tr -d ' '
```

**`readlink -f`**
```sh
# GNU: readlink -f resolves full canonical path
# macOS/BSD: readlink -f may not exist
# Portable replacement:
_realpath() {
  ( cd "$(dirname "$1")" && printf '%s/%s\n' "$(pwd -P)" "$(basename "$1")" )
}
```

**`grep`**
```sh
# Avoid GNU-only: -P (PCRE), --color in scripts
# Safe flags everywhere: -E (ERE), -F (fixed), -r, -l, -n, -q, -v
grep -E 'pattern' file
grep -F 'literal string' file
```

**`xargs`**
```sh
# GNU: xargs -r skips empty stdin
# BSD/macOS: no -r flag
# Portable: filter before piping, or accept a no-op call
```

**`mktemp`**
```sh
# Available on GNU/Linux, BSD, macOS, BusyBox
tmpfile="$(mktemp)"
tmpdir="$(mktemp -d)"
trap 'rm -rf "$tmpfile" "$tmpdir"' EXIT
```

**`cp`**
```sh
# Portable flag: -p (preserve mode/timestamps)
cp -p "$src" "$dst"
# Avoid: cp -a, cp --preserve (BSD behavior differs)
```

### awk over sed for non-trivial transforms

`awk` is more portable than `sed` for multi-line or complex edits. Avoid `gawk`-only extensions (`gensub`, `PROCINFO`, etc.):
```sh
awk -F: '{print $2}' /etc/passwd   # extract field 2
```

### PATH assumptions

- Never hardcode `/usr/local/bin`, `/opt/homebrew/bin`, or distribution-specific prefixes.
- Use `command -v` to locate tools at runtime.
- Homebrew-installed tools may not be in PATH in non-interactive shells; do not assume they shadow system tools.

### `/bin/sh` is not bash

| Distro/OS | `/bin/sh` |
|---|---|
| Debian / Ubuntu | dash |
| Alpine / BusyBox | ash |
| macOS | bash (legacy mode, effectively POSIX) |
| FreeBSD | sh (FreeBSD sh) |

Write for the lowest common denominator: POSIX sh with ash/dash constraints.

## Shebang

| Target | Shebang |
|---|---|
| POSIX portable | `#!/usr/bin/env sh` |
| Bash-specific | `#!/usr/bin/env bash` |
| Zsh-specific | `#!/usr/bin/env zsh` |

Default to `#!/usr/bin/env sh` unless the user explicitly requires bash/zsh features.

## POSIX Portability (sh scripts)

Avoid bashisms unless targeting bash explicitly:

| Avoid (bash-only) | Use instead (POSIX) |
|---|---|
| `[[ ... ]]` | `[ ... ]` |
| `$(( ))` arithmetic with `let` | `$(( ))` alone is fine |
| `local var` outside functions | `local` is OK inside functions in most sh implementations; if strict POSIX is needed, avoid it |
| `declare`, `typeset` | not available in POSIX sh |
| `source file` | `. file` |
| `echo -e` | `printf` |
| `&>>`, `<<<` | use `>>` and pipes |
| `${var,,}`, `${var^^}` | `tr` or `awk` |
| `read -r -d ''` | POSIX `read` is line-oriented |

Test across platforms using Docker before shipping:
```sh
docker run --rm -v "$PWD:/w" -w /w alpine sh script.sh          # ash (BusyBox)
docker run --rm -v "$PWD:/w" -w /w debian sh script.sh          # dash (Debian)
docker run --rm -v "$PWD:/w" -w /w ubuntu sh script.sh          # dash (Ubuntu)
docker run --rm -v "$PWD:/w" -w /w bash:3.2 sh script.sh        # bash 3.2 (macOS era)
docker run --rm -v "$PWD:/w" -w /w freebsd/freebsd-13 sh script.sh  # FreeBSD sh (if available)
```

## Quoting

Always double-quote variable expansions unless word-splitting or globbing is intentional:

```sh
# Good
echo "$name"
cp "$src" "$dst"
[ -f "$path" ]

# Bad — breaks on spaces
echo $name
cp $src $dst
```

Leave unquoted only when you need glob expansion or multiple-word splitting:
```sh
# Intentional glob
for f in $pattern; do ...
```

## Variable Conventions

```sh
# Declare and export separately
XDG_CONFIG_HOME="${XDG_CONFIG_HOME:-$HOME/.config}"
export XDG_CONFIG_HOME

# Default values
name="${NAME:-default}"

# Readonly constants
readonly SCRIPT_DIR
SCRIPT_DIR="$(cd "$(dirname "$0")" && pwd)"

# Clean up temp vars at end of script (not exported, not needed downstream)
unset _tmp_var _another_tmp
```

## Conditionals and Tests

```sh
# File/directory tests
[ -f "$file" ]       # regular file exists
[ -d "$dir" ]        # directory exists
[ -e "$path" ]       # any file exists
[ ! -d "$dir" ]      # directory does not exist
[ -r "$file" ]       # readable
[ -x "$cmd" ]        # executable

# String tests
[ -z "$var" ]        # empty string
[ -n "$var" ]        # non-empty string
[ "$a" = "$b" ]      # string equality (= not ==)

# Numeric tests
[ "$n" -eq 0 ]
[ "$n" -gt 1 ]
```

## Command Existence Checks

```sh
# Portable way to check if a command is available
if command -v curl >/dev/null 2>&1; then
  curl ...
else
  echo "curl not found" >&2
  exit 1
fi
```

Never use `which` — it is not POSIX and behaves inconsistently across systems.

## Error Handling

```sh
# Fail fast
set -eu

# (Optional) propagate errors through pipes
# Note: not in POSIX sh, but available in bash/zsh
set -o pipefail   # bash/zsh only

# Manual error check when set -e is not enough
some_command || { echo "failed" >&2; exit 1; }

# Cleanup on exit
_cleanup() {
  rm -f "$tmpfile"
}
trap _cleanup EXIT
```

Prefer `set -eu` at the top of every script unless the script explicitly handles errors inline.

## Output Conventions

```sh
# User-facing messages → stdout
echo "Done."

# Errors and warnings → stderr
echo "error: file not found: $path" >&2

# Prefer printf for portability and formatting
printf '%s\n' "$value"
printf 'count: %d\n' "$n"
```

Do not use `echo -e` or `echo -n` in POSIX sh — use `printf`.

## Functions

```sh
# Name functions with underscores; prefix with _ if private
_print_usage() {
  printf 'Usage: %s [options]\n' "$0"
}

main() {
  _print_usage
}

main "$@"
```

Calling `main "$@"` at the bottom is the standard pattern — it makes scripts sourceable without auto-executing.

## Here-docs and Multiline Strings

```sh
# Indented heredoc (dash/bash; strip leading tabs with <<-)
cat <<-EOF
	line one
	line two
EOF

# Non-indented
cat <<'EOF'
literal $dollar signs not expanded
EOF
```

Use `<<'EOF'` when the content should not expand variables.

## Formatting

Format all shell scripts with `shfmt` before committing:

```sh
shfmt -i 2 -ci -s script.sh        # 2-space indent, case indent, simplify
shfmt -w -i 2 -ci -s script.sh     # in-place
```

Recommended `shfmt` flags:
- `-i 2` — 2-space indentation
- `-ci` — indent case branches
- `-s` — simplify syntax where possible

## Common Patterns

**Resolve script directory (works with symlinks):**
```sh
readonly SCRIPT_DIR
SCRIPT_DIR="$(cd "$(dirname "$0")" && pwd)"
```

**Require a minimum number of arguments:**
```sh
[ "$#" -ge 1 ] || { echo "usage: $0 <arg>" >&2; exit 1; }
```

**Temp file with cleanup:**
```sh
tmpfile="$(mktemp)"
trap 'rm -f "$tmpfile"' EXIT
```

**Read file line by line:**
```sh
while IFS= read -r line; do
  printf '%s\n' "$line"
done < "$file"
```

**Iterate over arguments:**
```sh
for arg in "$@"; do
  printf 'arg: %s\n' "$arg"
done
```
