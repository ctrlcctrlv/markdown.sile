# SILE and Markdown

::: {custom-style=raggedleft}
"Markdown is intended to be as easy-to-read and easy-to-write as is feasible."^[From the
original [Markdown syntax specification](https://daringfireball.net/projects/markdown/syntax).]
:::

While the original Markdown format was indeed quite simple, it quickly became a landmark for
documentation, especially technical. Several variants then emerged. Amongst other solutions, the Pandoc
converter started supporting a nice set of interesting extensions for lists, footnotes, tables,
etc.^[See [IETF RFC 7764, section 3.3](https://datatracker.ietf.org/doc/html/rfc7764#section-3.3).]
---So that nowadays, Markdown, enriched with these extensions, may be quite appealing to writers and
authors alike.

Within SILE’s aim to produce beautiful printed documents, it's a pretty reasonable assumption that
such writers and authors may want to use this fine typesetter with their Markdown content, without
having to learn the SILE language and its specifics (but not, either, fully excluding it for some
advanced capabilities).

There is actually more than one solution to achieve great results in that direction:

 1. Directly using a native Markdown converter package,
 1. Using the Pandoc software to generate an ouput suitable for SILE.

Each of them has its advantages, and a few limitations as well.

[comment]: # (THIS TEXT SHOULD NOT APPEAR IN THE OUTPUT. It is actually the most platform independent comment in Mardown.)

## The native markdown package

Once you have loaded the `\autodoc:package{markdown}`{=sile} package,
the `\autodoc:command{\include[src=<file>]}`{=sile} command supports reading a Markdown file[^other-ways].
The speedy Markdown parsing relies on John MacFarlane's excellent **lunamark** Lua library.

[^other-ways]: The astute reader already knows, from reading the SILE manual, that there are other ways
(e.g. with command-line options) to tell SILE how to process a file in some format --- so we just stick
to the basics here.

```
\use[module=packages.markdown]
\include[src=somefile.md]
```

Including raw Markdown content from within a SILE document is also possible:

```
\begin[type=markdown]{raw}
Some Markdown content
\end{raw}
```

Guess what, the very chapter you are currently reading is written in Markdown!

### Basic typesetting {#basic-typesetting}

As it can be seen here, sectioning obviously works^[With a small caveat. The package maps heading
levels to `\chapter`, `\section`, `\subsection`, `\subsubsection` and uses a very basic fallback
if these are not found (or if the sectioning level gets past that point). The implication, therefore,
is that the class or other packages have to provide the relevant implementations.] and paragraphs
are of course supported.
As of formatting, *italic*, **bold**, and `code` all work as expected.

Three dashes on a standalone line (as well as asterisks or underscores)
produce an horizontal rule.

***

Hard line breaks...\
... are supported too, either using the standard "invisible" method from Markdown (i.e. two trailing
spaces at the end of a line) or a backslash-escaped newline (i.e. a backslash occurring at the
end of a line, as in the corresponding default Pandoc extension).

Several Pandoc-like extensions to Markdown are also supported.
Notably, the converter comes by default with smart typography enabled: three dashes (`---`) in an
inline text sequence are converted to an em-dash (---), two dashes (`--`)
to an en-dash useful for ranges (ex., "it's at pages 12--14"), and three dots
(`...`) to an ellipsis (...)

By the way, note, from the above example, that smart quotes and apostrophes are also automatically handled.

Likewise, superscripts and subscripts are available : H~2~O is a liquid, 2^10^ is 1024. This was
obtained with `H~2~O` and `2^10^` respectively.

Other nice features include:

 - ~~deletions~~ with `~~deletions~~`
 - [underlines]{.underline} with `[underlines]{.underline}`
 -  and even [Small Caps]{.smallcaps}, as `[Small Caps]{.smallcaps}`

The two latter cases use the extended Pandoc-inspired "span" syntax, which is also useful for languages
and custom styles (see futher below). They also use the CSS-like class notation that several
Pandoc writers recognize.

### Lists

Unordered lists (a.k.a. itemized or bullet lists) are obviously supported, or
we would not have been able to use them in the previous section.

Ordered lists are supported as well, and also accept some of the "fancy lists" features
from Pandoc. The starting number is honored, and you have the flexibility to use
digits, roman numbers or letters (in upper or lower case).
The end delimiter, besides the standard period, can also be a closing parenthesis.

 b. This list uses lowercase letters and starts at 2. Er... at "b", that is.
     i) Roman number...
     i) ... followed by a right parenthesis rather than a period.

By the way,

 1. Nesting...

    ... works as intended.

     - Fruits
        - Apple
        - Orange
     - Vegetables
        - Carrot
        - Potato

    And that's all about regular lists.

Task lists following the GitHub-Flavored Markdown (GFM) format are supported too:

 - [ ] Unchecked item
 - [x] Checked item

Definition lists^[As in Pandoc, using the syntax of PHP Markdown Extra with some
extensions.] are also decently supported:

apples
  : Good for making applesauce.

citrus
  : Like oranges but yellow.

### Block quotes

> Block quotes are written like so.
>
> > They can be nested.

There's a small catch here. If your class or previously loaded packages provide
a `blockquote` environment, it will be used. Otherwise, the converter uses its
own fallback method.

### Links and footnotes

Here is a link to [the SILE website](https://sile-typesetter.org/).
It might not be visible in the PDF output, but hover it and click. It just works.
Likewise, here is an internal link to the "[Basic typesetting](#basic-typesetting)" section.

Here is a footnote call[^1].

[^1]: And here is some footnote text. But there were already a few foonotes earlier in this
document. Let's just add, as you can check in the source, that the converter supports several
syntaxes for footnotes.

### Languages

Language changes within the text are supported, either around "div" blocks or inline
"span" elements (both of those are Pandoc-like extensions to standard Markdown).
It is not much visible below, obviously, but the language setting
affects the hyphenation and other properties. In the case of French, for instance,
you can see the special thin space before the exclamation point, or the internal
spacing around quoted text:

::: {lang=fr}
> Cette citation est en français!
:::

Or inline in text: [«Encore du français!»]{lang=fr}

This was obtained with:

```
::: {lang=fr}
> Cette citation est en français!
:::

Or inline in text: [«Encore du français!»]{lang=fr}
```

Smart typography takes the current language into account when converting straight double and single
quotation marks to the appropriate typographic variant:
["English"]{lang=en} / ["Deutsch"]{lang=de} / ["français"]{lang=fr} / ["dansk"]{lang=da} / ["русский"]{lang=ru};
['English']{lang=en} / ['Deutsch']{lang=de} / ['français']{lang=fr} / ['dansk']{lang=da} / ['русский']{lang=ru}.^[For
most languages, `"` and `'` correspond to the primary and secondary quotations marks, respectively.
In some languages, they are used the other way round, but obviously the user's input is respected in those cases
(e.g. respectively ["Ghàidhlig"]{lang=cy} and ['Ghàidhlig']{lang=cy}).]

### Custom styles

On the "div" and "span" extended elements, the converter also supports the `{custom-style="..."}`
attribute.
This is, as a matter of fact, the same syntax as proposed by Pandoc, for instance, in
its **docx** writer, to specify a specific, possibly user-defined, custom style name (in that
case, a Word style, obviously). So yes, if you had a Pandoc-Markdown document styled for Word,
you might consider that switching to SILE is a viable option!

If such a named style exists, it is applied. Erm. What does it mean? Well, in the default
implementation, if there is a corresponding SILE command by that name, the converter invokes
it. Otherwise, it just ignores the style and processes the content as-is.
It thus allows you to use some interesting SILE features. For instance, here is some block
of text marked as "center":

::: {custom-style="center"}
This is SILE at its best.
:::

And some inline [message]{custom-style="strong"}, marked as "strong". That's a fairly
contrived way to obtain a bold text, but you get the underlying general idea.

This logic is implemented in the `\autodoc:command{\markdown:custom-style:hook}`{=sile}
command. Package or class designers may override this hook to support any
other styling mechanism they may have or want. But basically, this is one of the
ways to use SILE commands in Markdown. While you could invoke _any_ SILE command with
this feature, we recommend, though, to restrict it to styling. You will see, further below,
another more powerful way to leverage Markdown with SILE’s full processing capabilities.

### Images

Here is an image: ![](./gutenberg.png "An exemplary image"){width=2.5cm}

![](./examples/manicule.svg){height=0.9em} SVG is supported too.

You can specify the required image width and/or height, as done just above actually,
by appending the `{width=... height=...}` attributes^[And possibly other attributes,
they are all passed through to the underlying SILE package.] after the usual Markdown
image syntax ---Note that any unit system supported by SILE is accepted.

### Tables

The converter only supports the PHP-like "pipe table" syntax at this point, with an optional
caption.

| Right | Left | Default | Center |
|------:|:-----|---------|:------:|
|  12   |  12  |    12   |    12  |
|  123  |  123 |   123   |   123  |

  : Demonstration of a pipe table.

Regarding captioned tables, there's again a catch. If your class or previously loaded packages
provide a `captioned-table` environment, it will be wrapped around the table (and it is then assumed to
take care of a `\caption` content, i.e. to extract and display it appropriately).  Otherwise,
the converter uses its own fallback method.

### Line blocks

So called "line blocks", a sequence of lines beginning with a vertical bar (`|`) and followed by a
space, are also supported. The division into lines is preserved in the output. Any additional leading space
is preserved too, interpretated as an em-quad. These blocks can be useful for typesetting addresses
or poetry.

::: { custom-style=em }
| Long is one night, long is the
  next;
|  how can I bear three?
| A month has often seemed less to me
|  than this half night of longing.
:::

This implementation goes a bit beyond the standard Pandoc-inspired support for line blocks.
In particular, empty lines (i.e. starting with a vertical bar and a single space, but no other content
afterwards) are interpretated as stanza separators, which should be smaller than an empty line
(i.e. a small skip, by default).

::: { .poetry lang=fr step=4 }
| En hiver la terre pleure ;
| Le soleil froid, pâle et doux,
| Vient tard, et part de bonne heure,
| Ennuyé du rendez-vous.
| 
| Leurs idylles sont moroses.
| — Soleil ! aimons ! — Essayons.
| Ô terre, où donc sont tes roses ?
| — Astre, où donc sont tes rayons ?
| 
| Il prend un prétexte, grêle,
| Vent, nuage noir ou blanc,
| Et dit : — C’est la nuit, ma belle ! —
| Et la fait en s’en allant ;
| 
| Comme un amant qui retire
| Chaque jour son cœur du nœud,
| Et, ne sachant plus que dire,
| S’en va le plus tôt qu’il peut.
:::

Moreover, there's once again a nice catch. If your class or previously loaded packages
provide a `poetry` environment, and you set the `.poetry` class specifier on a "div"
element just around the line blocks, then this environment will be used instead of
the default one provided by the converter. It is assumed to support the same
features and options ---namely, `numbering` (boolean, true by default)^[For consistency
with headers, the `.unnumbered` class specifier is also supported.], `start` and `step`
(integers) and `first` (boolean, false by default)--- as the `\autodoc:package{resilient.poetry}`{=sile}
3rd-party package. For instance:

~~~
:::{ .poetry step=4 first=true }
| Some verses...
| Other verses...
```

:::
~~~

### Code blocks

Verbatim code and "fenced code blocks" work:

```lua
function fib (n)
  -- Fibonacci numbers
  if n < 2 then return 1 end
  return fib(n - 2) + fib(n - 1)
end
```

As show above, code blocks marked as being in Lua (either with the `lua` information string or the `.lua` class
specifier) are syntax-highlighted. This is a naive approach, until the converter possibly supports a more general
solution.

### Raw blocks

Last but not least, the converter supports a `{=sile}` annotation on code blocks, to pass
through their content in SILE language, as shown below.[^raw-comment]

[^raw-comment]: This is also a Pandoc-inspired extension to standard Markdown. Other `{=xxx}` annotations
than those described in this section are skipped (i.e. their whole content is ignored).
`That's a \LaTeX{} construct, so it's skipped in the SILE output.`{=latex}

```{=sile}
For instance, this \em{entire} sentence is typeset in a \em{raw block}, in SILE language.
```

Likewise, this is available on inline code elements: `\em{idem.}`{=sile}

This was obtained with:

~~~
```{=sile}
For instance, this \em{entire} sentence is typeset in a \em{raw block}, in SILE language.
```

Likewise, this is available on inline code elements: `\em{idem.}`{=sile}
~~~


It also supports `{=sile-lua}` to pass Lua code.
This is just a convenience compared to the preceding one, but it allows you to exactly
type the content as if it was in a code block (i.e. without having to bother wrapping
it in a script).

```{=sile-lua}
SILE.call("em", {}, { 'This' })
SILE.typesetter:typeset(" is called from Lua.")
```

This was generated by:

~~~
```{=sile-lua}
SILE.call("em", {}, { 'This' })
SILE.typesetter:typeset(" is called from Lua.")
```
~~~

You now have the best of two worlds in your hands, bridging together Markdown and SILE
so that you can achieve wonderful things, we have no idea of. Surprise us!

## The Pandoc-based converters

Pandoc is a free-software document converter, created by the same John MacFarlane who
provided the **lunamark** library which empowers SILE’s native markdown package.
The latter, though, does not offer as many options and extensions as Pandoc does,
for advanced typesetting.

In the event where the native solution would fall short for you ---e.g. would you need some extension
it doesn't yet support--- you may want to use Pandoc directly for converting your document to an
output suitable for SILE.

The following solutions are still experimental proof-of-concepts, but you may give them a chance,
and help us fill the gaps.

### Using Pandoc's AST with the pandocast package

The experimental `\autodoc:package{pandocast}`{=sile} package allows you to use Pandoc’s JSON AST
as an input format for documents.
You can obtain an AST output from Pandoc for any supported source format. Keeping the focus on
Markdown here:

```
pandoc -t json somefile.md -f markdown -o somefile.pandoc
```

Once the package is loaded, the `\autodoc:command{\include[src=<file>]}`{=sile} command supports
reading and processing such a Pandoc AST file, assuming the `.pandoc` extension or specifying
the `format=pandocast` parameter:

```
\use[module=packages.pandocast]
\include[src=somefile.pandoc]
```

This package supports quite the same advanced features as the native markdown package, e.g. the
ability to use custom styles, to pass native content through to SILE, etc.

There is a small _caveat_, though: one must use a version of Pandoc which generates
an AST compatible with our parser ("inputter"). While the Pandoc AST is somewhat stable,
it may change when new features are introduced in the software.

### Other Pandoc-based solutions

#### Using a Pandoc SILE writer and the pandoc package {-}

There is no official SILE writer for Pandoc yet, but some efforts have been done in that direction.
It therefore requires installing a Pandoc fork^[<https://github.com/alerque/pandoc/commits/sile4>],
which is not merged upstream at this date.

You then need to use the SILE `\autodoc:package{pandoc}`{=sile} package, which provides the
required command mappings between the Pandoc-to-SILE writer and the rest of the software.

#### Using a Pandoc "custom writer" in Lua {-}

Pandoc also supports "custom writers" developed in Lua^[<https://pandoc.org/custom-writers.html>].

This custom writer API is fairly recent and might change. Actually, besides a "Classic style" API,
there's now also a "New style" API...  While such custom writers may have some rough edges, the idea
is quite appealing nevertheless. After all, SILE is mostly written in Lua, so the skills are there
in the community.

Again, there is no official solution using this conversion path, but some pretty neat
experimental results have been
achieved^[<https://github.com/Omikhleia/omikhleia-sile-packages/blob/main/examples/markdown-sample.pdf>].
That custom writer targets a specific (non-standard) class and a bunch of specific packages, which might
not have been ported to the latest version of SILE... This said, you can also certainly help pushing the
idea forward!
