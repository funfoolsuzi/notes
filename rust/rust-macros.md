# Rust Macros

## Macro rules

[The Little Book of Rust Macros](https://danielkeep.github.io/tlborm/book/index.html) (tlborm) is way out-dated, but I'd say this is the most comprehensive book about Rust macros/syntax-extensions. The down side is it doesn't cover _procedural macros_, which has not existed at the time of writing. The reason why official Rust references only covers procedural macros is because this book does such a good job there is no reason to do it in the official docs.

My goal is to be able to use proc macros to create my own attributes. But because that tlborm is the prerequisite.
Key points:

1. Rust macros use AST(Abstract Syntax Tree) to realize this so called _syntax extension_. In another word, "Macros (really, syntax extensions in general) are parsed as part of the abstract syntax tree.".
2. Rust compiler expands macros recursively([read more here](https://danielkeep.github.io/tlborm/book/mbe-syn-expansion.html)). It means, each time the compiler expand all the macros it can evaluate but doesn't evaluate macros that are used inside of these first layer of macros. So it may expose new macros every time it finishes expanding the current layer. It goes down levels of macro tree one level at a time. And the book also mentions by default compiler gives up at 32nd time of recursion.
3. `macro_rules` to define macro.
4. macro rules are __matched__ by _pattern_s.

    ```txt
    ($pattern) => {$expansion}
    ```

5. pattern can contain __captures__ to capture input into variable during the expansion.
6. _captures_ can achieve repetitions by following rule pattern syntax:

    ```rust
    (
        // Start a repetition:
        $(
            // Each repeat must contain an expression...
            $element:expr
        )
        // ...separated by commas...
        ,
        // ...zero or more times.
        *
    ) => {
        // Enclose the expansion in a block so that we can use
        // multiple statements.
        {
            let mut v = Vec::new();

            // Start a repetition:
            $(
                // Each repeat will contain the following statement, with
                // $element replaced with the corresponding expression.
                v.push(format!("{}", $element));
            )*

            v
        }
    };
    ```

## Procedural Macros

### Key points

- Instead of working on AST token directly from `macro_rule!`, proc macro begins with TokenStream, which is a list of TokenTree.
- Able to use template code directly and parse them into TokenStream.
- Three kinds of proc macro:

  - function-like macros(proc_macro, (TokenStream) -> TokenStream))
  - derive mode macros(proc_macro_derive, (TokenStream) -> TokenStream))
  - attribute macros(proc_macro_attribute, (TokenStream, TokenStream) -> TokenStream))

- Need a tweek in `Cargo.toml`

```toml
[lib]
proc-macro = true
```

- Maybe it can check syntax from other languages in IDE with RLS. For example, `let sql = sql!(SELECT * FROM posts WHERE id=1);`. It would be great it can check sql syntax in IDE, right? But it might be too much work for RLS. I had RLS crushed a few times today because of proc macro
[proc macros in rust reference](https://doc.rust-lang.org/reference/procedural-macros.html)
[proc macros in rust blog](https://blog.rust-lang.org/2018/12/21/Procedural-Macros-in-Rust-2018.html)
[proc-macro crate](https://doc.rust-lang.org/proc_macro/index.html)
It is quite different with what I thought proc macros were two days ago. It really looks like something fun to play with.

Another thing about Rust, mostly about cargo:
[creating cargo workspace](https://doc.rust-lang.org/book/ch14-03-cargo-workspaces.html#cargo-workspaces)
