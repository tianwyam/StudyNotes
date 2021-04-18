## Java 8 新的日期时间API



[TOC]





Java8对日期时间的处理提供了新的API，在 java.time包下面

新的java.time包涵盖了所有处理日期，时间，日期/时间，时区，时刻（instants），过程（during）与时钟（clock）的操作。

包括：新老日期时间之间的转换
如：date 和 LocalDateTime、LocalTime、LocalDate、Instant 之间的转换





### API



Java 8 对日期时间处理提供了两个比较重要的API：

Local（本地），简化了日期处理，没有时区

Zoned（时区），制定时区

| 时区有无 | Local（无时区） | Zoned（时区） |
| -------- | --------------- | ------------- |
| 日期     | LocalDate       |               |
| 日期时间 | LocalDateTime   | ZonedDateTime |
| 时间     | LocalTime       |               |
| 时刻     | Instant         |               |



### 简单使用



#### LocalDateTime



##### 获取当前日期时间

~~~java
// 获取 当前的 日期时间
LocalDateTime time = LocalDateTime.now();
// 2021-04-18T10:16:20.630
System.out.println(time);
~~~



##### 格式化输出

~~~java

// 日期时间格式化
DateTimeFormatter format = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
// 2021-04-18 10:16:20
System.out.println(time.format(format));
~~~



##### 解析字符串时间

~~~java
// 字符串时间的格式，必须保持一致
// 转 LocalDateTime 必须是 日期+时间
DateTimeFormatter dateFormat = DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm");
// 对字符串时间，进行转换
LocalDateTime date = LocalDateTime.parse("2020/08/01 10:30", dateFormat);
// 2020-08-01T10:30
System.out.println(date);
~~~



##### 自定义日期时间

~~~java
// 自定义时间日期
LocalDateTime customDateTime = LocalDateTime.of(2021, 10, 01, 8, 0, 0);
// 2021-10-01T08:00
System.out.println(customDateTime);
~~~



##### 转LocalDate

~~~java
// 转LocalDate 只有日期
LocalDate localDate = time.toLocalDate();
// 2021-04-18
System.out.println(localDate);
~~~



##### 转LocalTime

~~~java
// 转LocalTime 只有时间
LocalTime localTime = time.toLocalTime();
// 10:37:42.419
System.out.println(localTime);
~~~



##### 转Instant

~~~java
// 转Instant
// time: 2021-04-18T10:48:29.919
Instant instant = time.toInstant(ZoneOffset.UTC);
// 2021-04-18T10:48:29.919Z
System.out.println(instant);

//使用默认时区 +08:00 = ZoneOffset.UTC
ZoneOffset offset = OffsetDateTime.now().getOffset();
// +08:00
System.out.println(offset);
// 2021-04-18T02:48:29.919Z
System.out.println(time.toInstant(offset));
~~~



##### 转Date

~~~java
// 先转成 Instant
ZoneOffset zoneOffset = OffsetDateTime.now().getOffset();
Instant instant2 = time.toInstant(zoneOffset);
// 获取毫秒值
long milli = instant2.toEpochMilli();
// 转Date
Date date = new Date(milli);
// Sun Apr 18 11:08:06 CST 2021
System.out.println(date);
// 格式化 Date 输出格式
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
// 2021-04-18 11:08:06
System.out.println(simpleDateFormat.format(date));
~~~



##### Date 转 LocalDateTime

~~~java
// Date 转 LocalDateTime
// Date 先转成 时间
Instant instant3 = new Date().toInstant();
// 根据默认时区转
LocalDateTime dateTime = LocalDateTime.ofInstant(instant3, ZoneId.systemDefault());
// 2021-04-18T22:01:56.723
System.out.println(dateTime);
~~~







#### LocalDate

日期



##### 获取当前日期

~~~java
// 获取 当前的 日期，没有时间
LocalDate localDate = LocalDate.now();
// 2021-04-18
System.out.println(localDate);
~~~



##### 自定义日期

~~~java
// 自定义日期
LocalDate customLocalDate = LocalDate.of(2021, 10, 1);
// 2021-10-01
System.out.println(customLocalDate);
~~~



##### 格式化输出

~~~java
// 定义格式
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日");
// 格式化
String format = dateTimeFormatter.format(localDate);
// 2021年04月18日
System.out.println(format);
~~~



##### 解析字符串日期

~~~java
// 格式 必须与 字符串时间格式一致
DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy年M月d日");
// 解析字符串
LocalDate parseStrDate = LocalDate.parse("2020年7月10日", dateTimeFormatter);
// 2020-07-10
System.out.println(parseStrDate);
~~~



##### 转LocalDateTime

~~~java
// 转 LocalDateTime
// 需要添加时间 时分秒
LocalDateTime localDateTime = localDate.atTime(LocalTime.now());
// 2021-04-18T21:41:16.195
System.out.println(localDateTime);
~~~



##### 转 instant

~~~java
// 转 Instant
// 根据默认时区 转时刻
Instant instant = localDate.atStartOfDay().atZone(ZoneId.systemDefault()).toInstant();
~~~







##### 转Date

~~~java

// 转 Date
// 先转 LocalDateTime 获取时区
// 在转 Instant
Instant instant = localDate.atStartOfDay().atZone(ZoneId.systemDefault()).toInstant();
// Instant 转 Date
Date date = Date.from(instant);
// 没有时分秒
// Sun Apr 18 00:00:00 CST 2021
System.out.println(date);
~~~



##### Date 转 LocalDate

~~~java
// Date 转 LocalDate
// 先转成时刻
Instant instant = new Date().toInstant();
// 在根据默认时区 转成 LocalDateTime
LocalDateTime dateTime = LocalDateTime.ofInstant(instant, ZoneId.systemDefault());
// LocalDateTime 转 LocalDate
LocalDate localDate = dateTime.toLocalDate();
// 2021-04-18
System.out.println(localDate);
~~~



#### LocalTime



时间



##### 获取当前时间

~~~java
// 获取 当前的 时间，没有日期
LocalTime localTime = LocalTime.now();
// 22:07:55.425
System.out.println(localTime);
~~~



##### 自定义时间

~~~java
// 自定义时间
LocalTime customTime = LocalTime.of(12, 30, 10);
// 12:30:10
System.out.println(customTime);
~~~



##### 解析字符串时间

~~~java
// 解析字符串
LocalTime parse = LocalTime.parse("14点20分30秒", DateTimeFormatter.ofPattern("HH点mm分ss秒"));
// 14:20:30
System.out.println(parse);
~~~



##### 格式化输出

~~~java
// 格式化输出
String format = localTime.format(DateTimeFormatter.ofPattern("HH点mm分ss秒"));
// 22点18分58秒
System.out.println(format);
~~~



##### 转 LocalDateTime

~~~java
LocalDateTime localDateTime = localTime.atDate(LocalDate.now());
// 2021-04-18T22:23:51.692
System.out.println(localDateTime);
~~~



##### 转Date

~~~java

// 转 Date
LocalDateTime dateTime = LocalDateTime.of(LocalDate.now(), localTime);
Instant instant = dateTime.toInstant(OffsetDateTime.now().getOffset());
Date date = Date.from(instant);
// Sun Apr 18 22:30:54 CST 2021
System.out.println(date);
~~~



##### Date 转 LocalTime

~~~java
// Date 转 LocalTime
Instant instant2 = new Date().toInstant();
LocalDateTime localDateTime2 = LocalDateTime.ofInstant(instant2, ZoneId.systemDefault());
LocalTime localTime2 = localDateTime2.toLocalTime();
// 22:33:26.249
System.out.println(localTime2);
~~~





#### Instant

时刻 时间戳



##### 获取当前时刻

~~~java
// 获取当前时刻
Instant instant = Instant.now();
// 2021-04-18T14:35:40.372Z
System.out.println(instant);
~~~



##### 转 LocalDateTime

~~~java
LocalDateTime localDateTime = LocalDateTime.ofInstant(instant, ZoneId.systemDefault());
// 2021-04-18T22:37:06.950
System.out.println(localDateTime);
~~~



##### 转Date

~~~java
Date date = Date.from(instant);
// Sun Apr 18 22:38:27 CST 2021
System.out.println(date);
~~~



##### Date 转 Instant

~~~java
Instant instant2 = new Date().toInstant();
// 2021-04-18T14:40:01.481Z
System.out.println(instant2);
~~~



