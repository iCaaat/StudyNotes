# MySQL 8.0 数据类型与 Java 数据类型对应关系

## 数值类型

| **MySQL 数据类型**    | `Java 数据类型`        | 说明                                              |
| --------------------- | ---------------------- | ------------------------------------------------- |
| **TINYINT**           | `byte` 或 `short`      | 如果未定义 UNSIGNED 则使用 `byte`，否则用 `short` |
| **SMALLINT**          | `short` 或 `int`       | 如果未定义 UNSIGNED 则使用 `short`，否则用 `int`  |
| **MEDIUMINT**         | `int`                  | 对应有符号整数，3字节范围                         |
| **INT / INTEGER**     | `int`                  | 标准 4 字节整数                                   |
| **BIGINT**            | `long`                 | 8 字节的长整数                                    |
| **DECIMAL / NUMERIC** | `java.math.BigDecimal` | 精确表示的定点数                                  |
| **FLOAT**             | `float`                | 单精度浮点数                                      |
| **DOUBLE / REAL**     | `double`               | 双精度浮点数                                      |

---

## 日期和时间类型

| **MySQL 数据类型** | `Java 数据类型`      | 说明                       |
| ------------------ | -------------------- | -------------------------- |
| **DATE**           | `java.sql.Date`      | 不带时间的日期（年-月-日） |
| **TIME**           | `java.sql.Time`      | 不带日期的时间（时:分:秒） |
| **DATETIME**       | `java.sql.Timestamp` | 带时间戳的日期时间         |
| **TIMESTAMP**      | `java.sql.Timestamp` | 自动更新或存储时间         |
| **YEAR**           | `short` 或 `int`     | 表示年份                   |

---

## 字符和文本类型

| **MySQL 数据类型**          | `Java 数据类型` | 说明                 |
| --------------------------- | --------------- | -------------------- |
| **CHAR(n)**                 | `String`        | 固定长度字符串       |
| **VARCHAR(n)**              | `String`        | 可变长度字符串       |
| **TEXT (TINY/MEDIUM/LONG)** | `String`        | 大型文本字段         |
| **ENUM**                    | `String`        | 枚举值               |
| **SET**                     | `String`        | 多选值（以逗号分隔） |

---

## 二进制类型

| **MySQL 数据类型**          | `Java 数据类型` | 说明               |
| --------------------------- | --------------- | ------------------ |
| **BINARY(n)**               | `byte[]`        | 固定长度二进制数据 |
| **VARBINARY(n)**            | `byte[]`        | 可变长度二进制数据 |
| **BLOB (TINY/MEDIUM/LONG)** | `byte[]`        | 大型二进制数据     |

---

## 注意事项

1. **溢出问题**：对于 UNSIGNED 类型需要额外注意，Java 中没有无符号类型，如果 MySQL 使用 UNSIGNED 并超出对应 Java 类型的范围，则需要进行类型转换。
2. **`BigDecimal` 使用**：对于高精度需求（如金钱处理），应使用 `BigDecimal` 而不是浮点类型。
3. **时间类型的兼容性**：推荐使用 Java 8 的日期时间类（`LocalDate`、`LocalTime` 和 `LocalDateTime`），通过工具类如 `java.time` 或 JDBC 驱动的转换支持。

---

---

# Oracle数据类型与Java数据类型对应关系

| **Oracle 数据类型**     | **对应的 Java 类型**                         | **说明**                                                     |
| ----------------------- | -------------------------------------------- | ------------------------------------------------------------ |
| `VARCHAR2`, `CHAR`      | `java.lang.String`                           | 字符串类型                                                   |
| `NVARCHAR2`, `NCHAR`    | `java.lang.String`                           | Unicode 字符串                                               |
| `CLOB`, `NCLOB`         | `java.sql.Clob` / `java.lang.String`（可读） | 大字段字符类型                                               |
| `NUMBER(p,s)`           | `java.math.BigDecimal`                       | 精确数值，适合财务等场景；根据精度和范围也可对应为 Integer、Long、Double |
| `NUMBER`（无精度）      | `java.math.BigDecimal`                       | 默认映射为 BigDecimal                                        |
| `NUMBER(10,0)`          | `java.lang.Integer` 或 `Long`                | 整型数字（根据长度自动适配）                                 |
| `FLOAT`, `BINARY_FLOAT` | `java.lang.Float`                            | 单精度浮点数                                                 |
| `BINARY_DOUBLE`         | `java.lang.Double`                           | 双精度浮点数                                                 |
| `DATE`                  | `java.sql.Timestamp` / `java.util.Date`      | 日期时间（含日期+时间）                                      |
| `TIMESTAMP`             | `java.sql.Timestamp`                         | 精确到纳秒的时间戳                                           |
| `RAW`, `LONG RAW`       | `byte[]`                                     | 原始字节数据                                                 |
| `BLOB`                  | `java.sql.Blob` / `byte[]`（可读）           | 二进制大对象                                                 |
| `LONG`                  | `java.lang.String`                           | 过时类型，不推荐使用                                         |
| `BOOLEAN`（PL/SQL）     | 不直接支持，可通过 `NUMBER(1)` 映射          | 通常用 `0/1` 表示 false/true                                 |

## 常见的Java映射简表

| **Java 类型**      | **推荐 Oracle 类型**       |
| ------------------ | -------------------------- |
| `String`           | `VARCHAR2`, `CHAR`, `CLOB` |
| `Integer`          | `NUMBER(10,0)`             |
| `Long`             | `NUMBER(19,0)`             |
| `BigDecimal`       | `NUMBER(p,s)`              |
| `Double` / `Float` | `FLOAT`, `BINARY_DOUBLE`   |
| `Date`             | `DATE` 或 `TIMESTAMP`      |
| `byte[]`           | `RAW`, `BLOB`              |

## 对应MyBatis指定JDBC类型

| Oracle 数据类型          | Java 类型                     | JDBC 类型（MyBatis中`jdbcType`） |
| ------------------------ | ----------------------------- | -------------------------------- |
| `VARCHAR2(n)`            | `String`                      | `VARCHAR`                        |
| `CHAR(n)`                | `String`                      | `CHAR`                           |
| `NUMBER(p,s)`：整数      | `Integer` / `Long`            | `INTEGER` / `BIGINT`             |
| `NUMBER(p,s)`：有小数    | `BigDecimal` / `Double`       | `DECIMAL` / `NUMERIC`            |
| `FLOAT`                  | `Float` / `Double`            | `FLOAT` / `DOUBLE`               |
| `DATE`                   | `java.sql.Date` / `Timestamp` | `DATE`                           |
| `TIMESTAMP`              | `java.sql.Timestamp`          | `TIMESTAMP`                      |
| `CLOB`                   | `String` / `Clob`             | `CLOB`                           |
| `BLOB`                   | `byte[]` / `Blob`             | `BLOB`                           |
| `RAW(n)`                 | `byte[]`                      | `BINARY`                         |
| `LONG`（过时，不推荐）   | `String`                      | `LONGVARCHAR`                    |
| `BOOLEAN`（Oracle 12c+） | `Boolean`（较少用）           | `BOOLEAN`（部分驱动支持）        |