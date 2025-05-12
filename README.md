# ee - export and echo

A simple ZSH/Bash function that combines `export` and `echo` into a single command to streamline shell variable management. Optimized for macOS, but should work on most Unix-like systems.

## Problem

Setting and checking environment variables often requires two steps:

```bash
export FOO="some value"
echo $FOO  # To verify
```

This becomes tedious, especially with:
- Long variable names
- UPPERCASE variables
- Variables containing command substitutions

## Solution

The `exp` function exports variables and immediately shows their evaluated values.

## Installation

Add this function to your `~/.zshrc` or `~/.bashrc`:

```bash
ee() {
  # Check if any arguments were provided
  if [ $# -eq 0 ]; then
    echo "Usage: ee NAME=VALUE"
    return 1
  fi

  # Process each argument
  for arg in "$@"; do
    # Check if the argument has the format NAME=VALUE
    if [[ "$arg" =~ ^([A-Za-z_][A-Za-z0-9_]*)=(.*) ]]; then
      varname="${BASH_REMATCH[1]}"
      value="${BASH_REMATCH[2]}"

      # Use eval to handle complex values with quotes, command substitutions, etc.
      eval "export $varname=$value"

      # Get the actual value after evaluation
      eval "echo \"$varname=\$$varname\""
    else
      echo "Invalid format for argument: $arg"
      echo "Expected format: NAME=VALUE"
      return 1
    fi
  done
}
```

Then reload your shell:

```bash
source ~/.zshrc  # or ~/.bashrc
```

## Usage

Basic usage:

```bash
ee VAR="value"
# Output: VAR=value
```

With command substitution:

```bash
ee TIMESTAMP="Generated on $(date)"
# Output: TIMESTAMP=Generated on Mon May 12 12:34:56 PDT 2025
```

Multiple variables:

```bash
ee FOO=bar BAZ="hello world"
# Output:
# FOO=bar
# BAZ=hello world
```

## Notes

- This function is optimized for macOS's version of zsh/bash
- If you encounter issues on Linux or other systems, you might need to adjust the regex handling
