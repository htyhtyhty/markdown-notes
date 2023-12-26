# MySQL

## SQL语句

### DDL 数据定义语言

对数据库或者表进行创建 删除 修改等操作

#### 查看所有数据库

```mysql
SHOW DATABASES;
```

#### 选择某一个数据库

```mysql
USE users;
```

#### 查看当前正在使用的数据库

```mysql
SELECT DATABASE();
```

#### 新建数据库

```mysql
CREATE DATABASE IF NOT EXISTS lol;
# 指定编码格式 排序方式  一般不设置 使用默认
CREATE DATABASE IF NOT EXISTS lol DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci;
```

#### 删除数据库

```mysql
DROP DATABASE IF EXISTS lol;
```

#### 修改数据库的编码

```mysql
ALTER DATABASE lol CHARACTER SET = utf8 COLLATE = utf8_unicode_ci;
```



### DML 数据操作语言

对表进行添加 删除 修改等操作

#### 查看所有数据表

```mysql
SHOW TABLES;
```

#### 创建数据表

```mysql
CREATE TABLE IF NOT EXISTS `students` (
  `name` VARCHAR(10),
  `age` int,
  `score` int
);
```

#### 删除数据表

```mysql
DROP TABLE IF EXISTS `momen`;
```

#### 查看表结构

```mysql
DESC students;
SHOW CREATE TABLE `students`;
```

#### 表中添加数据

```mysql
INSERT INTO `user` VALUES (110, 'why', '020-110100', '2023-10-20');
# 常用下面方法
INSERT INTO `user` (`name`, `telePhone`, `createTime`, `updateTime`) VALUES (110, 'why', '020-110100', '2023-10-20');
```

#### 删除数据

```mysql
# 删除所有数据
DELETE FROM `user`;
# 删除符合条件的数据
DELETE FROM `user` WHERE id = 110;
```

#### 更新数据

```mysql
UPDATE `user` SET name = 'ty', telPhone = '010-1229' WHERE id = 115;
```



### DQL 数据查询语言

通过DQL从数据库中查询记录

#### 聚合函数

```mysql
SELECT SUM(price) AS totalPrice FROM `products`;
SELECT SUM(price) AS totalPrice FROM `products` WHERE brand = '苹果';
SELECT AVG(price) AS avgPrice FROM `products` WHERE brand = '苹果';
SELECT MAX(price) AS maxPrice FROM `products` WHERE brand = '苹果';
SELECT MIN(price) AS minPrice FROM `products` WHERE brand = '苹果';
SELECT COUNT(*) AS count FROM `products` WHERE brand = '苹果';
SELECT COUNT(url) AS count FROM `products` WHERE brand = '苹果';
SELECT COUNT(DISTINCT price) AS distinctCount FROM `products` WHERE brand = '苹果';
```

#### GROUP BY

```mysql
SELECT brand, AVG(price), COUNT(price), AVG(score), COUNT(price ) FROM `products` GROUP BY brand; # 通过brand进行分组
# 平均分大于7.5分的手机 手机品牌分类 
SELECT brand AVG(price) FROM `products` WHERE score > 7.5 GROUP BY brand;
```

#### HAVING

HAVING 配合FROUP BY使用 FROUP BY 后不能加WHERE  用HAVING

```mysql
SELECT brand, AVG(price) avgPrice, COUNT(price), AVG(score), COUNT(price ) FROM `products` GROUP BY brand HAVING avgPrice > 2000;
# 平均分大于7.5分的手机的平均价格
SELECT AVG(price) FROM `products` WHERE score > 7.5;
```

#### 多表查询

##### 添加外键 

1. 创建表的时候添加

   ```mysql
   CREATE TABLE `ptoducts` (
     #定义好字段(fields)
     foreign key(brand_id) references brand(id)
   );
   ```

2. 创建完表之后添加

   ```mysql
   # 添加一个brand_id字段
   ALTER TABLE `products` ADD `brand_id` INT;
   # 添加一个brand_id字段 修改brand_id为外键
   ALTER TABLE `products` ADD FOREIGN KEY (brand_id) REFERENCEs brand(id);
   ```

   外键约束 不能设置外键内容之外的值 

#####  修改删除外键引用的id

```mysql
# 外键引用的id不能直接修改或者删除, 需要修改外键的action类型
# 1. 获取到目前的外键的名称
SHOW  CREATE TABLE `products`;
# 2. 根据名称将外键删除掉
ALTER TABLE `products` DROP FOREIGN KEY products_ibfk_1;
# 3. 重新添加外键元素
ALTER TABLE `products` ADD FOREIGN KEY (brand_id) REFERENCES brand(id) ON UPDATE CASCADE;# 修改
ALTER TABLE `products` ADD FOREIGN KEY (brand_id) REFERENCES brand(id) ON DELETE RESTRICT;# 删除 一般使用默认
# 4. 进行修改
UPDATE `brand` SET `id` = 100 WHERE id= 1; 
```

##### 联合主键

```mysql
CREATE TABLE IF NOT EXISTS `students_select_courses` (
  `id` INT PRIMARY KEY NOT NULL AUTO_INCREMENT,
  `student_id` INT NOT NULL,
  `course_id` INT NOT NULL,
  FOREIGN KEY(student_id) REFERENCES students(id) ON UPDATE CASCADE,
  FOREIGN KEY(course_id) REFERENCES course(id) ON UPDATE CASCADE,
  PRIMARY KEY(student_id, course_id) # 联合主键
)
```



##### 多表查询

全部查询 一般不用这种

```mysql
# 获取到笛卡尔乘积
SELECT * FROM `products`, `brand`;
# 笛卡尔乘积再进行筛选
SELECT * FROM `products`, `brand` WHERE products.brand_id = brand.id;
```

SQL JOIN操作查询

![img](https://www.runoob.com/wp-content/uploads/2019/01/sql-join.png)

1. 左连接 查询所有手机(包括没有品牌信息的手机) 以及对应的品牌

   ```mysql
   SELECT * FROM `products` LEFT JOIN `brand` ON products.brand_id = brand.id;
   ```

   

2. 查询没有对应品牌数据的手机

   ```mysql
   SELECT * FROM `products` LEFT JOIN `brand` ON products.brand_id = brand.id WHERE brand.id IS NULL;
   ```

3. 右连接 查询所有的品牌(没有手机数据 品牌也显示) 以及对应的手机数据

   ```mysql 
   SELECT * FROM `products` RIGHT JOIN `brand` ON products.brand_id = brand.id;
   ```

   ```mysql
   SELECT * FROM `products` RIGHT JOIN `brand` ON products.brand_id = brand.id WHERE products.brand_id IS NULL;
   ```

   

4. 内连接 CROSS JOIN  (CROSS可省略)

   ```mysql
   SELECT * FROM `products` CROSS JOIN `brand` ON products.brand_id = brand.id;
   SELECT * FROM `products` CROSS JOIN `brand` ON products.brand_id = brand.id WHERE price > 4000;
   
   # 三表查询
   SELECT * FROM `students` CROSS JOIN `students_select_courses` ON student.id = students_select_courses.student_id CROSS JOIN `courses` ON student_select_courses.course_id = course.id
   ```

5. 全连接 MySQL不支持FULL OUTER JOIN 使用UNION联合

   ```mysql
   (SELECT * FROM `products` LEFT JOIN `brand` ON products.brand_id = brand.id) 
   UNION 
   (SELECT * FROM `products` RIGHT JOIN `brand` ON products.brand_id = brand.id);
   ```

   ```mysql
   (SELECT * FROM `products` LEFT JOIN `brand` ON products.brand_id = brand.id WHERE brand.id IS NULL) 
   UNION 
   (SELECT * FROM `products` RIGHT JOIN `brand` ON products.brand_id = brand.id WHERE products.brand_id IS NULL);
   ```

#### 数据处理

##### 转对象 一对多

```mysql
SELECT pt.id id, pt.title title, pt.price price, 
JSON_OBJECT('id', bd.id, 'website', bd.website, 'rank', bd.rank) brand 
FROM `products` pt 
LEFT JOIN `brand` bd 
ON bd.id = pt.brand_id;
```

##### 组织成对象 放入数组, 多对多

```mysql
SELECT students.id, students.name, students.age,
JSON_ARRAYAGG(JSON_OBJECT('id', courses.id, 'name', courses.name, 'price', courses.price))
FROM `students` 
CROSS JOIN `students_select_courses` ON student.id = students_select_courses.student_id 
CROSS JOIN `courses` 
ON student_select_courses.course_id = course.id
GROUP BY students.id;
```



### DCL 数据控制语言

对数据库 表格的权限进行相关访问控制操作

## SQL数据类型

### 整数数字类型

 INTEGER, INT, SMALLINT, TINYINT, MEDIUMINT, BIGINT;  一般使用INT

### 浮点数字类型

FLOAT(4字节), DOUBLE(8字节)

### 精确数字类型

DECIMAL(10, 2) 2位小数, NUMERIC

### 日期类型

YEAR 以YYYY格式显示值 范围是1901 - 2155 & 0000

DATA 具有日期部分但没有时间部分的值 以YYYY-MM-DD格式显示值 范围是1000-01-01 00: 00: 00 到9999-12-31 23:59:59

**DATETIME 包含日期和时间部分的值 以YYYY-MMMM-DD hh:mm:ss 格式显示值 范围是1000-01-01 00:00:00 到9999-12-31 23:59:59

**TIMESTAMP 同时包含日期和时间部分的值 以YYYY-MM-DD hh:mm:ss 格式显示值 范围是UTC时间范围 1970-01-01 00:00:01 到2038-01-19 03:14:07

** 注意: DATATIME 和 TIMESTAMP都可以表示高精度微秒 1000-01-01 00:00:00.000000 到 9999-12-31 23:59:59.999999

### 字符串类型

CHAR 固定长度, 长度可以是0-255之间的任何值;  在被查询时, 会删除后面的空格, 用的比较少;

VARCHAR 可变的长度的字符串 0-65535 被查询时, 不会删除后面的空格, 用的比较多;

BINARY和VARBINARY 存储二进制字符串, 存储的是字节字符串; 基本不用

BLOB 存储大的二进制类型;

TEXT 存大的字符串类型;

## 表约束

### 主键 PRIMARY KEY

一张表中, 为了区分每一条记录的唯一性, 必须有一个字段永远不会重复且不为空, 这个字段我们通常将它设置为主键

```mysql
CREATE TABLE IF NOT EXISTS `students` (
  `id` INT PRIMARY KEY AUTO_INCREMENT
  `name` VARCHAR(10) DEFAULT '' NOT NULL,
  `age` INT,
  `score` INT,
  `height`: DECIMAL(10, 2),
  `birthday` YEAR,
  `phoneNumber` VARCHAR(20) UNIQUE NOT NULL # 不能为空且不能重复
);
```



- 主键是表中唯一的索引
- 必须是NOT NULL的, 如果没有设置NOT NULL, MySQL也会隐式的设置为NOT NULL
- 主键可以是多键索引, PRIMARY KEY(key_part, ...), 一般称之为联合主键
- 主键字段应该是和业务无关的, 尽量不使用业务字段来作为主键

### 唯一 UNIQUE

- 有些字段我们希望是唯一的, 比如手机号码, 身份证号码等, 这个字段可以使用UNIQUE来约束

  ```mysql
  CREATE TABLE IF NOT EXISTS `students` (
    `name` VARCHAR(10),
    `age` INT,
    `score` INT,
    `height`: DECIMAL(10, 2),
    `birthday` YEAR,
    `phoneNumber` VARCHAR(20) UNIQUE NOT NULL # 不能为空且不能重复
  );
  ```

  

- 使用UNIQUE约束的字段在表中必须是不同的

- 对于所有引擎, UNIQUE索引允许NULL包含的列具有多个值NULL (NULL 是可以重复的, 前提是没有写NOT NULL)

### 不能为空 NOT NULL

```mysql
CREATE TABLE IF NOT EXISTS `students` (
  `name` VARCHAR(10),
  `age` INT,
  `score` INT,
  `height`: DECIMAL(10, 2),
  `birthday` YEAR,
  `phoneNumber` VARCHAR(20) UNIQUE NOT NULL # 不能为空且不能重复
);
```

### 默认值 DEFAULT

```mysql
CREATE TABLE IF NOT EXISTS `students` (
  `name` VARCHAR(10) DEFAULT '' NOT NULL,
  `age` INT,
  `score` INT,
  `height`: DECIMAL(10, 2),
  `birthday` YEAR,
  `phoneNumber` VARCHAR(20) UNIQUE NOT NULL # 不能为空且不能重复,
  `createTime` TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  `updateTime` TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### 自动递增 AUTO_INCREMENT

```mysql
CREATE TABLE IF NOT EXISTS `students` (
  `id` INT AUTO_INCREMENT
  `name` VARCHAR(10) DEFAULT '' NOT NULL,
  `age` INT,
  `score` INT,
  `height`: DECIMAL(10, 2),
  `birthday` YEAR,
  `phoneNumber` VARCHAR(20) UNIQUE NOT NULL # 不能为空且不能重复
);
```

## 修改表

### 修改表的名字

```mysql
ALTER TABLE `users` RENAME TO `user`;
```

### 添加新的列(字段)

```mysql
ALTER TABLE `user` ADD `updateTime` TIMESTAMP;
```

### 修改字段名称

```mysql
ALTER TABLE `user` CHANGE `phoneNumber` `telPhoneNum` VARCHAR(20);
```

### 修改字段的类型

```mysql
ALTER TABLE `user` MODIFY `name` VARCHAR(30);
```

### 删除字段

```mysql
ALTER TABLE `user` DROP `height`;
```

### 根据表结构创建另一张表

```mysql
CREATE TABLE `user1` LIKE `user`;
```

### 根据一个表中的所有内容, 创建另一张表

```mysql
CREATE TABLE `user2` (SELECT * FROM `user`); # 只是赋值内容而已  主键等表结构没有复制
```





