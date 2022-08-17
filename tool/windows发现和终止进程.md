# windows发现和终止进程

    netstat -ano |findstr "端口号"
    
    tasklist |findstr "进程id号"
    
    taskkill /f /t /im "进程id或者进程名称"
