#Storm 中的数据流分组

##Shuffle grouping （随机分组）

##Fields grouping（按照字段分组）

根据指定字段的值进行分组。比如说，一个数据流根据 “word”字段进行分组，所有具有相同 “word” 字段值的 tuple 会路由到同一个 bolt 的task 中