## Why have a best practice guide to LaTeX?

LaTeX is hard to parse, to the extent that the only tool that can reliably
work with LaTeX is the LaTeX compiler[1]. But the LaTeX compiler is limited 
because all it lets you do is create an even less machine readable PDF.

Well written LaTeX code is consistent and semantic:
* **Consistent.** Environments and commands obey consistent naming rules with capitalisation and abbreviation.
* **Semantic.** Instead of saying 'make 
this content bold and italicised', we say 'this is a theorem, format it
accordingly'.

If we
could all agree on a universal definition of 'well written', and of naming
conventions for environments, we could start
to reliably parse these well written LaTeX documents. Such a document could be
used outside of just generating a PDFs.

That sounds a bit like defining a standard, which seems ultimately doomed to
failure at this point. Before thinking that far ahead though, it would be useful
to start thinking about what well written LaTeX code should look like. Then if
we all agree to stick to such a best practice, it will make it easier to change
formats by swapping out only your preamble, or to introduce LaTeX newcomers to
ways of including maths, tables, figures, etc that are reliable and not prone to
breaking in odd, hard to debug and class dependant ways.

## Anti-patterns

It's easiest to start by point out some things that you should never do in LaTeX.

### \def

The `\def` command is part of TeX, but should never be used in LaTeX documents.
`\def` works by expanding all of the text that comes after it to match its
definition and is not limited to `{}` and `[]` for arguments.
With `\def`, the following code is possible, but is impossible
to parse by anything that doesn't implement a complete TeX macro processor (which
as far as I'm aware only Donald Knuth has ever managed):

```
\def\BOLD #1 ENDBOLD{\textbf{#1}}
\BOLD Hello world! ENDBOLD
```

### eqnarray

### \bf, etc.

TeX commands affecting the whole group like `{\bf Bold}` should never be used.

Does this rule out the common pattern of putting `\centering` in a figure environment?

### $, $$

Should these be considered bad? I know people claim that they are due to spacing
issues that are fixed by the `\(` and `\[` LaTeX equivalents, but they are so widespread
it might be an uphill battle to suggest never using `$` and `$$` while still trying to
be as much like recognisable LaTeX as possible.

### More

## Macros

Due to the internal token expansion in LaTeX, it is impossible just from inspection how
a macro will behave. In general, macros are of two types (are they?):

1) Output macros, like math symbols, that convert to a character, glyph or
some piece of output in the document. Examples: `\LaTeX`, `\sin`, etc.
2) Modifier macros that modify their arguments in some way: `\textbf{...}`,
`\emph{...}`.

Any macro without explicit `{}` arguments should be assumed to be an output
macro, or 'atomic' unit in parsing, like a letter or symbol. 

For example '\emph ABC' is not valid, even if you only want the 'A' to be
emphasised. It must be explicit: `\emph{A}BC`.

There will probably need to be some reasonable exceptions to this,
like accent commands: `\'`, `\"`, etc, which are frequently used
without `{}`: "Theatr\'e".

To fit this pattern, commands that affect the whole current group, like `\bf`
must be disallowed.

## Figures

Example `figure` environment with a caption above/below, with subfigures (which package and
syntax to use). Are images always `\centered`, or is there a semantic way to
define a figure environment that assumes this. Where does the `\label` go in a figure?
Would a parser need to assert that it was after the caption, like LaTeX needs it to be?

## Math mode

The core TeX math commands are clearly fine, but what about the different amsmath
environments, and other custom includes like `\dif` in the `mathtools` packages
for a roman d for differential forms/integral measures for example?

Custom definitions of operators and functions? Probably need to be defined as an
ok 'best practice' since it is very necessary to extend the available maths macros
for different domains.

## Explicit horizontal and vertical space

(JA and JLM discussed):
Only allowed in math mode, where the TeX representation of an equation is both its formatting 
and semantic representation due to the pervasiveness of TeX as the language of
choice for representing mathematics. For example, putting some space between an integral and the
measure is a common use case that would be hard to do any other way.

(JA): One possible issue to consider: I've seen people create the identity matrix
symbol (bbold 1) by putting a 1 and a lower case L next to each other, with the
L shifted by some negative hspace. It works surprisingly well, but is
neither semantic (good luck getting the meaning of that equation from the TeX) or
a good way of displaying a bbold 1 considering there is a font that does that.

## Tikz

Once you're in a `tikz` environment, you're on your own! Do what you like, we aren't
going to judge you. Tikz is like LaTeX where there is only a single reliable implementation.
We don't feel like taking that on as well as LaTeX!

## Labels and referencing

Recommend the way things are done by biblatex?

## Tables

Urgh... Don't ever use tables in LaTeX? Is that a viable best practice?
Probably not :(.

## Front matter: Abstract, declaration, dedication, TOC, LOF, LOT

Create recommended environment names for `abstract`, `declaration`, and `dedication`. What else?

Should something like an abstract be set at the top as metadata, like author and title?
Then printed in the doc with something like `\makeabstract`?

What about papers with abstracts in two languages? Specify an optional language in either
the environment or command? I.e.

```
\abstract[language=en]{This is my English abstract}
\abstract[language=fr]{This is my French abstract}

\makeabstract[en]
\makeabstract[fr]

% or

\begin{abstract}[language=en]
...
\end{abstract}
```

TOC, LOF and LOT do as LaTeX does already?

## Back matter: Appendix, bibliography, glossary, index

...

## Author (names, emails, affiliations, etc)

There is a lot of potential metadata about authors. Speccing that fully is a daunting
task. (JLM mentioned a package that
deals with this, but I wasn't familiar with it. Can you describe it here?)

## Multiple language and encoding support

...

# Next Steps?

Ideas:

* A simple script that validates first the correct use of command/environment
syntax (correct matching of curly braces, no changing of character codes, no use of raw TeX, etc),
and then that only valid commands/environments are used (i.e. throw and error if
`Abstract` rather than `abstract`.) 


[1] More accurately, the LaTeX classes and packages built on top of the 
TeX compiler.

