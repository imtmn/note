1、使用唯一索引，性能比普通索引更好，explain 索引类型是const ,而普通索引是index

2、查询时通过强制索引过滤数据，在许多情况下可以提高查询性能 使用force index 强制指定索引

3、explain 的type的性能好坏依次为：system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL