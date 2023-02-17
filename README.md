# IEML
IEML (Interface Engine Markup Language) - A simple but powerful config with support for file uploads, inter-file anchors and tags.

# Implementations
- [C++](https://github.com/Hedgehogo/IEML-cpp) (*Unshielded strings* with `>>` and *short lists* are not supported)

# Syntax

Nesting level is determined by indentation, indentation is allowed only from tabs. The examples use double spaces.

## Comments

The comment begins with `#`, must be preceded by ` `, `\t` (Tab) or the beginning of the line, and ends at the end of the line.

Example:
```
# At the beginning of the line
10 # After the space
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

Example:
```
105
```

*Integers in a particular system of notation.*

Zero is written first, followed by an English letter from A to Z, indicating a numbering system from 1-cimal to 26-cimal respectively. This is followed by a number in the resulting number system with all the letters in upper case.

Example:
```
0pFF
```

*Real decimal numbers.*

A dot is used as a separator.

Example:
```
1.15
```

*Real numbers in a particular number system.*

Zero is written first, followed by an English letter from A to Z, indicating a numbering system from 1-cimal to 26-cimal respectively. This is followed by a number in the resulting number system with all the letters in upper case. A dot is used as a separator.

Example:
```
0c0.1 # You get 1/3
```

### Strings
*Classic string*

It is written in `"`. Characters are escaped with `\`. The characters supported for escaping: `n`, `t`, `"`, `\`. Any characters are allowed, except `"` without `\` before it, including `\n` (Enter).

Example:
```
"Hello\n\"IEML\"!"
```

*Unshielded string*

Each line starts with characters `> ` and reads to the end of the line. The characters are not escaped. The indentation level is ignored, ` `, `\t` (Tab) are allowed as an indent.

Example:
```
> Hello
  > "IEML"!
```

It begins with `>>` characters followed immediately by the end of the line. Then each line starts with an indent, followed immediately by a string to the end of the line. This continues until the indentation is lower than required or the end of the file is reached.

Example:
```
>>
Hello
"IEML"!
```

### Raw data

*Boolean* and *Numbers* can be read as Raw data if the config-reader so requests.

Starts without special characters, ends at the end of the line where it began. Characters are not escaped. Characters are forbidden: `"`, `\n` (Enter), `>`, `<`.

Example:
```
Hello IEML!
```

### Null

A string equal to `null` is treated as a Null.

Example:
```
null
```

## Lists

Each element of the list is denoted by `- `, a space can be replaced by `\n` (Enter).

Example:
```
- 10
- 1.15
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

The map consists of keys and their corresponding values. Keys cannot be repeated. Symbols cannot be used in the key name: `"`, `\n` (Enter), `>`, `<`. Keys and values are separated by `: `, space can be omitted.

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

At the beginning you can write ` = `, the first space can be omitted. After that a string containing no characters: `"`, `\n` (Enter), `<`, `>`. And after that write `: `, space can be omitted. This will assign a tag to all content.

Example:
```
= Meat:
  - > Chicken
  - > Turkey
```

### Lists

After `- ` you can write ` = `, the first space can be omitted. This is followed by a line containing no characters: `"`, `\n` (Enter), `<`, `>`. And after that write `: `, space can be omitted. This will assign a tag to the *List* item.

Example:
```
- = Animal: > dog
- > stone
```

### Maps

After the key name but before `: ` you can write ` = `, the first space can be omitted. This is followed by a line containing no characters: `"`, `\n` (Enter), `<`, `>`. And after that write `: `. This will assign a tag to the content by key.

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

### Getting

You can write `*` before the value, followed by a string that does not contain: `"`, `\n` (Enter), `<`, `>`, ` `. This string will be the name of the requested anchor. Its value will be inserted in that place.

Example:
```
take: &name > John
get: *name # Here will be the "John"
```

## Downloading files

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

All file anchors are retired when the file is loaded, but the child file can access the anchors of the parent file.

When you load a file, you can pass a *map* of anchors, these anchors will only wind up in the file.

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

# For developers

It is recommended to store all *Scalars values* except *Null* and *String* as *Raw data* (strings) and then convert them to the desired type when queried.
