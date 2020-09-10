# Tailable 游标

默认情况下，当客户端使用完游标中的所有结果时，MongoDB将自动关闭游标。但是，对于有上限的集合，您可以使用一个可定制的游标，该游标在客户端穷尽初始游标的结果后保持打开状态。可跟踪游标在概念上等同于带-f选项的tail Unix命令\(即“follow”模式\)。在客户端向有上限的集合中插入新的额外文档之后，可定制游标将继续检索文档。

在具有高写量的有上限集合上使用可定制游标，因为索引不实用。例如，MongoDB复制使用可跟踪的游标跟踪主服务器的[oplog](https://docs.mongodb.com/manual/reference/glossary/#term-oplog).

> **\[success\] 注意**
>
> 如果查询位于索引字段上，则不要使用可跟踪游标，而是使用常规游标。跟踪查询返回的索引字段的最后一个值。要检索新添加的文档，使用查询条件中索引字段的最后一个值再次查询集合，如下面的示例所示:
>
> ```text
> db.<collection>.find( { indexedField: { $gt: <lastvalue> } } )
> ```

考虑以下与可跟踪游标相关的行为:

* 可跟踪游标不使用索引，并按自然顺序返回文档。
* 由于可tailable游标不使用索引，因此查询的初始扫描可能开销较大;但是，在最初耗尽游标之后，后续对新添加文档的检索就不那么昂贵了。
* 可跟踪游标可能会死亡或无效，如果有下列情况:

  * 查询返回不匹配。
  * 游标返回集合“末尾”的文档，然后应用程序删除该文档。

  一个已死亡游标的id为0。

请参阅[驱动程序文档](https://docs.mongodb.com/ecosystem/drivers)，以获取特定于驱动程序的方法以指定可跟踪游标。

译者：杨帅

校对：杨帅
