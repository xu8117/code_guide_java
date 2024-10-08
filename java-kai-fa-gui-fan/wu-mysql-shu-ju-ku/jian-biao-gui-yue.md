# （一）建表规约

## (一) 建表规约

1. 【强制】表达是与否概念的字段，必须使用is\_xxx的方式命名，数据类型是unsigned tinyint（ 1表示是，0表示否）。\
   说明：任何字段如果为非负数，必须是`unsigned`。\
   正例：表达逻辑删除的字段名`is_deleted`，1表示删除，0表示未删除。
2. 【强制】表名、字段名必须使用小写字母或数字，禁止出现数字开头，禁止两个下划线中间只出现数字。数据库字段名的修改代价很大，因为无法进行预发布，所以字段名称需要慎重考虑。\
   说明：MySQL在Windows下不区分大小写，但在Linux下默认是区分大小写。因此，数据库名、表名、字段名，都不允许出现任何大写字母，避免节外生枝。\
   正例：aliyun\_admin，rdc\_config，level3\_name\
   反例：AliyunAdmin，rdcConfig，level\_3\_name
3. 【强制】表名不使用复数名词。\
   说明：表名应该仅仅表示表里面的实体内容，不应该表示实体数量，对应于DO类名也是单数形式，符合表达习惯。
4. 【强制】禁用保留字，如`desc`、`range`、`match`、`delayed`等，请参考MySQL官方保留字。
5. 【强制】主键索引名为pk\_字段名；唯一索引名为uk\_字段名；普通索引名则为idx\_字段名。\
   说明：pk\_ 即primary key；uk\_ 即 unique key；idx\_ 即index的简称。
6. 【强制】小数类型为decimal，禁止使用float和double。\
   说明：float和double在存储的时候，存在精度损失的问题，很可能在值的比较时，得到不正确的结果。如果存储的数据范围超过decimal的范围，建议将数据拆成整数和小数分开存储。
7. 【强制】如果存储的字符串长度几乎相等，使用char定长字符串类型。
8. 【强制】varchar是可变长字符串，不预先分配存储空间，长度不要超过5000，如果存储长度大于此值，定义字段类型为text，独立出来一张表，用主键来对应，避免影响其它字段索引效率。
9. 【强制】表必备三字段：id, gmt\_create, gmt\_modified。\
   说明：其中id必为主键，类型为unsigned bigint、单表时自增、步长为1。gmt\_create, gmt\_modified的类型均为datetime类型，前者现在时表示主动创建，后者过去分词表示被动更新。
10. 【推荐】表的命名最好是加上“业务名称\_表的作用”。\
    正例：alipay\_task / force\_project / trade\_config
11. 【推荐】库名与应用名称尽量一致。
12. 【推荐】如果修改字段含义或对字段表示的状态追加时，需要及时更新字段注释。
13. 【推荐】字段允许适当冗余，以提高查询性能，但必须考虑数据一致。冗余字段应遵循：\
    1）不是频繁修改的字段。\
    2）不是varchar超长字段，更不能是text字段。\
    正例：商品类目名称使用频率高，字段长度短，名称基本一成不变，可在相关联的表中冗余存储类目名称，避免关联查询。
14. 【推荐】单表行数超过500万行或者单表容量超过2GB，才推荐进行分库分表。\
    说明：如果预计三年后的数据量根本达不到这个级别，请不要在创建表时就分库分表。
15. 【参考】合适的字符存储长度，不但节约数据库表空间、节约索引存储，更重要的是提升检索速度。\
    正例：如下表，其中无符号值可以避免误存负数，且扩大了表示范围。

| 对象   | 年龄区间   | 类型                | 字节 |
| ---- | ------ | ----------------- | -- |
| 人    | 150岁之内 | unsigned tinyint  | 1  |
| 龟    | 数百岁    | unsigned smallint | 2  |
| 恐龙化石 | 数千万岁   | unsigned int      | 4  |
| 太阳   | 约50亿年  | unsigned bigint   | 8  |
