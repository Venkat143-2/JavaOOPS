# Java String, StringBuilder, and StringBuffer Methods

A reference guide for Java fundamentals and DSA. Each method includes its purpose, a successful example, a failure or edge-case example, and index-boundary rules where relevant.

## Core concepts

- **`String`** is immutable: methods that transform its text return a result; they do not modify the original string.
- **`StringBuilder`** is mutable and is generally preferred for repeated string modifications in single-threaded code.
- **`StringBuffer`** is mutable and its methods are synchronized. Synchronization can help coordinate individual method calls between threads, but does not automatically make a multi-call workflow atomic.
- **Inclusive** means the boundary is included. **Exclusive** means the boundary is not included.
- Unless stated otherwise, examples show the result of the expression. Each builder/buffer example starts with a fresh object.

---

## 1. Common index rules

| Method / operation | Start index | End index | Example |
|---|---|---|---|
| `charAt(index)` | One index is selected | No end index | `"Java".charAt(1)` → `'a'` |
| `substring(start)` | Inclusive | Extends to the end | `"Java".substring(2)` → `"va"` |
| `substring(start, end)` | Inclusive | Exclusive | `"Java".substring(1, 3)` → `"av"` |
| `subSequence(start, end)` | Inclusive | Exclusive | `"Java".subSequence(1, 3)` → `"av"` |
| `StringBuilder.delete(start, end)` | Inclusive | Exclusive | `new StringBuilder("Java").delete(1, 3)` → `"Ja"` |
| `StringBuilder.replace(start, end, text)` | Inclusive | Exclusive | `new StringBuilder("Java").replace(1, 3, "XY")` → `"JXYa"` |
| `indexOf(text)` | Searches from the beginning | Not an end-index method | `"Java".indexOf("a")` → `1` |
| `lastIndexOf(text)` | Searches backward | Not an end-index method | `"Java".lastIndexOf("a")` → `3` |

**Important:** `String.replace(old, new)` replaces matching text and does not accept start/end indexes. The indexed `replace(start, end, text)` method belongs to `StringBuilder` and `StringBuffer`.

---

## 2. `String` methods

Assume:

```java
String s = "Java Programming";
String x = "Java";
String empty = "";
```

### A. Length, characters, and extracting text

| Method | Purpose | Successful example → result | Failure / edge example | Index rule |
|---|---|---|---|---|
| `length()` | Returns character count | `"Java".length()` → `4` | No normal invalid-index case | No index |
| `isEmpty()` | Checks whether length is `0` | `"".isEmpty()` → `true` | `" ".isEmpty()` → `false` (not an error) | No index |
| `isBlank()` | Checks whether text is empty or whitespace-only | `"  ".isBlank()` → `true` | `"Java".isBlank()` → `false` (not an error) | Java 11+ |
| `charAt(i)` | Gets one character | `"Java".charAt(1)` → `'a'` | `"Java".charAt(4)` → `StringIndexOutOfBoundsException` | Valid index: `0` through `length()-1` |
| `substring(start)` | Extracts from start to end | `"Java".substring(2)` → `"va"` | `"Java".substring(5)` → `StringIndexOutOfBoundsException` | Start inclusive |
| `substring(start, end)` | Extracts a range | `"Java".substring(1, 3)` → `"av"` | `"Java".substring(1, 5)` → `StringIndexOutOfBoundsException` | Start inclusive, end exclusive |
| `subSequence(start, end)` | Returns a character sequence for a range | `"Java".subSequence(0, 2)` → `"Ja"` | `"Java".subSequence(0, 5)` → `StringIndexOutOfBoundsException` | Start inclusive, end exclusive |
| `toCharArray()` | Converts string to a character array | `"Cat".toCharArray()` → `{'C','a','t'}` | No normal invalid-index case | No index |

### B. Searching methods

| Method | Purpose | Successful example → result | Failure / no-match example | Index rule |
|---|---|---|---|---|
| `contains(seq)` | Checks whether text exists | `"Java".contains("av")` → `true` | `"Java".contains("xyz")` → `false` | No end index |
| `startsWith(prefix)` | Checks the beginning | `"Java".startsWith("Ja")` → `true` | `"Java".startsWith("va")` → `false` | No end index |
| `startsWith(prefix, offset)` | Checks a prefix from an offset | `"Java".startsWith("va", 2)` → `true` | `"Java".startsWith("va", 1)` → `false` | Offset is a starting position |
| `endsWith(suffix)` | Checks the ending | `"Java".endsWith("va")` → `true` | `"Java".endsWith("Ja")` → `false` | No end index |
| `indexOf(ch)` | Finds first character occurrence | `"Java".indexOf('a')` → `1` | `"Java".indexOf('z')` → `-1` | Returns index or `-1` |
| `indexOf(text)` | Finds first substring occurrence | `"Java".indexOf("av")` → `1` | `"Java".indexOf("xyz")` → `-1` | Returns starting index |
| `indexOf(text, from)` | Searches from an index | `"Java".indexOf("a", 2)` → `3` | `"Java".indexOf("a", 4)` → `-1` | Search begins at `from` |
| `lastIndexOf(ch)` | Finds last character occurrence | `"Java".lastIndexOf('a')` → `3` | `"Java".lastIndexOf('z')` → `-1` | Returns index or `-1` |
| `lastIndexOf(text)` | Finds last substring occurrence | `"banana".lastIndexOf("an")` → `3` | `"Java".lastIndexOf("xyz")` → `-1` | Returns starting index |

**Remember:** A missing search result usually returns `-1`; it is not an exception.

### C. Comparing strings

Assume `String a = "Java";` and `String b = "java";`.

| Method / operator | Purpose | Successful example → result | Different-value example | Index rule |
|---|---|---|---|---|
| `equals(obj)` | Compares contents, case-sensitive | `a.equals("Java")` → `true` | `a.equals("java")` → `false` | No index |
| `equalsIgnoreCase(str)` | Compares contents ignoring case | `a.equalsIgnoreCase(b)` → `true` | `a.equalsIgnoreCase("Python")` → `false` | No index |
| `compareTo(str)` | Lexicographic comparison | `"Apple".compareTo("Apple")` → `0` | `"Apple".compareTo("Banana")` → negative value | No index |
| `compareToIgnoreCase(str)` | Lexicographic comparison ignoring case | `"java".compareToIgnoreCase("JAVA")` → `0` | `"Java".compareToIgnoreCase("Python")` → negative value | No index |
| `==` | Compares object references | `a == a` → `true` | `new String("Java") == new String("Java")` → `false` | Not a method |

For `compareTo()`, negative means less than, zero means equal in comparison, and positive means greater than. The result is not guaranteed to be exactly `-1` or `1`.

### D. Transforming text

These methods do not modify the original `String`.

| Method | Purpose | Successful example → result | Failure / edge example | Index rule |
|---|---|---|---|---|
| `concat(str)` | Joins strings | `"Java".concat(" DSA")` → `"Java DSA"` | `"Java".concat(null)` → `NullPointerException` | No index |
| `toUpperCase()` | Converts to uppercase | `"Java".toUpperCase()` → `"JAVA"` | No ordinary input failure | No index |
| `toLowerCase()` | Converts to lowercase | `"JAVA".toLowerCase()` → `"java"` | No ordinary input failure | No index |
| `trim()` | Removes leading/trailing characters up to U+0020 | `"  Java  ".trim()` → `"Java"` | Does not remove internal spaces | No index |
| `strip()` | Removes leading/trailing Unicode whitespace | `"  Java  ".strip()` → `"Java"` | `"Java DSA".strip()` → `"Java DSA"` (not an error) | Java 11+ |
| `stripLeading()` | Removes leading Unicode whitespace | `"  Java".stripLeading()` → `"Java"` | `"Java".stripLeading()` → `"Java"` | Java 11+ |
| `stripTrailing()` | Removes trailing Unicode whitespace | `"Java  ".stripTrailing()` → `"Java"` | `"Java".stripTrailing()` → `"Java"` | Java 11+ |
| `replace(old, new)` | Replaces literal characters/sequences | `"banana".replace("a", "o")` → `"bonono"` | `"banana".replace("xyz", "o")` → `"banana"` | No index |
| `replaceFirst(regex, replacement)` | Replaces first regex match | `"a1a2".replaceFirst("\\d", "X")` → `"aXa2"` | `"abc".replaceFirst("[", "X")` → `PatternSyntaxException` | Regex, not index |
| `replaceAll(regex, replacement)` | Replaces all regex matches | `"a1b2".replaceAll("\\d", "X")` → `"aXbX"` | `"abc".replaceAll("[", "X")` → `PatternSyntaxException` | Regex, not index |
| `split(regex)` | Splits into an array | `"a,b,c".split(",")` → `["a","b","c"]` | `"abc".split(",")` → `["abc"]` | Regex, not index |
| `String.join(delimiter, elements)` | Joins multiple strings | `String.join("-", "a", "b")` → `"a-b"` | A null delimiter causes `NullPointerException` | No index |

**Note:** `trim()` and `strip()` differ. `strip()` uses the `Character.isWhitespace()` definition; `trim()` removes characters up to U+0020.

---

## 3. `StringBuilder` methods

Each example starts with a fresh builder unless otherwise shown.

| Method | Purpose | Successful example → result | Failure / edge example | Index rule |
|---|---|---|---|---|
| `length()` | Gets character count | `new StringBuilder("Java").length()` → `4` | No normal invalid-index case | No index |
| `capacity()` | Gets allocated capacity | `new StringBuilder("Java").capacity()` → `20` | No normal invalid-index case | No index |
| `append(str)` | Adds text at the end | `new StringBuilder("Java").append(" DSA")` → `"Java DSA"` | Appending a null `String` appends `"null"`; not an error | No index |
| `insert(offset, str)` | Inserts text at an offset | `new StringBuilder("Java").insert(1, "XX")` → `"JXXava"` | `.insert(5, "X")` → `StringIndexOutOfBoundsException` | Offset `0` through `length()`, inclusive |
| `delete(start, end)` | Removes a range | `new StringBuilder("Java").delete(1, 3)` → `"Ja"` | `.delete(3, 1)` → `StringIndexOutOfBoundsException` | Start inclusive, end exclusive |
| `deleteCharAt(index)` | Removes one character | `new StringBuilder("Java").deleteCharAt(1)` → `"Jva"` | `.deleteCharAt(4)` → `StringIndexOutOfBoundsException` | Valid index: `0` through `length()-1` |
| `replace(start, end, str)` | Replaces a range | `new StringBuilder("Java").replace(1, 3, "XY")` → `"JXYa"` | `.replace(3, 1, "X")` → `StringIndexOutOfBoundsException` | Start inclusive, end exclusive |
| `reverse()` | Reverses characters | `new StringBuilder("Java").reverse()` → `"avaJ"` | No ordinary input failure | No index |
| `charAt(index)` | Gets a character | `new StringBuilder("Java").charAt(2)` → `'v'` | `.charAt(4)` → `StringIndexOutOfBoundsException` | Valid index: `0` through `length()-1` |
| `setCharAt(index, ch)` | Changes one character | `new StringBuilder("Java").setCharAt(0, 'L')` → `"Lava"` | `.setCharAt(4, 'L')` → `StringIndexOutOfBoundsException` | Valid index: `0` through `length()-1` |
| `substring(start)` | Returns a `String` from start to end | `new StringBuilder("Java").substring(2)` → `"va"` | `.substring(5)` → `StringIndexOutOfBoundsException` | Start inclusive |
| `substring(start, end)` | Returns a `String` for a range | `new StringBuilder("Java").substring(1, 3)` → `"av"` | `.substring(1, 5)` → `StringIndexOutOfBoundsException` | Start inclusive, end exclusive |
| `indexOf(str)` | Finds first substring occurrence | `new StringBuilder("Java").indexOf("av")` → `1` | `.indexOf("xyz")` → `-1` | Returns index or `-1` |
| `lastIndexOf(str)` | Finds last substring occurrence | `new StringBuilder("banana").lastIndexOf("an")` → `3` | `.lastIndexOf("xyz")` → `-1` | Returns index or `-1` |
| `setLength(n)` | Changes logical length | `new StringBuilder("Java").setLength(2)` → `"Ja"` | `.setLength(-1)` → `StringIndexOutOfBoundsException` | Length must be nonnegative |
| `ensureCapacity(n)` | Ensures minimum capacity | `new StringBuilder().ensureCapacity(100)` | Negative values do not normally cause an exception | Capacity, not index |
| `toString()` | Produces a `String` snapshot | `new StringBuilder("Java").toString()` → `"Java"` | No ordinary input failure | No index |

### Range behavior to know

For `delete()` and indexed `replace()`, an end index greater than the current length is clamped to the current length. An invalid start index or a start greater than the end can throw an exception.

```java
StringBuilder sb = new StringBuilder("Java");
sb.delete(1, 100);
System.out.println(sb);
```

Output:

```text
J
```

---

## 4. `StringBuffer` methods

`StringBuffer` provides almost the same core methods as `StringBuilder`, but its methods are synchronized.

| Method | Purpose | Successful example → result | Failure / edge example | Index rule |
|---|---|---|---|---|
| `length()` | Gets character count | `new StringBuffer("Java").length()` → `4` | No normal invalid-index case | No index |
| `capacity()` | Gets allocated capacity | `new StringBuffer("Java").capacity()` → `20` | No normal invalid-index case | No index |
| `append(str)` | Adds text at the end | `new StringBuffer("Java").append(" DSA")` → `"Java DSA"` | Appending a null `String` appends `"null"`; not an error | No index |
| `insert(offset, str)` | Inserts text at an offset | `new StringBuffer("Java").insert(1, "XX")` → `"JXXava"` | `.insert(5, "X")` → `StringIndexOutOfBoundsException` | Offset `0` through `length()`, inclusive |
| `delete(start, end)` | Removes a range | `new StringBuffer("Java").delete(1, 3)` → `"Ja"` | `.delete(3, 1)` → `StringIndexOutOfBoundsException` | Start inclusive, end exclusive |
| `deleteCharAt(index)` | Removes one character | `new StringBuffer("Java").deleteCharAt(1)` → `"Jva"` | `.deleteCharAt(4)` → `StringIndexOutOfBoundsException` | Valid index: `0` through `length()-1` |
| `replace(start, end, str)` | Replaces a range | `new StringBuffer("Java").replace(1, 3, "XY")` → `"JXYa"` | `.replace(3, 1, "X")` → `StringIndexOutOfBoundsException` | Start inclusive, end exclusive |
| `reverse()` | Reverses characters | `new StringBuffer("Java").reverse()` → `"avaJ"` | No ordinary input failure | No index |
| `charAt(index)` | Gets a character | `new StringBuffer("Java").charAt(2)` → `'v'` | `.charAt(4)` → `StringIndexOutOfBoundsException` | Valid index: `0` through `length()-1` |
| `setCharAt(index, ch)` | Changes one character | `new StringBuffer("Java").setCharAt(0, 'L')` → `"Lava"` | `.setCharAt(4, 'L')` → `StringIndexOutOfBoundsException` | Valid index: `0` through `length()-1` |
| `substring(start, end)` | Returns a `String` for a range | `new StringBuffer("Java").substring(1, 3)` → `"av"` | `.substring(1, 5)` → `StringIndexOutOfBoundsException` | Start inclusive, end exclusive |
| `indexOf(str)` | Finds first substring occurrence | `new StringBuffer("Java").indexOf("av")` → `1` | `.indexOf("xyz")` → `-1` | Returns index or `-1` |
| `lastIndexOf(str)` | Finds last substring occurrence | `new StringBuffer("banana").lastIndexOf("an")` → `3` | `.lastIndexOf("xyz")` → `-1` | Returns index or `-1` |
| `setLength(n)` | Changes logical length | `new StringBuffer("Java").setLength(2)` → `"Ja"` | `.setLength(-1)` → `StringIndexOutOfBoundsException` | Length must be nonnegative |
| `ensureCapacity(n)` | Ensures minimum capacity | `new StringBuffer().ensureCapacity(100)` | Negative values do not normally cause an exception | Capacity, not index |
| `toString()` | Produces a `String` snapshot | `new StringBuffer("Java").toString()` → `"Java"` | No ordinary input failure | No index |

The same range rules described for `StringBuilder` generally apply to `StringBuffer`.

---

## 5. Inclusive vs. exclusive: quick revision

| Expression | Meaning |
|---|---|
| `charAt(i)` | Accesses exactly index `i` |
| `substring(a, b)` | Includes `a`, excludes `b` |
| `subSequence(a, b)` | Includes `a`, excludes `b` |
| `delete(a, b)` | Deletes from `a` up to, but not including, `b` |
| `replace(a, b, text)` in builders | Replaces from `a` up to, but not including, `b` |
| `insert(i, text)` | Inserts before the existing character at `i`; `i == length()` appends |
| `indexOf(text)` | Returns the starting index of the first match |
| `lastIndexOf(text)` | Returns the starting index of the last match |

### Example

```java
String s = "ABCDE";
System.out.println(s.substring(1, 4));
```

Output:

```text
BCD
```

Indexes selected are `1`, `2`, and `3`. Index `4` is excluded.

```text
Character:  A  B  C  D  E
Index:      0  1  2  3  4
                [-----)
             start=1, end=4
```

**Final distinction:** An unsuccessful search is not the same as an invalid index. A missing substring returns `-1`; accessing an invalid index generally throws `StringIndexOutOfBoundsException`.

## Recommended methods to prioritize for DSA

- **String:** `length()`, `charAt()`, `substring()`, `equals()`, `indexOf()`, `lastIndexOf()`, `replace()`, `split()`, `toCharArray()`
- **StringBuilder:** `append()`, `delete()`, `insert()`, `replace()`, `reverse()`, `charAt()`, `setCharAt()`, `toString()`
- **StringBuffer:** Learn the corresponding builder methods and understand synchronization.
