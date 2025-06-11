# 列
## ColumnDef

- ColumnDef 是 SQL 抽象语法树（AST）的一部分，**SQL 解析器生成的**。用于表示 CREATE TABLE 或 CREATE SOURCE 语句中的列定义。

```rs
pub struct ColumnDef {
    pub name: Ident,                      // 列名
    pub data_type: Option<DataType>,      // 列的数据类型
    pub collation: Option<Collation>,     // 排序规则
    pub options: Vec<ColumnOptionDef>,    // 列的选项（如主键、默认值等）
}
```

## ColumnCatalog


- ColumnCatalog 表示的是**真实列的元数据**，包含列的详细信息，例如列名、数据类型、是否为用户定义列、默认值等。

```rust
pub struct ColumnCatalog {
    pub column_desc: ColumnDesc,
    pub is_hidden: bool,
}

pub struct ColumnDesc {
    pub data_type: DataType,
    pub column_id: ColumnId,
    pub name: String,
    pub generated_or_default_column: Option<GeneratedOrDefaultColumn>,
    pub description: Option<String>,
    pub additional_column: AdditionalColumn,
    pub version: ColumnDescVersion,
    /// Currently the system column is used for `_rw_timestamp` only and is generated at runtime,
    /// so this field is not persisted.
    pub system_column: Option<SystemColumn>,
    /// Whether the column is nullable.
    /// If a column is not nullable, BatchInsert/BatchUpdate operations will throw an error when NULL is inserted/updated into.
    /// The row contains NULL for this column will be ignored when streaming data into the table.
    pub nullable: bool,
}
```

- generated_or_default_column 表示列创建时的数据是默认值，还是生成得到的（通过表达式计算得到的列）。

```sql
CREATE TABLE orders (
    quantity INT,
    price DECIMAL,
    total_price DECIMAL GENERATED ALWAYS AS (quantity * price)
);
```
## summary

- 在由 TableCatalog 恢复得到原始 table 定义语句时候（TableDef），`try_purify_table_source_create_sql_ast` 中，**ColumnCatalog 是输入，ColumnDef 是输出**，函数的作用是根据 ColumnCatalog 补全或生成 ColumnDef，以生成完整的 SQL 定义。