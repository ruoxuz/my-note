# scp上传文件

    scp /usr/test/working/Test1 me@55.55.55.55:/usr/me/directory1/working/Test1
                |          |   |     |               |                     |
        Source Dir       |   |  Remote Svr         |                 Remote File (optional)
                    Source File |                Remote Directory (optional)
                        Remote User


    scp -r -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-stg-tokyo-new.pem /var/lib/jenkins/workspace/jobpool-front-stg/dist/jobpool-front/browser/* ec2-user@54.95.98.47:/tmp/jobpool-front
