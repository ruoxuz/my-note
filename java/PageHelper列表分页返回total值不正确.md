# PageHelper列表分页返回total值不正确

使用PageHelper分页，不可以将数据库中查出来的list转为其他list，可以直接封装DTO在数据库中直接返回你需要的数据类型，不要查询出来之后再去转换,这样会导致PageHelper中pageInfo失效。

[PageHelper列表分页返回total值不正确](https://blog.csdn.net/qq_40180411/article/details/108172014)
