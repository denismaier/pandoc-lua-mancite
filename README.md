# pandoc-lua-mancite
A simple Lua filter to implement a manual citation command in `pandoc-citeproc`.

# Background

`pandoc-citeproc` is an amazing tool, but it sometimes produces wrong results for styles that use "Ibid." to shorten immediately repeating citations, i.e., "Ibid." occurs when it clearly shouldn't---e.g., when both bibliographic footnotes and discursive footnotes are used.

This

```
Bla bla. [@doe]

Bla bla. ^[Bla Bla.]

Bla bla. [@doe]

```

May lead to:

```
Bla bla. ^[Doe]

Bla bla. ^[Bla Bla.]

Bla bla. ^[Ibid.]

```

The "Ibid." in the third footnotes is obviously wrong.

In the absence of an official solution, pandoc's capabilities to use Lua filters provide an easy way---hackish, but efficient---to remedy this situation: You just have to add a fake citation, which will cause the next citation to be a normal citation (`position="subsequent"` in CSL parlor), and then use a filter to remove all citations with that particular `id`. This filter assumes that all such manual/fake citations have the `id` `mancite`.

This 

```
Bla bla. [@doe]

Bla bla. ^[Bla Bla. @mancite]

Bla bla. [@doe]

```

Will correctly lead to: 

```
Bla bla. ^[Doe.]

Bla bla. ^[Bla Bla.]

Bla bla. ^[Doe.]

```

Obviously, this can also be used to mix automatic and manual citations---e.g. when citing classical texts that currently can't be cited properly with `CSL`.


# Usage

Usage is simple:


```
pandoc input.md -o output.html --csl=chicago-note-bibliography.csl -F pandoc-citeproc -L pandoc-mancite-lua
```

Just be sure to have the filters in the correct order: `pandoc-citeproc` must produce the correct citations before we remove the fake citations with `-L pandoc-mancite-lua`.