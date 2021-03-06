---
layout: post
title: 正则表达式学习（一）：元字符与转义
Date:  2017-2-28
author: "Leo Qi"
catalog: true
tags:
    - 正则表达式
---
> 让一个人成年累月地处于不确定之中，霸占了他们生命中可以用来干其他事的宝贵时间，最终落得一事无成——那才是真正的冷酷无情    ——《从优秀到卓越》

### 什么是正则表达式 ###

正则表达式（regular expression）就是用一个“字符串”来描述一个特征，然后去验证另一个“字符串”是否符合这个特征。比如 表达式“ab+” 描述的特征是“一个 'a' 和 任意个 'b' ”，那么 'ab', 'abb', 'abbbbbbbbbb' 都符合这个特征。

正则表达式可以用来：
1. 验证字符串是否符合指定特征，比如验证是否是合法的邮件地址。
2. 用来查找字符串，从一个长的文本中查找符合指定特征的字符串，比查找固定字符串更加灵活方便。
3. 用来替换，比普通的替换更强

### 元字符 ###

元字符（meta-character）并不匹自身对应的字符，而具有其他的含义。

|元字符 | 说明 | 举例
|-----|-----|-----
|^    |匹配整个字符串的起始位置，或者行的起始位置，如果在字符串组内，则表示排除型（negative）字符组 | ^start
|$    |匹配整个字符串的结束为止，或者行的结束位置| End$
|()   |分组，提供反向引用（group1）\1或多个分支选项 | (ab)+
| * + ? | 量词，限定之前的元素出现的次数 | a+(ab)+
|.    | 默认情况下匹配换行符之外的任意字符，在多行模式下可以匹配换行符 ||
|[    | 字符组的起始符号 | [1-9] |
| \   | 转义序列，或去掉元字符的转义 | \1  \\\w
| {   | 重现限定符的开始 | {2,6}
| &#124;   | 划分多选分支（括号没出现时，可以想象括号出现在整个表达式最外层| Tom&#124; Jerry

这些元字符并不是“对称”出现的，比如与开方括号 [ 对应的闭方括号 ]，与开花括号 { 对应的闭花括号 } ，这两个字符是否元字符，需要依据具体正则表达式的情况确定，我们以闭方括号]的情况为例（}的情况与此类似）：如果之前能找到与之对应的元字符开方括号[，则]作为元字符出现，否则，作为普通字符出现。

|表达式\字符串 | [ab]] | ab]
|------|-----|-----
|a] | √ | |
|ab] | |  √  

另外，因为方括号本身可以表示字符组『[0-9]』，所以在字符组内部的闭方括号在任何情况下都要转义，否则类似『[]]』的正则表达式会出现二义性，造成识别错误。
如果需要匹配方括号内（包括方括号），至少包含一个字符的字符串（比如[text]），所用的正则表达式就应该是：『\[[^\]]+]』。

『\[』匹配开方括号，然后用一个排除型字符组匹配“除闭方括号 ] 之外的任意字符（注意，在字符组内部，闭方括号 ] 一定需要转义），用『+』表示它至少要出现一次以上，最后用一个『]』匹配闭方括号。

java中转义序列（escape sequence）用来表示特殊字符，比如\n表示换行符，\t表示制表符，而\[并不是Java能识别的转义序列。为了表示“正则表达式中的\[”，我们传递给Pattern.compile()的字符串必须正确表示\[——在字符串中，[ 是不需要转义的，而 \ 是需要转义的，所以在字符串中，应该写做 \\[。

| 字符串的表现层|\\[
|----| ----
|字符串的概念层    | \[
|正则表达式的表现层 | \[
|正则表达式的概念层 | [（非元字符）

这也是为什么正则表达式的转义序列在正则表达式中要写两个反斜线了，比如 \+ 要写成 \\+ 。但是 \n 之类的有点特殊，无论你写成 \n 或是 \\n ，结果都是一样，\t之类的情况与此类似。

|字符串的表现层  | \\n | \n|
| ----- | ----- | ----- |
|字符串的概念层  | \n | 换行符|
|正则表达式的表现层 | \[ | 换行符|
|正则表达式的概念层 | 换行符 | 换行符|

如果字符串中表示反斜线字符本身（不是用来转义的符号），则需要在正则表达式中写四个反斜线字符。

"\".matches("\\\\"); //true

|字符串的表现层 |\\\\\\\
| ----- | ----- |
|字符串的概念层|\\\
|正则表达式的表现层|\\\
|正则表达式的概念层|\（非元字符）

### 正则表达式全部符号解释 ###

摘自[维基百科 https://zh.wikipedia.org/wiki/%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F](https://zh.wikipedia.org/wiki/%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)

|字符|描述
| ----- | -----
|\ |	将下一个字符标记为一个特殊字符、或一个原义字符、或一个 向后引用、或一个八进制转义符。例如，'n' 匹配字符 "n"。'\n' 匹配一个换行符。序列 '\\' 匹配 "\" 而 "\(" 则匹配 "("。
|^|	匹配输入字符串的开始位置。如果设置了 RegExp 对象的 Multiline 属性，^ 也匹配 '\n' 或 '\r' 之后的位置。
|$|	匹配输入字符串的结束位置。如果设置了RegExp 对象的 Multiline 属性，$ 也匹配 '\n' 或 '\r' 之前的位置。
|*|	匹配前面的子表达式零次或多次。例如，zo* 能匹配 "z" 以及 "zoo"。* 等价于{0,}。
|+|	匹配前面的子表达式一次或多次。例如，'zo+' 能匹配 "zo" 以及 "zoo"，但不能匹配 "z"。+ 等价于 {1,}。
|?|	匹配前面的子表达式零次或一次。例如，"do(es)?" 可以匹配 "do" 或 "does" 中的"do" 。? 等价于 {0,1}。
|{n}|	n 是一个非负整数。匹配确定的 n 次。例如，'o{2}' 不能匹配 "Bob" 中的 'o'，但是能匹配 "food" 中的两个 o。
|{n,}|	n 是一个非负整数。至少匹配n 次。例如，'o{2,}' 不能匹配 "Bob" 中的 'o'，但能匹配 "foooood" 中的所有 o。'o{1,}' 等价于 'o+'。'o{0,}' 则等价于 'o*'。
|{n,m}|	m 和 n 均为非负整数，其中n <= m。最少匹配 n 次且最多匹配 m 次。例如，"o{1,3}" 将匹配 "fooooood" 中的前三个 o。'o{0,1}' 等价于 'o?'。请注意在逗号和两个数之间不能有空格。
|?|	当该字符紧跟在任何一个其他限制符 (*, +, ?, {n}, {n,}, {n,m}) 后面时，匹配模式是非贪婪的。非贪婪模式尽可能少的匹配所搜索的字符串，而默认的贪婪模式则尽可能多的匹配所搜索的字符串。例如，对于字符串 "oooo"，'o+?' 将匹配单个 "o"，而 'o+' 将匹配所有 'o'。
|.|	匹配除 "\n" 之外的任何单个字符。要匹配包括 '\n' 在内的任何字符，请使用象 '[.\n]' 的模式。
|(pattern)|	匹配 pattern 并获取这一匹配。所获取的匹配可以从产生的 Matches 集合得到，在VBScript 中使用 SubMatches 集合，在JScript 中则使用 $0…$9 属性。要匹配圆括号字符，请使用 '\(' 或 '\)'。
|(?:pattern)|	匹配 pattern 但不获取匹配结果，也就是说这是一个非获取匹配，不进行存储供以后使用。这在使用 "或" 字符 (&#124;) 来组合一个模式的各个部分是很有用。例如， 'industr(?:y&#124;ies) 就是一个比 'industry&#124;industries' 更简略的表达式。
|(?=pattern)|	正向预查，在任何匹配 pattern 的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。例如，'Windows (?=95&#124;98&#124;NT&#124;2000)' 能匹配 "Windows 2000" 中的 "Windows" ，但不能匹配 "Windows 3.1" 中的 "Windows"。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始。
|(?!pattern)|	负向预查，在任何不匹配 pattern 的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。例如'Windows (?!95&#124;98&#124;NT&#124;2000)' 能匹配 "Windows 3.1" 中的 "Windows"，但不能匹配 "Windows 2000" 中的 "Windows"。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始
|x&#124;y|	匹配 x 或 y。例如，'z&#124;food' 能匹配 "z" 或 "food"。'(z&#124;f)ood' 则匹配 "zood" 或 "food"。
|[xyz]|	字符集合。匹配所包含的任意一个字符。例如， '[abc]' 可以匹配 "plain" 中的 'a'。
|[^xyz]|	负值字符集合。匹配未包含的任意字符。例如， '[^abc]' 可以匹配 "plain" 中的'p'。
|[a-z]|	字符范围。匹配指定范围内的任意字符。例如，'[a-z]' 可以匹配 'a' 到 'z' 范围内的任意小写字母字符。
|[^a-z]|	负值字符范围。匹配任何不在指定范围内的任意字符。例如，'[^a-z]' 可以匹配任何不在 'a' 到 'z' 范围内的任意字符。
|\b|	匹配一个单词边界，也就是指单词和空格间的位置。例如， 'er\b' 可以匹配"never" 中的 'er'，但不能匹配 "verb" 中的 'er'。
|\B|	匹配非单词边界。'er\B' 能匹配 "verb" 中的 'er'，但不能匹配 "never" 中的 'er'。
|\cx|	匹配由 x 指明的控制字符。例如， \cM 匹配一个 Control-M 或回车符。x 的值必须为 A-Z 或 a-z 之一。否则，将 c 视为一个原义的 'c' 字符。
|\d|	匹配一个数字字符。等价于 [0-9]。
|\D|	匹配一个非数字字符。等价于 [^0-9]。
|\f|	匹配一个换页符。等价于 \x0c 和 \cL。
|\n|	匹配一个换行符。等价于 \x0a 和 \cJ。
|\r|	匹配一个回车符。等价于 \x0d 和 \cM。
|\s|	匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。
|\S|	匹配任何非空白字符。等价于 [^ \f\n\r\t\v]。
|\t|	匹配一个制表符。等价于 \x09 和 \cI。
|\v|	匹配一个垂直制表符。等价于 \x0b 和 \cK。
|\w|	匹配包括下划线的任何单词字符。等价于'[A-Za-z0-9_]'。
|\W|	匹配任何非单词字符。等价于 '[^A-Za-z0-9_]'。
|\xn|	匹配 n，其中 n 为十六进制转义值。十六进制转义值必须为确定的两个数字长。例如，'\x41' 匹配 "A"。'\x041' 则等价于 '\x04' & "1"。正则表达式中可以使用 ASCII 编码。.
|\num|	匹配 num，其中 num 是一个正整数。对所获取的匹配的引用。例如，'(.)\1' 匹配两个连续的相同字符。
|\n|	标识一个八进制转义值或一个向后引用。如果 \n 之前至少 n 个获取的子表达式，则 n 为向后引用。否则，如果 n 为八进制数字 (0-7)，则 n 为一个八进制转义值。
|\nm|	标识一个八进制转义值或一个向后引用。如果 \nm 之前至少有 nm 个获得子表达式，则 nm 为向后引用。如果 \nm 之前至少有 n 个获取，则 n 为一个后跟文字 m 的向后引用。如果前面的条件都不满足，若 n 和 m 均为八进制数字 (0-7)，则 \nm 将匹配八进制转义值 nm。
|\nml|	如果 n 为八进制数字 (0-3)，且 m 和 l 均为八进制数字 (0-7)，则匹配八进制转义值 nml。
|\un|	匹配 n，其中 n 是一个用四个十六进制数字表示的 Unicode 字符。例如， \u00A9 匹配版权符号 (?)。
