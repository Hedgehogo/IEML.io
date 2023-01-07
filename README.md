# IEML
IEML (Interface Engine Markup Language) - A simple but powerful config with support for file uploads, inter-file anchors and type tags.

# Implementations
- [C++](https://github.com/Hedgehogo/IEML-cpp)

# Syntax

Nesting level is determined by indentation, indentation is allowed only from tabs.

## Comments
```
 # The commentary will go from the " #" to the end of the line.
```

## Scalar values
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

It is written in `"`. Characters are escaped with `\`. The characters supported for escaping: `n`, `t`, `"`, `\`.

Example:
```
"Hello\n\"IEML\"!"
```

*Unshielded string*

Each line starts with characters `> ` and reads to the end of the line. The characters are not escaped.

Example:
```
> Hello
> "IEML"!
```

*Simple string*

Starts without special characters, ends at the end of the line where it began. Characters are not escaped. Characters are forbidden: `"`, `\n` (Enter), `>`, `<`.

Example:
```
Hello IEML!
```

## Lists

Each element of the list is denoted by `- `, a space can be omitted.

Example:
```
- 10
- 1.15
- Hello
- - 2
  - 4
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

If there is only one value in a list or in a map, you can write them on one line.

Example:
```
a: b: - 10
```

## Tags

All values can be assigned a tag. This is some string that can store some characteristic of the value, for example you can store a type there.

### All

At the beginning you can write ` = `, the first space can be omitted. After that a string containing no characters: `"`, `\n` (Enter), `<`, `>`. This will assign a tag to all content.

Example:
```
= Meat:
  - Chicken
  - Turkey
```

### Lists

After `- ` you can write ` = `, the first space can be omitted. This is followed by a line containing no characters: ``, `\n` (Enter), `<`, `>`. This will assign a tag to the list item.

Example:
```
- = Animal: dog
- stone
```

### Maps

After the key name but before `: ` you can write ` = `, the first space can be omitted. This is followed by a line containing no characters: ``, `\n` (Enter), `<`, `>`. And after that write `: `. This will assign a tag to the content by key.

Example:
```
name = English: John
job: Chef
```

## Anchors
### Taking

You can write `&` before the value, after which a string not containing: `"`, `\n`, `<`, `>`, ` `. This string will be the name of the anchor. 
Space after the anchor is skipped, then comes the value.

Example:
```
&name John
```

### Getting

You can write `*` before the value, followed by a string that does not contain: `"`, `\n`, `<`, `>`, ` `. This string will be the name of the requested anchor. Its value will be inserted in that place.

Example:
```
take: &name John
get: *name #Here will be the "John"
```

## Downloading files

You can write `< ` before the value followed by a line. That line will be the path to the file. The contents of the file will be inserted there after the anchors are processed.

Example:
```
key: < subfile.ieml
```

### Anchors

All file anchors are retired when the file is loaded, but the child file can access the anchors of the parent file.

When you load a file, you can pass a list of anchors that will be used by the file as a map. these anchors will only wind up in the file.

Example:
```
key: < subfile.ieml
    name: John
```
