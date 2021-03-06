# Table of Contents

* [Overview](#overview)
* [Features](#features)
* [Benchmark](#benchmark)
* [Syntax](#syntax)
* [Basic Example](#basic-example)
* [CaveMark's Syntax Philosophy](#cavemarks-syntax-philosophy)
* [Documentation](#documentation)
  * [General Use](#general-use)
  * [Making CaveMark Forget Stuff](#making-cavemark-forget-stuff)
  * [Customizing CaveMark](#customizing-cavemark)
    * [List of Options](#list-of-options)
* [Todo](#todo)

# Overview

CaveMark is a typesetting markdown parser.  It is also the fastest pure-Python
markdown parser!  

If you don't know what's a typesetting parser read [this
](https://en.wikipedia.org/wiki/Typesetting).  I don't know any typesetting
markdown parser for Python.  So maybe CaveMark is the only one.  I know about
[Madoko](https://www.madoko.net/), but its syntax sucks and I guess it's not a
Python module (JavaScript).  I never used Madoko.

This typesetting aspect makes CaveMark the easiest way to allow you write
pretty documents for your blog/website/whatever, than, say, mistune or any
other conventional markdown parser around.

E.g. by simply typing `[myimage] shows a happy cat[mynote].` (assuming
resources `myimage` and `mynote` are defined) you will conveniently get this
beautifully formatted page, where figures, captions, footnotes, links, etc are
all done automatically:

![rendered output](https://github.com/Al-Caveman/cavemark/blob/master/demo.png)


Every single HTML code that CaveMark generates is configurable via
`self.frmt_*` variables.  There is no hard-coded HTML strings.  E.g. while by
default `# This is a heading` becomes `<h1>This is a heading</h1>`, you are
free to change it to be, say, `lol This is a heading rofl`, or whatever.

For a production demonstration of what CaveMark can do, checkout [this
website](https://cave.mn); all its articles are written in CaveMark!

# Features

* Headings with automatic indices.
* Paragraphs.
* Emphasized texts.
* Strikethrough texts.
* Lists.
* Shortcuts.
* Inline/block codes.
* Inline/box resources (e.g. books, links, figures, quotes, theorems,
  definitions, etc).
* Footnotes.
* Bibliographies.
* Simple, user-friendly, syntax (I think simplest among all typesetting
  parsers).
* Fastest in class.
* Supports CPython2, Cpython3, PyPy2, PyPy3.
* Made with beard.

# Benchmark

Speed is not the main selling point, but I think it shows that I didn't take
too sloppy shortcuts while coding it.  I am benchmarking against
[mistune](https://github.com/lepture/mistune) coz it's the fastest in town.
Others were too slow to even bother.

Since [mistune](https://github.com/lepture/mistune) and CaveMark have different
syntax, this benchmark only tests things where they agree on their syntax.
Even though, I think, CaveMark still does more.  E.g. headings in CaveMark automatically also produce section identifiers.  E.g:

```md
# Heading

## Subheading

# Another heading
```

gives in [mistune](https://github.com/lepture/mistune) this:

```html
<h1>Heading<h1>

<h2>Subheading<h2>

<h1>Another heading<h1>
```

But gives in CaveMark:

```html
<h1 id="sec_1"><a href="#sec_1">1.  Heading</a></h1>

<h2 id="sec_1.1"><a href="#sec_1.1">1.1.  Subheading</a></h2>

<h1 id="sec_2"><a href="#sec_2">2.  Another headinng</a></h1>
```


Results with CPython3 (repeated 3 times):

```
mistune : 18.177862088 seconds
cavemark: 9.386662586 seconds (1.9 times faster!)

mistune : 18.733004819999998 seconds
cavemark: 9.601156947 seconds (2.0 times faster!)

mistune : 18.762310276999997 seconds
cavemark: 9.647228555 seconds (1.9 times faster!)
```

Results with CPython2 (repeated 3 times):

```
mistune : 19.7261228561 seconds
cavemark: 9.48487520218 seconds (2.1 times faster!)

mistune : 19.4376080036 seconds
cavemark: 9.53958702087 seconds (2.0 times faster!)

mistune : 20.3399410248 seconds
cavemark: 9.61795091629 seconds (2.1 times faster!)
```

Results with PyPy3 (repeated 3 times):

```
mistune : 12.311563448000001 seconds
cavemark: 3.1916073270000003 seconds (3.9 times faster!)

mistune : 12.470256598999999 seconds
cavemark: 3.376128782 seconds (3.7 times faster!)

mistune : 13.637612451999999 seconds
cavemark: 3.126377384 seconds (4.4 times faster!)
```

Results with PyPy2 (repeated 3 times):

```
mistune : 8.99331712723 seconds
cavemark: 1.47040700912 seconds (6.1 times faster!)

mistune : 9.01434993744 seconds
cavemark: 1.45837283134 seconds (6.2 times faster!)

mistune : 9.00144791603 seconds
cavemark: 1.47606897354 seconds (6.1 times faster!)
```

You can find the full benchmark code in the
[benchmark](https://github.com/Al-Caveman/cavemark/tree/master/benchmark)
directory.

In the future, I may do another benchmark where I test all the overlapping
features, even when the syntax is not identical accross CaveMark and
[mistune](https://github.com/lepture/mistune).  I plan to create two separate
texts, both rendering exactly the same desired HTML document, but one in
CaveMark's superior-yet-simpler typesetting syntax, and another in
[mistune](https://github.com/lepture/mistune)'s.

# Syntax

`#[BOOKMARK] HEADING` defines section headings, alongside its optional
BOOKMARK.  E.g.:

* `# heading` becomes `<h1 id='sec_1'><a href='#sec_1'>1.</a> heading</h1>`.
* `#some_bookmark heading` becomes `<h1 id='some_bookmark'><a
  href='#some_bookmark'>1.</a>heading</h1>`.

`_` emphasizes texts.  E.g.  `_this_` = _this_. `~~` to strike through text.
E.g. `~~this~~` = ~~this~~.

Ordered lists are defined by `+`, and the unordered by `*`.  E.g.:

```
* this item is not orderd.
* another unordered item.
    + this item is nested and is ordered.
    + another ordered item.
```

The most interesting part of CaveMark is how it defines _resources_, and uses
them to achieve typesetting.  Below is an example where a figure is defined.
This figure can then be cited later in the document (as shown later in this
section).

```
image  : myimage
url    : http://cave.mn/pics/cat.png
caption: caveman's cat looks happy
alt    : caveman's cat
```

Aside from `image`, many other resource types are defined, e.g. book, footnote,
quote, theorem, etc.  You can also easily add your own custom ones.

Citing a resource is by using `[]`.  E.g. `[myimage]` will cite the `myimage`
resource we defined earlier.  When a resource identifier, e.g. `myimage` is
prefixed by `!`, in a citation box, e.g. `[!myimage]`, then the resource
`myimage` will not expand into its inline citation format. Using `[!myimage]`
is useful when you want to only place the box/bibliography/footnote expansion
of the resource, without placing the inline expansion.

You can also pass an optional data string to the citation procedure, when
citing something.  E.g. `Recently, it was shown that [mylink:chickens can
fly!]`, where `mylink` is a `link` resource that was defined earlier.
This will then work similar to `[chickens can fly!](https://...)`, except better
and prettier.

Finally, a default resource type is implied if no specific resource identifier
is specified in a citation.  E.g.  by default, `[:some text]` will be
interpreted as a citation of the footnote with the automatically-generated
identifier `__1` (this ID is also configurable, where `1` keeps incrementing to
keep each default identifiers unique):

```
footnote: __1
text    : some text
```

CaveMark also supports shortcuts.  By default:

* `(c)`   = `&copy;`.
* `(tm)`  = `&trade;`.
* `(R)`   = `&reg;`.
* `"`     = `&ldquo;` .
* `''`    = `&rdquo;` .
* `--`    = `&mdash;` .
* `...`   = `&hellip;`.

# Basic Example

```python
import cavemark

text_input = '''
image  : myimage
url    : http://cave.mn/pics/cat.png
caption: Caveman's cat.
alt    : Caveman's cat.

book     : mybook
title    : The Book
authors  : Dude McDudeface
publisher: CavePress
year     : 2018

footnote: mynote
text    : She is 1 years old.

quotation: myquote
text     : Beardless men are entry-level trannis
author   : Dude McDudeface, 2018

# This is a _heading_

[myimage] shows a happy cat[mynote].

* A nice list.
* With items in it.
    + Can be nested.

    With, possibly, multiple paragraphs in it!

    Yep, another paragraph, with citations [mybook].

    + Another ordered item, but this one with ~~some struck-through text~~.

A paragraph could have `inline code like this`, or:

```#include <stdio.h>
int main(){
    printf("a code block like this!\\n");
    return 0;
}```

How about citing [myquote]?
'''

parser = cavemark.CaveMark()
parser.parse(text_input)
parser.flush()
html_output = parser.get_html()

with open('test001.html', 'w') as f:
    f.write(html_output)
```

If you open `test.html` using your browser, you will see something neat:

![rendered output](https://raw.githubusercontent.com/Al-Caveman/cavemark/master/test/test001.png)

# CaveMark's Syntax Philosophy

I tried to avoid needless formatting features in CaveMark.  E.g. most markdown
parsers have several ways of doing the same thing.  For example a heading could
be defined by `# This is a heading` or:

```
This is a heading
-----------------
```

The `#` method is superior, since it allows you to also specify heading's level
by repeating `#`.  E.g. `###### This is a heading` defines a level 6 heading.

Therefore, CaveMark only supports the `#` method, and not the under-dashed one.
I don't see a good reason to do it.  Specially that text editors, such as
`vim`, highlight headings texts accordingly either way.

Another example of avoiding needless features is that there is only one way to
define emphasized texts.  I don't see any reason to allow to offer several ways
to emphasize texts.  E.g. some markdown implementations offer `*`, `**`, `_`,
`__` to denote that a text is emphasized.  I think this is needless.  CaveMark
only offers `_` emphasize texts. E.g. `_this_` becomes `<em>this</em>` in HTML
by default.  You can then style the `em` tags as you want, or completely
replace the `em` tags by whatever you want.

I basically think that emphasization of texts is a semantic unit, and it is of
one type.  I see no case where it is good to emphasize texts by underlines in
some paragraph, and then emphasize by bold in another paragraph.  To me this
makes no sense.  IMO, if you wish to denote that a text is emphasized, then
stick to the standard format for such a thing.  In other words, varying the
emphasization format across your paragraphs is like changing the font and color
used in your section headings.

An example of where CaveMark reduces space of reserved keywords is that, in
markdown, commonly, you cite a link by putting `[some text](https://...)` in
your paragraphs.  IMO this is inconvenient for two reasons:

* Citing ugly URLs will clutter your paragraphs.
* Too many keywords are reserved for the parser, namely `[...]` and `(...)`.
  Do we really need to do this?

CaveMark solves those inconveniences by using the syntax `[mylink:some text]`
instead, where `mylink` is a link resource defined elsewhere, e.g.:

```
link:mylink
url :https://domain.com/the/ugly/url?goes=here%20away%20from%20paragraphs
```

CaveMark's approach is superior because:

* It reserves fewer keywords in practice.  I.e. it only reserves `[... : ...]`,
  so you can use `(...)` in your text without worrying about escaping them.
  The fact that `:` is reserved inside a `[...]` block is not worrying, because
  a resource ID is just an identifier (e.g. you don't need to use `:` in an
  identifier).
* It keeps the ugly URLs away from your paragraphs.  You can cite as many ugly
  URLs as you want, without being disturbed by damaging the readability of your
  markdown text.

If you can convince me that more features are needed, I will change my mind.
But so far I have not seen any reason to justify having multiple ways of
defining section headings or emphasized texts.

# Documentation

## General Use

To use CaveMark:

1. `import cavemark`.
2. Initialize a CaveMark parsing object:

  ```python
  parser = cavemark.CaveMark()
  ```
3. Pass CaveMark text `text` to the parser:

  ```python
  parser.parse(text)
  ```

4. Get HTML output

  ```python
  parser.flush()
  html = parser.get_html()
  ```

  Flushing with `parser.flush()` is required to ensure you don't leave some
  leftover shit behind you.

  **Note:** this will flush everything, including the body, footnotes and
  bibliographies.  Sometimes, you wish to only flush one of these three.  E.g.
  sin case you are parsing several articles, and you wish to only flush their
  HTML body and footnote, but not the bibliography, as you may want to gather
  the bibliography and flush it later after all the articles are parsed.  To
  achieve this flexibility, you can pass the following arguments to
  `parser.flush()`:

  * `footnotes`:  Set it to `True` (default) to flush footnotes, or `False`
    to not flush footnotes yet (gather footnotes for a later flush).
  * `bibliography`:  Set it to `True` (default) to flush the bibliography, or
    `False` to not flush the bibliography yet (gather bibliography items for
    a later flush).
  
  E.g. if `parser.flush(footnotes=False, bibliography=False)`, then only the
  HTML body is flushed.

5. Use HTML `html` in your website however you please.

## Making CaveMark Forget Stuff

In order to avoid needing to-reinitialize new CaveMark objects per document, as
you may only wish to reset only simple aspect, such as resetting section
heading indices, citation indices, etc.  Why initialize a whole new CaveMark
parser object, right?  

Here is what you have:

* `parser.forget_resources()`

    Forgets all resources that were found in `text`, unless
    `resource=RESID` is given then only that specific resource, RESID, will
    be forgotten.  

* `parser.forget_citations()`

    For any resource in `text`, forgets the that fact a resource was cited,
    unless `resource=RESID` is given, then only that specific resource,
    RESID, will have its citation forgotten.

    `parser.forget_citations()` differs against `parser.forget_resources()`
    in that the former forgets the fact that a resource was cited, while
    the latter forgets resource's existance/definition.

* `parser.forget_citation_counters()`

    Reset counters of all cited resources, unless `resource_type=RESTYPE`
    is given then only resource indecies of that type RESTYPE will be
    reset.

    This is useful so when you wish to parse a new page, you don't need to
    kcontinue your citation indices from the previous page.  E.g. if the
    last citation index was `[100]`, and you start parsing a new page, you
    want to reset it so that your first cited resource is `[1]` instead of
    `[101]`.

* `parser.forget_section_counters()`

    Forgets the section heading ineces.  E.g. if the last section was, say,
    `10.3.3.5`, then calling this method will forget this fact, so that
    subsequently parsed section headings will start from the beginning,
    e.g. `1.`.  This is useful when you are about to parse a new unrelated
    page to what you were parsing previously.

## Customizing CaveMark

Two ways:

* During CaveMark parsing object initialization:

  ```python
  parser = cavemark.CaveMark(option1=value1, option2=value2, ...)
  ```

* After CaveMark parsing object initialization:

  ```python
  parser.option1=value1
  parser.option2=value2
  # ... etc
  parser.update()
  ```

  As you see, with this approach, calling `parser.update()` is required in
  order to recompile regular expressions, and other things, in order to ensure
  that all your changes are applied.

### List of Options

* `resources`

    Contains parsed resources in HTML form, as found in your passed text.
    This can be a convenient way for you to see all the resources that were
    parsed in the gtext that you have given to `parser.parse(text)`.

    This can also be useful as a caching mechanism.  E.g. for resources
    that you frequently use, you don't need to parse them each time into
    HTML.  Instead, you can simply store `parser.resources` somewhere, and
    reload it `parser.resources=old_parser_resources` before
    `parser.parse(text)`.  This way, you won't need to include resources in
    raw format in `text` as the HTML-cached one is already supplied in
    `parser.resources`.

* `resource_keys_ignored`

    A set containing resource keys that their corresponding value must not
    be parsed.  E.g. `url` is one.

* `resource_counters`

    A dictionary specifying counter names of various resources.  If two
    entries have the same counter name, it means that they will share they
    same counter.  E.g. by default `link` and `book` share the same counter:

    ```python
    resource_counters = {
        'link'      :'counter_a', # sharing same counters
        'book'      :'counter_a', # sharing same counters
        'image'     :'counter_c',
        'note'      :'counter_d',
        'quotation' :'counter_e',
        'definition':'counter_f',
        'axiom'     :'counter_g',
        'theorem'   :'counter_h',
        'corollary' :'counter_i',
        'conjecture':'counter_j',
        'footnote'  :'counter_k',
    }
    ```

* `escape`

    Specifies the escaping sequence.  By default, it's the backward slash.

* `code`

    Defines opening/closing tags for code substrings, or any substring that
    you wish to ignore parsing on.  By default:

    ```python
    code = {
        '$$' : '$$',
        '$'  : '$',
        '`'  : '`',
        '```': '```',
    }
    ```

* `code_inline`

    Specifies whether a specific code substring (identified by its opening
    tag) should be always in a paragraph.  By default:

    ```python
    code_inline= {'$$', '$', '`'}
    ```

* `code_unescape`

    Code substrings that unescapping should be enabled in them.  By
    default:

    ```python
    code_unescape = {'`', '```'}
    ```

    **Note:** the only unescapping that's enabled for them, which is the
    only one that makes sense, is unescapping strings that match their
    closing tag.

* `heading_offset`

    Specifies an offset for all heading levels.  By default:

    ```python
    heading_offset=1
    ```

    E.g. with the default offset of `1`, if `# heading` is seen, then its
    level will be `2` instead of `1`.  E.g. if `## heading` is seen, then
    its level will be `3` instead of `2`.

* `shortcuts`

    A dictionary for simple replacement of some characters by other
    charactters.  By default:

    ```python
    shortcuts = {
        '(c)'   : '&copy;', 
        '(tm)'  : '&trade;', 
        '(R)'   : '&reg;', 
        '"'     : '&ldquo;', 
        "''"    : '&rdquo;', 
        '---'   : '&mdash;', 
        '...'   : '&hellip;', 
    }
    ```

* `cite_id_default`

    If a resource citation lacks an identifier, then that `cite_id_default`
    will be used as its identifier prefix (the suffix is a counter to keep
    dthe identifiers unique).  E.g. `[:some text]` lacks an identifier. By
    default:

    ```python
    cite_id_default = '__'
    ```
* `cite_type_default`

    The resource type for the cited resource that lacked a resource
    identifier.  I.e. the resource type of resource ID `__N`. where N is
    some unique number, as set by `cite_id_default`. By default:

    ```python
    cite_type_default = 'footnote'
    ```

* `cite_key_default`

    The resource key for the cited resource that had optional data
    supplied.  E.g. `[RESID:some optional data]`.  This key depends on the
    resource type that the RESID belongs to.  So, this is a dictionary that
    maps resource type against its default key that the optional data will
    be assigned to, By default:

    ```python
    cite_key_default = {
        'link'      : 'text',
        'footnote'  : 'text',
    }
    ```

* `frmt_cite_inline`, `frmt_cite_box`, `frmt_cite_data_default`,
  `frmt_cite_error_inline`, `frmt_cite_error_box`,
  `frmt_bibliography_prefix`, `frmt_bibliography_suffix`,
  `frmt_bibliography_item`, `frmt_bibliography_error_item`,
  `frmt_footnote_prefix`, `frmt_footnote_suffix`, `frmt_footnote_item`,
  `frmt_footnote_error_item`, `frmt_paragraph_prefix`,
  `frmt_paragraph_suffix`, `frmt_emph_prefix`, `frmt_emph_suffix`,
  `frmt_strike_prefix`, `frmt_strike_suffix`, `frmt_code_prefix`,
  `frmt_code_suffix`, `frmt_olist_prefix`, `frmt_olist_suffix`,
  `frmt_ulist_prefix`, `frmt_ulist_suffix`, `frmt_list_item_prefix`,
  `frmt_list_item_suffix`, `frmt_heading_prefix`, `frmt_heading_suffix`

  All these are HTML formatting variables.  They allow you to change every
  HTML code is generated by CaveMark.

  They are simple stuff.  To see how they look like, just pring them. E.g.
  `print(parser.frmt_cite_inline)` to see the default value.  I am sure anyone
  could figure out how to use them.

# Todo

  - [ ] Tables.

**Note 1:**  I currently don't need these features.  My plan is to wait, until
I happen to need them, then implement them.  You are more than welcome to
submit patches/pull requests.  Alternatively, you can try to motivate me enough
so that I implement them for you.
