# mysql like优化

使用like查询时，把%放前面会全表检索，非常慢

    select * from project WHERE name like '%リーダー経験%';

只在后面加%，会走索引

    select * from project WHERE name like 'リーダー経験%';

需要前后都有%的情况，可以使用全文索引full text index

但是全文索引是通过空格，标点符号来分词的，中文，日文韩文可以用ngram

    show variables like '%ngram%';
    
    'ngram_token_size', '2'
    
    select SQL_NO_CACHE * from project WHERE (del_flg = false and foreigner = 1 and project_type = 1 and match(name) against('リーダー経験' in boolean mode)) order by id desc LIMIT 50 ;

[mysql ngram全文检索引擎](https://blog.csdn.net/u013887008/article/details/106757993)

[MySQL 之全文索引](https://blog.csdn.net/mrzhouxiaofei/article/details/79940958)

[MySQL全文索引和like](https://blog.csdn.net/belongtocode/article/details/102990743)

[ngram Full-Text Parser](https://dev.mysql.com/doc/refman/5.7/en/fulltext-search-ngram.html)

[Boolean Full-Text Searches](https://dev.mysql.com/doc/refman/8.0/en/fulltext-boolean.html)
