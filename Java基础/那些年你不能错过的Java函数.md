#### 字符串 API

> Java中的String类包含了 50 多个方法，令人惊讶的是绝大多数都很有用，可以设想使用的频率非常高，下面的API注释汇总了一部分最常用的方法。

##### **String**

- char chartAt（int index）

返回给定位置的字符。

- int codePointAt(int index)

返回从给定位置开始或结束的代码点

- int offsetByCodePoint(int startIndex, int cpCount)

返回从startIndex代码点开始，位移cpCount后的代码点索引

- int compareTo(String other)

按照字典顺序，如果字符串位于other之前，返回一个负数；如果字符串位于other之后，返回一个正数；如果两个字符串相等，返回0

- boolean endsWith(String suffix)

如果字符串以suffix结尾，返回true

- boolean startWith(String prefix)

如果字符串以prefix开头，返回true

- boolean equal(Object other)

如果字符串与other相等，返回true

- boolean equalsIgnoreCase(String other)

如果字符串与other相等（忽略大小写），返回true

- int indexOf(String str)
- int indexOf(String str, int fromIndex)
- int indexOf(int cp)
- int indexOf(int cp, int fromIndex)

返回与字符串str或代码点cp匹配的最后一个子串的开始位置。这个位置从索引0或fromIndex开始计算。如果在原始串中不存在str，返回-1.

- int lastIndexOf(String str)
- int lastIndexOf(String str, int fromIndex)
- int lastIndexOf(int cp)
- int lastIndexOf(int cp, int fromIndex)

返回与字符串str或代码点cp匹配的最后一个子串的开始位置。这个位置从原始串尾端或fromIndex开始计算。

- int length()

返回字符串的长度

- int codePointCount(int startIndex, int endIndex)

返回 startIndex 和 endIndex 之间的代码点数量，没有配成对的代用字符数将计入代码点

- String replace(CharSequence oldString, CharSequence newString)

返回一个新字符串，这个字符串用 newString 代替原始字符串中所有的 oldString，可以用 String 或 StringBuilder 对象作为 CharSequence 参数。

- boolean subString(int beginIndex)
- boolean subString(int beginIndex, int endIndex)

返回一个新字符串。这个字符串包含原始字符串中从 beginIndex 到串尾或 endIndex - 1 的所有代码单元。

- String toLowerCase()

返回一个新字符串。这个字符串将原始字符串中的所有大写字母改成了小写字母。

- String toUpperCase()

返回一个新字符串。这个字符串将原始字符串中的所有小写字母改成了大写字母。

- String trim()

返回一个新字符串。这个字符串将删除了原始字符串头部和尾部的空格。

##### **StringBuffer/StringBuilder**

- int length()

返回构建器或缓冲器中的代码单元数量。

- StringBuilder append(String str)

追加一个字符串并返回this

- StringBuilder append(char c)

追加一个代码单元并返回this

- StringBuilder appendCodePoint(int cp)

追加一个代码点，并将其转换为一个或两个代码单元并返回 this

- void setCharAt(int i, char c)

将第 i 个代码单元设置为 c

- StringBuilder insert(int offset, String str)

在 offset 位置插入一个字符串并返回 this

- StringBuilder delete(int startIndex, int endIndex)

删除偏移量从 startIndex 到 endIndex 的代码单元并返回 this

- String toString()

返回一个与构建器或缓冲器内容相同的字符串