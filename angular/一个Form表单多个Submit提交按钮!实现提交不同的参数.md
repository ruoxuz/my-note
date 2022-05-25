    <form name="myForm">
    <input type="hidden" name="action" value="">
    <input type="submit" name="editBtn" value="修改" οnclick="submitFun('edit');">
    <input type="submit" name="delBtn" value="删除" οnclick="submitFun('del');">
    </form>
    
    <script type="text/javascript">
        function submitFun(act){
            $('#action').val(act);
        }
    </script>
    
[link](https://blog.csdn.net/xiaozhen0610/article/details/107744490)