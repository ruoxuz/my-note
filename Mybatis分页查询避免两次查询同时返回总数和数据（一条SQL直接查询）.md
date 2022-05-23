一、 场景：
mysql分页查询一般都需要给前端返回一个总数，便于前端人员计算总页数。以前的处理办法是查询两次数据库，现在想提升下效率。

二、处理方法：

    SELECT SQL_CALC_FOUND_ROWS * FROM [table] WHERE ......  limit M, N;
    SELECT FOUND_ROWS();

SQL_CALC_FOUND_ROWS 告诉MySQL将sql所处理的行数记录下来
FOUND_ROWS() 则取到了这个纪录。

三、controller层：

    public Page<People> PageAfter(PageModel<People>  page, PeopleQuery query) {
            List<People> list = service.PageAfter(page.getP(),page.getPs(),query);
            List<People> people = (List<People>) list.get(0); //数据集合
            Integer total = ((List<Integer>) list.get(1)).get(0); //总量
            Page<People> page1 = new Page();
            page1.setTotal(total);
            page1.setRecords(people);
            page1.setCurrent(page.getP());
            page1.setSearchCount(true);
            page1.setSize(page.getPs());
            page1.setPages(total/page1.getSize());
            return page1;
        }
    
四、service层：

    public List<People> PageAfter(long p, long ps, PeopleQuery query) {
    		return peopleMapper.PageAfter(p,ps,query);
    }

五、mapper层：

    List<People> PageAfter(@Param("p") Long p,@Param("ps") Long ps, @Param("query") PeopleQuery query);

六、xml代码：

    <!-- 用来记录分页查询时的全部数据量，避免出现分页查询时须要查询两次 -->
        <resultMap type="java.lang.Integer" id="count">
            <result column="total"/>
        </resultMap>
    
    <!-- 这个地方的resultMap中的内容会变红，不过不影响程序正常启动 -->
    <select id="PageAfter" resultMap="BaseResultMap,count"
                  resultType="com.sangang.video.admin.modules.dim.entity.People">
        select SQL_CALC_FOUND_ROWS
            a.id,a.name,sum(b.type = 'PEOPLE_VOICE' OR NULL) AS voice, sum(b.type = 'PEOPLE_FACE' OR NULL) AS face,a.create_date
        from
            dim_people a
        LEFT JOIN
            dim_attach b ON b.bill_id = a.id
        where
            a.flag=1
           <if test="query.name!=null  and query.name !='' ">
              and a.name like  CONCAT('%',#{query.name},'%')
           </if>
        GROUP BY
           a.id,a.name
        LIMIT #{p}, #{ps};
        SELECT FOUND_ROWS() as total;
    </select>

注意：在使用时须要在配置文件中，设置容许sql进行多语句执行：allowMultiQueries=true，在sql的url上加上这个配置就能够了

https://blog.csdn.net/weixin_44102521/article/details/120450150

有时候两次查询更快

https://blog.csdn.net/Saintyyu/article/details/88837259?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_default&utm_relevant_index=2