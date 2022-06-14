# left join右表里多条数据，只取第一条

    select * from a left join B e 
    on a.aname = e.name 
    and e.id = (select top 1 id from B where name = e.name order by UserLevel desc)

mysql 没有 select top 语法，可以用 limit 实现

    select * from a left join B e 
    on a.aname = e.name 
    and e.id = (select id from B where name = e.name order by UserLevel desc limit 1)

[Left Join 右表多条记录 如何取合适的第一条？](https://bbs.csdn.net/topics/370125167)
[MySQL中如何实现select top n](https://www.cnblogs.com/yxnchinahlj/p/4096484.html)
