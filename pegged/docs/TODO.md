TODO
====

Parsers
-------

- Change the grammar examples, dropping the now-uneceessary ':'.

- Add a boolean grammar (&&, ||, !) and an comparison grammar( ==, !=, <, >, <=, >=), calling arithmeric expressions. I might need it if I add guards on parametrized rules anyway.

Infrastructure
--------------

- Directly reading a grammar from a file.

- Better error report still: explaining what was expected "rule Digit failed at XXX, expected '0'-'9'"

-  Check for grammar well-formedness. For example: repated subrules that consume no character (`(a?)*`) and indirect left recursion: `A -> "a" / "b"? A`. If there is no 'a', the second branch is tried and if there is no 'b', `A` calls itself again, creating an infinite loop. All in all, that means determining if a rule can succeed while consuming no input (rules Eps, EOI, e?, e*, !e and &e are concerned and also an or branch containing such a construct or a sequence containing *only* such rules). Then emit a warning (how?). Maybe a testGrammar() free function?

- Different levels of parsing and perse-tree generation : `enum TreeLevel { none, standard, full, fullest }`. 'none' means no parse tree generation, only the matches (captures) are recorded. 'standard' is what **Pegged** does right now (with tree decimation, see [[Extended PEG Syntax]] and [[Four Levels of Encapsulation]]), 'full' keeping external rules nodes and 'fullest' disabling all the cutting actions, like `:e` or `~e`. The parsing levels will be template parameter in the `parse` call (`parse!(TreeLevel.fullest)(input)`).

- aliases for the previous parsing levels. For example: `match` for `TreeLevel.none`, `externalParse`, ...

- Multiple semantic actions { foo, bar, baz }

- Some predefined semantic actions: cutChildren, fuseCaptures, dropCaptures

- Default arguments for parametrized rules `List(Elem, Sep = ',') < Elem (Sep Elem)*`.

- 'if' guards for parametrized rules? `List(Elem, Sep) if (Sep == ',')`. Tricky point: extracting `','` to transform it into `Lit!(',')`. That means parsing the guard as a boolean expression, whose terminals must accept PEG expressions or arithmetic expressions.

- Adding direct (non-PEG) arguments to parametrized expressions: `Repeat(Expr, n) if (n > 1) <- Expr Repeat(Expr, n-1)` and `Repeat(Expr, n) if (n == 1) <- Expr`. The tricky part is in the right-hand side: dealing with arbitrary expressions in the argument list, and not only identifiers representing rules. Which sub-grammar should take precedence? PEG or aritmetic? For example `a* b` can parsed as 'some a, followed by a b' or as 'a*b'. Is 'Expr' a rule or a 1-element arithmetic expression? The last one is not problematic, as long as identifiers are then re-encoded as themselves. In any case, terminals such as "abc" and 'a' *must* be seen as PEG expressions.

- Parametrized grammars? Since grammars are parsers (with one publically accessible rule), they could be parametrized, to be generic. For example, a generic boolean grammar, with a parametrized lowest level.

- Make rule names enumerated results to use `final switch` on them

- Grammar optimization: inlining, for example

- parse `wstring`s and `dstring`s

- parse ranges.

- (PARTIALLY DONE) Add unit-tests. I tend to write docs and unittests as I go, D making that so easy. **Pegged** is an exception, which I'll kill with extreme prejudice.

- (PARTIALLY DONE) Explore the possibility to create D code with inner functions instead of inner classes.

Documentation
-------------

- Explain the internal indices (+ line / col).

- Explain grammar composition.

- Explain how to change the **Pegged** grammar.

- Explain the D grammar?
