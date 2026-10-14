# toy-language-compiler

A small compiler written in C++17 that turns a C-like toy language into LLVM IR.
Built as a university compilers course project (phase 1 to final).

## Pipeline

| Stage | File | Notes |
|---|---|---|
| Lexer | `src/lexer.cpp` | hand-written tokenizer |
| Parser | `src/parser.cpp` | recursive descent, produces the AST in `src/ast.h` |
| Optimizer | `src/optimizer.cpp` | unrolls `for` loops with literal bounds, folds `if` with constant conditions |
| Code generator | `src/codegen.cpp` | emits LLVM IR through LLVM's `IRBuilder` |
| Semantic analysis | `src/semantic.cpp` | written but **not wired into `main.cpp`** yet |

The grammar is in `src/grammer.txt`.

## The language

- Types: `int`, `float`, `bool`, `char`, `string`, `array`
- Statements: variable declarations (also `int a, b = 1, 2;`), assignment, `+= -= *= /= %=`, `++ --`, `if / else`, `for`, `foreach`, `match`, `try / catch`, `print`
- Expressions: arithmetic, comparison, `&& || ^`, ternary `?:`, array indexing, `concat`, `abs`, `pow`, `length`, `min`, `max`, element-wise `add / subtract / multiply / divide` on arrays

Example:

```
int s = 0;
for (int i = 0; i < 3; i++) { s += i; }
print(s);
```

## Build and run

Needs LLVM (the makefile expects Homebrew's at `/opt/homebrew/opt/llvm`).

```
cd src
make
./compiler 'int a = 2; int b = 3; print(a + b);' > main.ll
```

Pass the program in **single quotes** (not double quotes). The compiler prints LLVM IR to stdout; run it with `lli main.ll`.

## Known limitations

- Binary operators are parsed left to right with **no operator precedence**: `2 + 3 * 4` is `(2 + 3) * 4`. Use parentheses.
- `+` is addition only when both operands are `int` / `float`; with strings (or variables of unknown type) it is string concatenation.
- No unary minus before parentheses; `foreach` and `try / catch` are only partly supported.
- The semantic analyzer is not run, so type errors are caught late (in code generation) or not at all.
- `tests/test_lexer.cpp` is out of date and does not build against the current lexer.
