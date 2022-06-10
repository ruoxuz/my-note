# jenkins修改yaml文件

Credentials Binding Plugin

在Pre Steps中添加“执行shell”

    sed -r -i 's#password:#password: Hj*(9Cv%GcmH#g' ${WORKSPACE}/jobpool-admin-api-prod/src/main/resources/application-prod.yml
