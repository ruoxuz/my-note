安装

    # yum install multitail
    
查询
    
    multitail /var/log/jobpool-api_exception.log -l 'ssh -o StrictHostKeyChecking=no -i /app/shell/jobpool-prod-tokyo.pem ec2-user@54.95.125.234 tail -f /var/log/jobpool-api_exception.log'