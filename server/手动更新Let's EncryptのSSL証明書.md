# 手动更新Let's EncryptのSSL証明書

1. 确认有效期限

        sudo certbot certificates

2. 更新证书

    SSL証明書の有効期限が3０日未満になっている場合、

        sudo certbot renew 

    SSL証明書の有効期限に関わらず、強制的に更新する場合、

        certbot renew --force-renew

    出现「Congratulations, all renewals succeeded.」时即为成功

3. 更新の自動化

    使用Linux的定时任务来定期执行更新命令。
    cronを使用して自動でSSL証明書を更新する
