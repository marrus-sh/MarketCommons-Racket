# Market Commons

Market Commons is a lightweight markup language resulting from my frustrations regarding Markdown and how little it is able to do.
It converts more straightforwardly to HTML, aiming for a simpler syntax while providing authors with the same semantics.
If you frequently find yourself writing HTML inside of your Markdown files, Market Commons might be useful to you.
(If you donʼt, you may find it needlessly complicated.)


(License: GPL 3 or later.)


## Metadata

If the first non­‑whitespace character in a document is an unescaped less­‑than sign (`<`), all lines up until the next blank line will be treated as literal metadata content (to be included verbatim in the `<head>`).

	<title>My lovely document</title>

	; There was a blank line, so now the document begins.


## Headings

Headings are indicated by a number of `-` characters, giving the level of the heading, followed by the heading text, followed by zero or more `-` characters, which will be ignored.
Headings must be on a single line.
For example:

	- Level 1 Heading ------

	-- Level 2 Heading

Heading marks *must not* be immediately followed by a pipe.

	-| This is a paragraph which starts with the string "-|".


## Sectioning Elements

Sections are indicated using one of a number of sectioning marks (which identify the type of section), repeated to show the nesting level.
Section markers must be on a single line, and there must not be spaces between them.
For example:

	$$$

	This is a level 3 section.

Sectioning marks *must not* be immediately followed by a pipe.

	$| This is a paragraph which starts with the string "$|".

The available sectioning marks are as follows:

+ `@`: `<article>`
+ `<`: `<aside>`
+ `>`: `<blockquote>`
+ `:`: `<div>`
+ `_`: `<footer>`
+ `^`: `<header>`
+ `%`: `<nav>`
+ `$`: `<section>`

A title for the section may optionally be given on the same line, after the sectioning marks.
This is the same as if it were specified using a heading element with the same level.
The sectioning mark may be repeated any number of times after the title to no effect.
For example,

	@ My Article
	$$ Section the First $$$$

is the same as

	@
	- My Article
	$$
	-- Section the First

A blank heading will be generated with the following:

	$$        $$$

Do not do this!

Sections are closed when they are followed with another section of equal or lesser level.
A line consisting only of slashes can be used to manually close all previous sections of equal or greater level, like so:

	$$ A section
	$$$ A subsection

	//

	This paragraph is not in either section.


## Block Elements

Block elements consist of a number of successive nonblank lines, followed by either a blank line or the end of the document.
The type of block is identified by the first character(s) of the first line.
This character may be repeated at the beginning of following lines, in which case it will be ignored.

	. This
	. is
	. a
	. paragraph
	. broken
	. across
	. multiple
	. lines

Block sigils *must not* be immediately followed by a pipe.

	+| This is a paragraph which starts with the string "+|".

Block elements (except for paragraphs) may contain other block elements by combining their sigils.
For example, here is an unordered list item which contains multiple paragraphs:

	+. This is the first paragraph.
	+. This is another sentence in the first paragraph.
	+
	+. This is the second paragraph.
	+. But, note that this is still the same list item.

	+. This is the first paragraph of a second list item.

Not all block sigils need be present, so long as the ones which *are* present are in the correct order.
For example, any of the following will be ignored:

	+. With this first line
	+. This
	+ Or this
	. Or this

However:

	++. With this first line
	+.+ The second plus here will be considered part of content.

Space characters are allowed between block sigils.

If the first characters of a block are not a valid block sigil, then the block is treated as a paragraph (as though `.` had been specified).
However, this is not true inside of other blocks:

	This is implicitly a paragraph.

	+ however, this is not
	+
	+ . but this is
	+
	+ and this is not again
	+

The following blocks are available:

+ `]`: `<address>`
+ `>` : `<blockquote>` (only inside of other blocks)
+ `,`: `<dd>`
+ `:` : `<div>` (only inside of other blocks)
+ `?`: `<dt>`
+ `_`: `<figcaption>` (as a child of `<figure>` only)
+ `[`: `<figure>`
+ `&`: `<li>` (ordered)
+ `+`: `<li>` (unordered)
+ `.`: `<p>`
+ `;`: Block comment
+ `` ` ``: Block literal (the contents of this block element will be output directly)

You can use a block comment to separate two lists.
If you need to comment inside of a list, place the comment within one of the list items.
For example:

	+ A list item

	+ Another item in the same list

	;

	+ A new list
	+
	+ ; with a comment inside


## Inline Elements

Inline elements consist of text surrounded by tags of the form `X|` and `|X`, where `X` is a character identifying the kind of element.
They can appear anywhere text content is allowed, but must be entirely contained by a single block.
(This means they cannot contain blank lines.)

The following inline elements are available:

+ `@`: `<a>`
+ `]` `<abbr>`
+ `#`: `<b>`
+ `'`: `<cite>`
+ `~`: `<code>`
+ `-`: `<del>`
+ `?`: `<dfn>`
+ `*`: `<em>`
+ `/`: `<i>`
+ `+`: `<ins>`
+ `>`: `<kbd>`
+ `%`: `<mark>`
+ `"`: `<q>`
+ `=`: `<s>`
+ `<`: `<samp>`
+ `[`: `<small>`
+ `:`: `<span>`
+ `!`: `<strong>`
+ `,`: `<sub>`
+ `^`: `<sup>`
+ `_`: `<u>`
+ `$`: `<var>`
+ `;`: Inline comment
+ `` ` ``: Inline literal (the contents of this inline element will be output directly)

As a special case, if an anchor element is empty and it has an `href` attribute, that will be used as its contents:

	@||@{!http://example.org}

…is equivalent to…

	@|http://example.org|@{!http://example.org}


## Attributes

Attributes may be provided in curly­‑brace containers in various locations depending on the type of element:

+ For headings and titles, at the end of the line: `- Heading ----- {attributes}`
+ For sections, after the section mark: `: {attributes}`
	+ For sections with titles, you can include both: `$$ {section-attributes} Title {heading-attributes}`
+ For block elements, after the block sigil: `. {attributes} Paragraph content.`
	+ Ignored for comments and literals
+ For inline elements, after the closing tag: `:|Inline content|: {attributes}`
	+ Ignored for comments and literals

Any spaces before the attribute container are ignored.

Attributes must be separated by space characters and can take a variety of syntaxes:

+ `?value`: specifies the `about` attribute
+ `.value`: specifies the `class` attribute
+ `~value`: specifies the `content` attribute
+ `^value`: specifies the `datatype` attribute
+ `!value`: specifies the `href` attribute
+ `#value`: specifies the `id` attribute
+ `@value`: specifies the `lang` attribute
+ `,value`: specifies the `property` attribute
+ `>value`: specifies the `rel` attribute
+ `%value`: specifies the `resource` attribute
+ `<value`: specifies the `rev` attribute
+ `&value`: specifies the `src` attribute
+ `$value`: specifies the `style` attribute
+ `*value`: specifies the `tabindex` attribute
+ `/value`: specifies the `title` attribute
+ `` `value ``: specifies the `type` attribute
+ `;value`: specifies the `typeof` attribute
+ `=value`: specifies the `role` attribute
+ `attr`: specifies the attribute given by `attr` as having a value of the empty string
+ `attr=value`: specifies any attribute given by `attr`

If an attribute is specified more than once, its values will be joined with spaces:

	]|NASA|]{/National /Aeronautics /and /Space /Administration}


## Multimedia content

Multimedia content has the following form:

	&|Alt text | http://example.com|& {`media-type}

That is, it is an inline element consisting of plain­‑text contents (used as its description), followed by a pipe and a link to the content.
The `type` attribute (shorthand: `` ` ``) will determine whether a `<video>` or `<img>` element is used; the latter is used whenever the `type` begins with `image/`, is the literal string `image`, or is unspecified.

You can provide multiple sources for the multimedia element as well:

	&|
		Video/audio with multiple sources |
		http://example.com/content.webm |
		http://example.com/content.mp4
	|& {`video/webm `video/mp4}

As you can see, the types for each source are separated by spaces.


## Atomic values and text handling

Any single character may be escaped by placing it in­‑between backslashes, like so: `\|\`.
This includes the backslash itself: `\\\`.

Characters may also be escaped by codepoint using the form `\U+XXXX\`, where XXXX is the codepoint of the character in hex form (any length is allowed).

Two plain adjacent pipe characters represent a line break (`<br>`): `||`.

All whitespace will be trimmed and collapsed, except inside of inline literals (where it will be preserved).
This takes place *after* substituting character escapes, so `\U+20\` will be treated as though it were a space in the source.
