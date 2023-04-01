# IEML
IEML (Interface Engine Markup Language) - A simple but powerful config with support for file uploads, inter-file anchors and tags.

# Implementations 
- [C++](https://github.com/Hedgehogo/IEML-cpp) (*short lists* are not supported, support for strings and comments is outdated)

# Syntax

Nesting level is determined by indentation, indentation is allowed only from tabs.

## Comments

The comment begins with `# ` or `#!` and ends at the end of the line.

There may be blank lines at the beginning and end of the file, consisting of: ` `, `\t` (Tab) and comments.

Example:
```
#!Can be used for shebang
# At the beginning of the line
10 # After the scalar
```

## Scalar values
### Boolean

A string equal to `true`, `false`, `yes`, `no` is treated as a Boolean value.

Example:
```
yes
```

### Numbers
*Integer decimal numbers.*

`_` can be used as a separator between digits.

Example:
```
1_005
```

*Integers in a particular system of notation.*

Zero is written first, followed by an English letter from A to Z, indicating a numbering system from 1-cimal to 26-cimal respectively. This is followed by a number in the resulting number system with all the letters in upper case. `_` can be used as a separator between digits.

Examples:
```
0pFF # Hexadecimal, 255 in decimal
```
```
0b01100101 # Binary, 101 in decimal
```

*Real decimal numbers.*

`.` is used as a separator between the integer part and the fractional part. `_` can be used as a separator between digits. The integer part is required to have at least one digit.

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

*Real numbers in a particular number system.*

Zero is written first, followed by an English letter from A to Z, indicating a numbering system from 1-cimal to 26-cimal respectively. This is followed by a number in the resulting number system with all the letters in upper case. `.` is used as a separator between the integer part and the fractional part. `_` can be used as a separator between digits. The integer part is required to have at least one digit.

Examples:
```
0c0.1 # Trinary, 1/3 in decimal
```
```
0b10. # Binary, 2 in decimal
```
```
0b.1 # Binary, error
```

### Strings

All strings must be indented if they are moved to the next line. The indent does not become part of the line.

*Classic string*

It is written in `"`. Characters are escaped with `\`. The characters supported for escaping: 
| The symbol after `\` | The result   |
|----------------------|--------------|
| `n`                  | `\n` (Enter) |
| `t`                  | `\t` (Tab)   |
| `"`                  | `"`          |
| `\`                  | `\`          |
| `\n` (Enter)         | Nothing      |

Any characters are allowed, except `"` without `\` before it, including `\n` (Enter).

Examples:
```
"Hello\t\n\"IEML\"!" # Hello{Enter}"IEML"!
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

*Line string*

Starts with characters `> ` and reads to the end of the line. The characters are not escaped. It is not permissible to move it to the next line.

Examples:
```
> Hello "IEML"!
```
```
> # Not a comment
```

*Not escaped string*

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

Starts without special characters, ends at the end of the line where it began. Characters are not escaped. Characters are forbidden: `"`, `\n` (Enter), `>`, `<`.

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

Each element of the list is denoted by `- `, a space can be replaced by `\n` (Enter).

There may be blank lines between and in front of the *List* items, consisting of: ` `, `\t` (Tab) and comments.

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

### Short note

Starts with `[` and ends with `]`. Inside there is data in `, `. 

Valid data:
- *Boolean*
- *Numbers*
- *Classic string*
- *Raw data*
- *Null*
- Another *List* in the *Short note*

Example:
```
[12, true, "Hello", Hello, null, [10, 15]]
```


## Maps

The map consists of keys and their corresponding values. Keys cannot be repeated. Symbols cannot be used in the key name: `"`, `\n` (Enter), `>`, `<`. The first character of the key must not be a ` `. Keys and values are separated by `: `, space can be omitted.

There may be blank lines between and in front of the *Map* items, consisting of: ` `, `\t` (Tab) and comments.

Example:
```
a: 10
b:
	- 15
	- 20
```

## Short note

If there is only one value in a *List* or in a *Map*, you can write them on one line.

Example:
```
a: b: - 10
```

## Tags

All values can be assigned a tag. This is some string that can store some characteristic of the value, for example you can store a type there.

### All

At the beginning you can write <code>&nbsp;=&nbsp;</code>, the first space can be omitted. After that a string containing no characters: `"`, `\n` (Enter), `<`, `>`. And after that write `: `, space can be omitted. This will assign a tag to all content.

Example:
```
= Meat:
	- > Chicken
	- > Turkey
```

### Lists

After `- ` you can write <code>&nbsp;=&nbsp;</code>, the first space can be omitted. This is followed by a line containing no characters: `"`, `\n` (Enter), `<`, `>`. And after that write `: `, space can be omitted. This will assign a tag to the *List* item.

Example:
```
- = Animal: > Dog
- > Stone
```

### Maps

After the key name but before `: ` you can write <code>&nbsp;=&nbsp;</code>, the first space can be omitted. This is followed by a line containing no characters: `"`, `\n` (Enter), `<`, `>`. And after that write `: `. This will assign a tag to the content by key.

Example:
```
name = English: > John
job: > Chef
```

## Anchors
### Taking

You can write `&` before the value, after which a string not containing: `"`, `\n` (Enter), `<`, `>`, ` `. This string will be the name of the anchor. 
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

You can write `*` before the value, followed by a string that does not contain: `"`, `\n` (Enter), `<`, `>`, ` `. This string will be the name of the requested anchor. Its value will be inserted in that place.

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

You can write `< ` before the value followed by a line. That line will be the path to the file. The contents of the file will be inserted there after the anchors are processed.

Example:
```
key: < subfile.ieml
```

What the `subfile.ieml` could look like:
```
> Inside the child file!
```

### Anchors

All file anchors are retired when the file is included, but the child file can access the anchors of the parent file.
Anchors inside a file can shade anchors from an parent file.

When you include a file, you can pass a *map* of anchors, these anchors will only wind up in the file.

Example:
```
key: < editor.ieml
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
key: < editor.ieml
```

What the `editor.ieml` could look like:
```
template-editor:
	text: > Let's talk about IEML!
	name: *name
```

# For developers

It is recommended to store all *Scalars values* except *Null* and *String* as *Raw data* (strings) and then convert them to the desired type when queried.
