# 99 Language Support PRs - Review & Submit Guide

## Status

| # | Language | Branch | Lint | Fmt | Review | Submitted |
|---|----------|--------|------|-----|--------|-----------|
| 1 | JavaScript | `feat/javascript-support` | PASS | PASS | [ ] | [ ] |
| 2 | Bash | `feat/bash-support` | PASS | PASS | [ ] | [ ] |
| 3 | Haskell | `feat/haskell-support` | PASS | PASS | [ ] | [ ] |
| 4 | PowerShell | `feat/powershell-support` | PASS | PASS | [ ] | [ ] |
| 5 | Rust | `feat/rust-support` | PASS | PASS | [ ] | [ ] |

Go already existed upstream - no PR needed.

## Per-Language Details

### 1. JavaScript (`feat/javascript-support`)

**Files:**
- `queries/javascript/99-function.scm`
- `lua/99/language/javascript.lua`
- `lua/99/test/fill_in_function.javascript_spec.lua`
- `lua/99/init.lua` (added "javascript" to languages list)

**Treesitter nodes:** function_declaration, function_expression, arrow_function, method_definition, generator_function, generator_function_declaration

**log_item:** `console.log(%s)`

**Tests:** function declaration + arrow function

---

### 2. Bash (`feat/bash-support`)

**Files:**
- `queries/bash/99-function.scm`
- `lua/99/language/bash.lua`
- `lua/99/test/fill_in_function.bash_spec.lua`
- `lua/99/init.lua` (added "bash" to languages list)
- `lua/99/request-context.lua` (added `sh → bash` filetype mapping)

**Treesitter nodes:** function_definition (covers both `foo() {}` and `function foo() {}` syntax)

**Filetype mapping:** Neovim sets filetype `sh` for shell scripts but treesitter parser is `bash`. Added mapping in request-context.lua alongside existing `typescriptreact → typescript`.

**log_item:** `echo "$%s"`

**Tests:** basic function + function keyword syntax

---

### 3. Haskell (`feat/haskell-support`)

**Files:**
- `queries/haskell/99-function.scm`
- `lua/99/language/haskell.lua`
- `lua/99/test/fill_in_function.haskell_spec.lua`
- `lua/99/init.lua` (added "haskell" to languages list)

**Treesitter nodes:** function (each equation clause)

**Note:** Haskell functions are pattern-matched equations, not block-bodied. The query captures `match` as the body node.

**log_item:** `print %s`

**Tests:** function with pattern matching replacement

---

### 4. PowerShell (`feat/powershell-support`)

**Files:**
- `queries/powershell/99-function.scm`
- `lua/99/language/powershell.lua`
- `lua/99/test/fill_in_function.powershell_spec.lua`
- `lua/99/init.lua` (added "powershell" to languages list)
- `lua/99/request-context.lua` (added `ps1 → powershell` filetype mapping)

**Treesitter nodes:** function_statement

**Filetype mapping:** Neovim sets filetype `ps1` for PowerShell but treesitter parser is `powershell`. Added mapping in request-context.lua.

**log_item:** `Write-Output $%s`

**Tests:** function with param block

---

### 5. Rust (`feat/rust-support`)

**Files:**
- `queries/rust/99-function.scm`
- `lua/99/language/rust.lua`
- `lua/99/test/fill_in_function.rust_spec.lua`
- `lua/99/init.lua` (added "rust" to languages list)

**Treesitter nodes:** function_item (free functions + impl methods), closure_expression

**log_item:** `println!("{:?}", %s)`

**Tests:** free function + impl method

---

## How to Review Locally

```bash
cd ~/Code/99

# Check out a branch
git checkout feat/javascript-support

# Run full PR check
make pr_ready

# Or individually:
luacheck lua/ --globals vim
stylua lua/ --config-path=.stylua.toml --check
```

## How to Submit PRs to Upstream

After review, create PRs against `ThePrimeagen/99:master`:

```bash
cd ~/Code/99

# Example for JavaScript
git checkout feat/javascript-support
gh pr create --repo ThePrimeagen/99 \
  --title "feat: add JavaScript language support" \
  --body "$(cat <<'EOF'
## Summary
- Add treesitter function detection queries for JavaScript
- Support function declarations, arrow functions, method definitions, and generators
- Add language config with console.log for log_item
- Add fill_in_function tests

## Files Changed
- `queries/javascript/99-function.scm` (new)
- `lua/99/language/javascript.lua` (new)
- `lua/99/test/fill_in_function.javascript_spec.lua` (new)
- `lua/99/init.lua` (add javascript to languages list)
EOF
)"
```

Repeat for each branch, adjusting title/body per language.

## Conflict Note

Each branch independently modifies `lua/99/init.lua` (the languages list). They won't conflict with upstream but will conflict with each other if merged sequentially. Each PR should be rebased after the previous one merges.

Bash and PowerShell also modify `lua/99/request-context.lua` for filetype mappings. These two will conflict with each other - whoever merges second needs a rebase.

## Local Setup Context

The local nvim config at `~/.config/nvim/lua/plugins/4-dev.lua` uses a custom OpenRouter provider (`~/.config/nvim/lua/custom/ollama-provider.lua`) with wrapper script at `~/.local/bin/openrouter-99` that calls Codestral via OpenRouter API. This is separate from these upstream PRs.
