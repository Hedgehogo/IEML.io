# IEML
IEML (Interface Engine Markup Language) - A simple but powerful data representation format with support for file uploads, inter-file anchors and tags.

Designed to store and manually edit data, poorly suited for non-manual editing and data transfer.

# Implementations 
- [C++](https://github.com/Hedgehogo/IEML-cpp)

# Syntax
A *character* is referred to as a Unicode grapheme cluster.

An *indent* is a sequence of N number of *characters* ⇥ (Tab). An *indentation level* is the number N. *Increasing the indentation level* is adding a one to the number N. The initial *indentation level* for any document is 0.

A *line break* is a sequence consisting of the optional *character* ↤ (Return stub) and the mandatory *character* ↵ (Newline).

A *node* is one of the enumerated list where, the higher the element, the higher it is prioritised: 
- *Tagged*
- *Anchor*
- *Child document*
- *List*
- *Map*
- *Scalar*

A *child node* is an optional sequence W followed by a *node*. The W sequence begins with a *line break*, continues with any number of *blank lines*, and ends with an *indent*.

## Comments
A *comment* is a sequence beginning with the sequence <code>#&nbsp;</code> or `#!` and ending with a sequence of any number of *characters* that are not part of a *line break*.

A *whitespace* is a sequence starting with any number of ` ` or ⇥ (Tab) characters, ending with an optional comment.

A *blank line* is a sequence starting with *whitespace* and ending with a *line break* or the end of a file.

Any document starts with any number of *blank lines*, continues with a single *node*, and ends with any number of *blank lines*.

Example:
```
#!Can be used for shebang
# At the beginning of the line
10 # After the scalar
# At the end of file
```

## Scalar values
A *scalar* is a sequence of one of the elements of the list S and *whitespace*.

In the S list, the higher items are prioritised and arranged as follows:
- String
- Number
- Null
- Raw data
### Booleans

A *boolean* is a sequence equal to `yes` or `no`.

Example:
```
yes
```

### Numbers
A *digit* is a *character* included in the set `0` - `9` or `A` - `Z`. A *base digit* is a *digit* that exists in the specified base. A *decimal digit* is a *base digit* with a base of 10. Unless otherwise specified, the default base is 10. Symbols `0` - `9` are mapped to the number range 0 - 9, and `A` - `Z` to 10 - 35.

A *number* is a sequence of a *number content* and the optional sequence E.

A *number content* is a sequence starting with the optional character `-`, and following it sequentially: optional sequence B, non-zero sequence N and optional sequence F

The sequence B begin with a non-zero sequence of *decimal digits* and `_` and end with the symbol `'`. The entire contents of sequence B except for the characters `_` and `'` are the base, written in decimal notation, for sequence N.

The sequence N contains only *base digits* and `_` characters. Its contents, except for the `_` *characters*, are the integer part of a number, written in positional notation.

The sequence F starts with the character `.` followed by a sequence containing only *base digits* and the *characters* `_`. Its contents, except for the *characters* `_` and `'`, are the fractional part of a number, written in positional notation.

The sequence E starts with the *character* `e` and ends with a *number content* that does not contain the sequence F. The contents of the sequence with the exception of *character* `e` is an exponent for scientific notation.

**Integer number**

An *integer number* is a *number* that does not contain the sequence F.

Example:
```aber
3_005 // 3005 in decimal
```
```aber
16'FF // Hexadecimal, integer, 255 in decimal
```
```aber
2'01100101 // Binary, integer, 101 in decimal
```

**Floating point number**

A *floating point number* is a *number* that optionally contains the sequence F.

Examples:
```aber
1.15
```
```aber
3'0.1 // Trinary, 1/3 in decimal
```
```aber
2'10. // Binary, 2 in decimal
```

**Scientific notation.**

Examples:
```
9.109_383_56e−31 # 9.10938356 * 10^(−31) in decimal
```
```
1e16'F # 1 * 10^15 in decimal
```

### Strings

**Classic string**

A *classic string* is a sequence starting with the *character* `"` and ending with the same *character* that is not part of an *escape sequence*, and contains a sequence of *escape sequences*, sequences I and *characters* except for `"` and ↵ (Newline). The contents are sequentially standing *characters* and *characters* obtained by substituting *escape sequences*.

The sequence I starts with a *line break* and ends with an *indent*, the *line break* remains part of the contents of the line and the *indent* does not.

For *string* *escape sequences* are mapped to *character* according to the following table: 

| Аfter `\`  | Result      |
| ---------- | ----------- |
| `"`        | `"`         |
| `\`        | `\`         |
| `n`        | ↵ (Newline) |
| `t`        | ⇥ (Tab)     |
| Sequence I | Nothing     |

Examples:
```
"Hello\t\n\"IEML\"!" # Hello{Newline}"IEML"!
```
```
	"Hello\t
	\"IEML\"!" # Same as the first one
```
```
	"Hello\
		\n\"IEML\"!" # Same as the first one
```
```
	"Hello\t
\"IEML\"!" # Error, the tab level for the string is not respected
```

**Line string**

A *line string* is a sequence beginning with the sequence <code>>&nbsp;</code>, and ending with any number of *characters* that are not part of a *line break*. Everything except for <code>>&nbsp;</code> is the contents of the string.

Examples:
```
> Hello "IEML"!
```
```
> # Not a comment
```

**Not escaped string**

An *not escaped string* is a sequence starting with sequence S and ending with any number of alternating sequences I and C. The sequential concatenation of the contents of all sequences I and C is the contents of the string.

The sequence S starts with the sequence `>>` and ends with a *blank line* and *indent*.

The sequence I starts with a *line break* and ends with an *indent*. Only the *line break* is content.

The sequence C is any number of *characters* that are not part of a *line break*. The whole sequence is content.

Examples:
```
>>
Hello
"IEML"!
```
```
	>>
	Part of a string
		Part of a string (Including tab)
Not part of the string
```

### Raw data

Note: *boolean* and *numbers* can be read as *raw data* if the config-reader so requests.

A *raw data* is any number of *characters* that are not `"`, `>`, `<` or part of a *line break*.

Examples:
```
Hello IEML!
```
```
>Hello IEML! # Error, Raw data prohibits the `>` character, and an line string requires a space after this character
```

### Null

A *null* is a sequence `null`.

Examples:
```
null
```
```
 null # Raw data, Null does not allow spaces before the word
```

## List

A *list* is a sequence of: sequence E, the number N of sequences of pairs of sequences I and E. If the list is a *child node* without sequence W, then the number N must be 0. The content is a list of the contents of the E sequences, in the same order in which they occur in the document.

The sequence I starts with a *line break*, continues with any number of *blank lines*, and ends with an *indent*.

The sequence E starts with the sequence <code>-&nbsp;</code>, and ends with a *child node* with *increasing indentation level*. The space after `-` can be omitted if the sequence is followed by a *line break*. The content is the content of the *child node*.

Example:
```
- 10
- 1.15
  # String
- > Hello
- 
	- 2
	- 4
```

### Short notation

A *list* in *short notation* is a sequence starts with `[`, continues with any number of elements of list E, separated by the sequences <code>,&nbsp;</code>, ends with the sequence `]`.

In the S list, the higher items are prioritised and arranged as follows:
- *Anchor request* that does not contain the sequence <code>,&nbsp;</code> or `]` in the *name*
- *List* in *short notation*
- *Classic string* that does not contain ↵ (Newline)
- *Number*
- *Boolean*
- *Null*
- *Raw data* that does not contain the sequence <code>,&nbsp;</code> or `]`

Example:
```
[12, yes, "Hello", Hello, null, [10, 15]]
```

## Map

A *name* is a sequence of any *characters* that are not part of sequence <code>:&nbsp;</code> or a *line break* that meets the following requirements:
- It must not begin with the sequence <code>=&nbsp;</code>, `@`, ` ` or ⇥ (Tab)
- It must not end with `:`

A *pair separator* is a sequence starting with the *character* `:` and ending with the *character* ` `, optional if followed by a *line break*.

A *map* is a sequence of: sequence E, the number N of sequences of pairs of sequences I and E. If the list is a *child node* without sequence W, then the number N must be 0. The content is a map assembled from the content of E sequences. Keys in the map must not be repeated.

The sequence I starts with a *line break*, continues with any number of *blank lines*, and ends with an *indent*.

The sequence E starts with a *name*, continues with a *pair separator*, and ends with a *child node* with *increasing indentation level*. The content is a pair of the *name* and the content of the *child node*.

Example:
```
a: 10
b:
	- 15
	- 20
```

## Tag

A tag is a sequence containing sequentially: the sequence `= `, a *name*, a *pair separator* and a *child node*. The content is a pair of *name* and content of the *child node*.

Example:
```
= Meat:
- > Chicken
- > Turkey
```
```
- = Animal: > Dog
- > Stone
```
```
name: = English: > John
job: > Chef
```

## Anchor

An *anchor* is a sequence starting with the *character* `@`, continuing with a *name* and ending with the optional sequence V. The content is the content associated with the anchor.

The sequence V starts with a *pair separator* and ends with a *child node*.

An *anchor creation* is an *anchor* with a sequence V. An *anchor request* is an *anchor* with no sequence V. 

If sequence V is present, the *anchor* is associated with the sequence content, it must be associated exactly once.  *Creation* and *requests* of a single *anchor* can be in any order.

Example:
```
# An anchor with the name "name" is associated with the value "John"
@name: > John
```
```
create: @name: > John
# Here will be the "John"
request: @name 
```
```
# Here will be the "John"
request: @name
create: @name: > John
```

## Child document

A *child document* is a sequence starting with the sequence `< `, continuing with the sequence P and ending with the optional sequence A. A *path* is the contents of a sequence P. Content is the content of the document on the *path*.

The sequence P is any number of *characters* that are not part of a *line break*. The content is the entire sequence.

The sequence A starts with a *line break*, continues with any number of *blank lines* and *indent*, and ends with a *map*. The contents are the contents of the *map*.

If the *path* is treated as a file path, the file extension should be omitted and the *path* treated in the next priority:
- Path relative to the path to the current document
- Path relative to the path to the program that reads the document
- Absolute system path 

Example:
```
key: < subfile
```

What the `subfile.ieml` could look like:
```
> Inside the child file!
```

### Anchors

The contents of a sequence A are treated as a set of *anchors*, where in each pair, *name* is the *anchor* *name* and content is the content associated with the *anchor*. These *anchors* are available in the document located on the *path*, and can be shadowed by *anchors* within.

Example:
```
key: < editor
	name: > John
```

What the `editor.ieml` could look like:
```
template-editor:
	text: > Let's talk about IEML!
	name: @name
```

All anchors available within this document are also available within the child document and can be shadowed within it.

Example:
```
name-key: @name: John
key: < editor
```

What the `editor.ieml` could look like:
```
template-editor:
	text: > Let's talk about IEML!
	name: @name
```

# For developers

It is recommended to store all *Scalars values* except *Null* and *String* as *Raw data* (strings) and then convert them to the desired type when queried.
