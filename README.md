# IEML
IEML (Interface Engine Markup Language) - A simple but powerful config with support for file uploads, inter-file anchors and tags.

# Implementations 
- [C++](https://github.com/Hedgehogo/IEML-cpp) (support is outdated)

# Syntax
Nesting level is determined by indentation, indentation is allowed only from tabs.

All elements can be moved to the next line with an indentation level.

## Comments

The comment begins with <code>#&nbsp;</code> or `#!` and ends at the end of the line.

There may be blank lines at the beginning and end of the file, consisting of: <code>&nbsp;</code>, ⇥ (Tab) and comments.

Example:
```
#!Can be used for shebang
# At the beginning of the line
10 # After the scalar
```

## Scalar values
### Booleans

A string equal to `yes`, `no` is treated as a Boolean value.

Example:
```
yes
```

### Numbers

Digits (0-9) and capital letters of the English alphabet (A-Z) can be used, if they exist in the required number system. `_` can be used as a separator between digits. The default numbering system is decimal. 

**Integer numbers.**

Example:
```
3_005 # 3005 in decimal
```

**Real numbers.**

`.` is used as a separator between the integer part and the fractional part. The integer part is required to have at least one digit.

Any *integer number* can be read as a real number.

Examples:
```
1.15
```
```
23.
```
```
.1 # Error
```

The functional further works for any numbers. 

**Specifying the number system.**

Before the number is written the number system in decimal notation, and then it is written `'`.

Examples:
```
16'FF # Hexadecimal, integer, 255 in decimal
```
```
2'01100101 # Binary, integer, 101 in decimal
```
```
3'0.1 # Trinary, real, 1/3 in decimal
```
```
2'10. # Binary, real, 2 in decimal
```

**Scientific notation.**

A number can be followed by an `e` followed by a full *integer number* (can be written in different number systems, etc.). This number is an exponent.

Examples:
```
9.109_383_56e−31 # 9.10938356 * (10^−31) in decimal
```
```
1e16'F # 1 * (10^15) in decimal
```

### Strings

All strings must be indented if they are moved to the next line. The indent does not become part of the line.

**Classic string**

It is written in `"`. Characters are escaped with `\`. The characters supported for escaping: 
| The symbol after `\` | The result   |
|----------------------|--------------|
| `"`                  | `"`          |
| `\`                  | `\`          |
| `n`                  | ↵ (Newline)  |
| `t`                  | ⇥ (Tab)      |
| ↵ (Newline)          | (Nothing)    |

Any characters are allowed, except `"` without `\` before it, including ↵ (Newline).

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

Starts with characters <code>>&nbsp;</code> and reads to the end of the line. The characters are not escaped. It is not permissible to move it to the next line.

Examples:
```
> Hello "IEML"!
```
```
> # Not a comment
```

**Not escaped string**

It begins with `>>` characters followed immediately by the end of the line. Then each line starts with an indent, followed immediately by a string to the end of the line. This continues until the indentation is lower than required or the end of the file is reached.

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

*Boolean* and *Numbers* can be read as Raw data if the config-reader so requests.

Starts without special characters, ends at the end of the line where it began. Characters are not escaped. Characters are forbidden: `"`, ↵ (Newline), `>`, `<`.

Examples:
```
Hello IEML!
```
```
>Hello IEML! # Error, Raw data prohibits the `>` character, and an unshielded string requires a space after this character
```

### Null

A string equal to `null` is treated as a Null.

Examples:
```
null
```
```
 null # Raw data, Null does not allow spaces before the word
```

## Lists

Each element of the list is denoted by <code>-&nbsp;</code>, a space can be omitted if the next character is ↵ (Newline).

There may be blank lines between and in front of the *List* items, consisting of: <code>&nbsp;</code>, ⇥ (Tab) and comments.

Lists increase the indentation level for each element.

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

Starts with `[` and ends with `]`. Inside there is data in <code>,&nbsp;</code>. 

Valid data:
- *Boolean*
- *Numbers*
- *Classic string*
- *Raw data*
- *Null*
- Another *List* in the *Short notation*

Example:
```
[12, true, "Hello", Hello, null, [10, 15]]
```


## Maps

The map consists of keys and their corresponding values. Keys cannot be repeated. Symbols cannot be used in the key name: `"`, ↵ (Newline), `>`, `<`. The first character of the key must not be a <code>&nbsp;</code>. Keys and values are separated by <code>:&nbsp;</code>, space can be omitted if the next character is ↵ (Newline).

There may be blank lines between and in front of the *Map* items, consisting of: <code>&nbsp;</code>, ⇥ (Tab) and comments.

Lists increase the indentation level for each element.

Example:
```
a: 10
b:
	- 15
	- 20
```

## Short notation

If there is only one value in a *List* or in a *Map*, you can write them on one line.

Example:
```
a: b: - 10
```

## Tags

All values can be assigned a tag. This is some string that can store some characteristic of the value, for example you can store a type there.

The tag is preceded by <code>=&nbsp;</code>. This is followed by a string containing no characters: `"`, ↵ (Newline), `<`, `>`. This string is a tag. It must be followed by <code>:&nbsp;</code>.

The tag does not raise the indentation level and is written right before the value it is assigned to.

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

## Anchors
### Taking

You can write `&` before the value, after which a string not containing: `"`, ↵ (Newline), `<`, `>`, <code>&nbsp;</code>. This string will be the name of the anchor. 
Space after the anchor is skipped, then comes the value.

Example:
```
&name > John
```

Example:
```
# name = "John"
first: &name > John
```

### Getting

You can write `*` before the value, followed by a string that does not contain: `"`, ↵ (Newline), `<`, `>`, <code>&nbsp;</code>. This string will be the name of the requested anchor. Its value will be inserted in that place.

Let's assume a non-direct order of anchors. That is, you can first get an anchor, and then take it.

Example:
```
take: &name > John
get: *name # Here will be the "John"
```
```
get: *name # Here will be the "John"
take: &name > John
```

## Including files

You can write <code><&nbsp;</code> before the value followed by a line. That line will be the path to the file. The contents of the file will be inserted there after the anchors are processed.

Example:
```
key: < subfile
```

What the `subfile.ieml` could look like:
```
> Inside the child file!
```

### Anchors

All file anchors are retired when the file is included, but the child file can access the anchors of the parent file. Anchors inside a file can shade anchors from an parent file.

When you include a file, you can pass a *map* of anchors, these anchors will only wind up in the file.

Example:
```
key: < editor
	name: > John
```

What the `editor.ieml` could look like:
```
template-editor:
	text: > Let's talk about IEML!
	name: *name
```

In addition to passing anchors, child files can access parent file anchors directly.
If there is an anchor in both the child and parent file, then the anchor of the child file is selected. Anchors passed with *Map* are considered to be anchors of the child file.

Example:
```
name-key: &name John
key: < editor
```

What the `editor.ieml` could look like:
```
template-editor:
	text: > Let's talk about IEML!
	name: *name
```

# For developers

It is recommended to store all *Scalars values* except *Null* and *String* as *Raw data* (strings) and then convert them to the desired type when queried.
