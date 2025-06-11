# show create table

对于这个关键字需要考虑创建表的时候，sql 的模式。一般有 `create table` 和 `create table as`。关键区别在于 `Statement::CreateTable` 的 query 字段是否为空。

## 对于通过查询创建表

调用 create_table_as::handle_create_as 方法处理：
- name: 
- if_not_exists: 
- query: 用于创建表的查询。
- columns: 列定义。
- append_only: 
- on_conflict: 
- with_version_column: 
- engine: 

```sql
CREATE TABLE users AS SELECT id, name FROM old_users;
```

## 直接创建表

调用 create_table::handle_create_table 方法处理：
- name: 表名。
- columns: 列定义。
- wildcard_idx: 通配符列的索引（如果有）。
- constraints: 表级约束（如主键、唯一约束等）。
- if_not_exists: 是否使用 IF NOT EXISTS 选项。
- format_encode: 表的编码格式。
- source_watermarks: 源的水印信息。
- append_only: 是否为追加模式。
- on_conflict: 冲突处理策略。
- with_version_column: 是否包含版本列。
- cdc_table_info: CDC（变更数据捕获）表的信息。
- include_column_options: 是否包含列选项。
- webhook_info: Webhook 配置信息。
- engine: 表的存储引擎。