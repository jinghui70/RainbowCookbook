# 数据类型转换

把数据从一种类型转为另一种类型，这是一个普遍的需求，因此 Converters 类提供了下面的转换函数：

    <F, T> T convert(F from, Class<T> toClass);

目前支持以下类型的数据转换：

    Boolean2Number
    Date2Long
    Date2SqlDate
    Date2Timestamp
    Enum2Number
    Enum2String
    LocalDate2SqlDate
    LocalDate2Timestamp
    LocalDateTime2Timestamp
    Number2BigDecimal
    Number2Boolean
    Number2Date
    Number2Enum
    Number2Double
    Number2Int
    Number2Long
    Number2Short
    Object2String
    SqlDate2LocalDate
    String2BigDecimal
    String2Boolean
    String2Date
    String2Double
    String2Enum
    String2Int
    String2Long
    String2Short
    String2SqlDate
    String2Timestamp
    String2LocalDate
    String2LocalDateTime
    Timestamp2Date
    Timestamp2LocalDate
    Timestamp2LocalDateTime

另外，还支持 Java Bean 与 Map 的互转：

    <T> Map<String, Object> object2Map(T object);
    <T> T map2Object(Map<String, Object> map, Class<T> clazz);
