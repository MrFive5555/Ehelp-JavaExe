# 把文字中所有颜色的词换成相应的颜色
## 样例
> ```html
> 牛顿采用三棱镜把太阳光分解成红色、橙色、黄色、绿色等七种颜色的光
> ->
> 牛顿采用三棱镜把太阳光分解成<red>红色<\red>、<orange>橙色<\orange>、<yellow>黄色><\yellow>、<green>绿色<\green>等七种颜色的光
> ```
```java
private static String REGEX = "红色";
private static String REPLACE = "<red>红色<\\red>";
private static String INPUT = "红色的红领巾在飘扬";
public static void main(String[] args) {
  Pattern p = Pattern.compile(REGEX);
  // 获取 matcher 对象
  Matcher m = p.matcher(INPUT);
  System.out.println(m.replaceAll(REPLACE));
}
```
## 反斜杠陷阱
小心"\\"在正则表达式和replacement中的行为
下面这段代码会有意料之外的结果
```java
private static String REGEX = "a";
private static String INPUT = "abc";
private static String REPLACE = "\\a";
public static void main(String[] args) {
  // 正则表达式形式
  Pattern p = Pattern.compile(REGEX);
  Matcher m = p.matcher(INPUT);
  System.out.println(m.replaceAll(REPLACE));
  // 直接替换的形式
  System.out.println(INPUT.replace("a", "\\a"));
  System.out.println(INPUT.replaceFirst("a", "\\a"));
  System.out.println(INPUT.replaceAll("a", "\\a"));
}
```
有几个需要注意的要点是：
## 可扩展性，
在新增加一种颜色之后，应该能很方便地增加，因此下面这种风格的代码不应该出现
```java
private static String REGEX_RED = "红色";
private static String REPLACE_RED = "<red>红色<\\red>";
private static String REGEX_BLUE = "蓝色";
private static String REPLACE_BLUE = "<blue>蓝色<\\blue>";
private static String REGEX_YELLOW = "黄色";
private static String REPLACE_YELLOW = "<yellow>黄色<\\yellow>";
// .....
```
最好的方法是，构造配置文件，把对应颜色和需要替换的标签名记录到独立的文件里
```
红色 red
蓝色 blue
黄色 yellow
\\d+ number
\\w+@\\w+.com email
```
## 效率
如果字典的项目数量为m，文本长度为n，替换的时间复杂度为O(mn)。当字典大小非常大的时候，这个开销非常大，请设计相应的机制减少这种开销