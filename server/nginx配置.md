# nginx配置

    include /etc/nginx/conf.d/*.conf;

        server {
            server_name  mypenguin.biz;
            root         /usr/share/nginx/html;

            # Load configuration files for the default server block.
            include /etc/nginx/default.d/*.conf;

            location / {
                proxy_set_header 'Access-Control-Allow-Origin' 'http://localhost:9000';
                proxy_set_header 'Access-Control-Allow-Methods' 'POST, GET, OPTIONS, DELETE';
                proxy_set_header 'Access-Control-Allow-Credentials' 'true';
                proxy_set_header 'Access-Control-Max-Age' '3600';
                proxy_set_header 'Access-Control-Allow-Headers' 'X-Requested-With, Content-Type, Accept, uid, sid';
                #rewrite ^/api/(.+) $1 break;
                #proxy_pass http://127.0.0.1:8088/admin/$1;
                proxy_pass http://127.0.0.1:8080/;
            }


            error_page 404 /404.html;
            location = /404.html {
            }

            error_page 500 502 503 504 /50x.html;
            location = /50x.html {
            }

            listen [::]:443 ssl ipv6only=on; # managed by Certbot
            listen 443 ssl; # managed by Certbot
            ssl_certificate /etc/letsencrypt/live/mypenguin.biz/fullchain.pem; # managed by Certbot
            ssl_certificate_key /etc/letsencrypt/live/mypenguin.biz/privkey.pem; # managed by Certbot
            include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
            ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

        }
        server {
        server_name  jobinder.net;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
            #proxy_set_header 'Access-Control-Allow-Origin' 'http://localhost:9000';
            #proxy_set_header 'Access-Control-Allow-Methods' 'POST, GET, OPTIONS, DELETE';
            #proxy_set_header 'Access-Control-Allow-Credentials' 'true';
            #proxy_set_header 'Access-Control-Max-Age' '3600';
            #proxy_set_header 'Access-Control-Allow-Headers' 'X-Requested-With, Content-Type, Accept, uid, sid';
            #rewrite ^/api/(.+) $1 break;
            #proxy_pass http://127.0.0.1:8088/admin/$1;
          # proxy_pass http://localhost:4200/;
           try_files $uri /index.html;
        }

        location /api {
            proxy_set_header 'Access-Control-Allow-Origin' '*';
            proxy_set_header 'Access-Control-Allow-Methods' 'POST, GET, OPTIONS, DELETE';
            proxy_set_header 'Access-Control-Allow-Credentials' 'true';
            proxy_set_header 'Access-Control-Max-Age' '3600';
            proxy_set_header 'Access-Control-Allow-Headers' 'X-Requested-With, Content-Type, Accept, uid, sid';
            #rewrite ^/api/(.+) $1 break;
            #proxy_pass http://127.0.0.1:8080/api/$1;
            proxy_pass http://localhost:8080/api;
        }

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }



        listen 443 ssl; # managed by Certbot
        ssl_certificate /etc/letsencrypt/live/jobinder.net/fullchain.pem; # managed by Certbot
        ssl_certificate_key /etc/letsencrypt/live/jobinder.net/privkey.pem; # managed by Certbot
        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

    }

    # Settings for a TLS enabled server.
    #
    #    server {
    #        listen       443 ssl http2;
    #        listen       [::]:443 ssl http2;
    #        server_name  _;
    #        root         /usr/share/nginx/html;
    #
    #        ssl_certificate "/etc/pki/nginx/server.crt";
    #        ssl_certificate_key "/etc/pki/nginx/private/server.key";
    #        ssl_session_cache shared:SSL:1m;
    #        ssl_session_timeout  10m;
    #        ssl_ciphers PROFILE=SYSTEM;
    #        ssl_prefer_server_ciphers on;
    #
    #        # Load configuration files for the default server block.
    #        include /etc/nginx/default.d/*.conf;
    #
    #        error_page 404 /404.html;
    #            location = /40x.html {
    #        }
    #
    #        error_page 500 502 503 504 /50x.html;
    #            location = /50x.html {
    #        }
    #    }



    server {
    if ($host = mypenguin.biz) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


        listen       80;
        listen       [::]:80;
        server_name  mypenguin.biz;
    return 404; # managed by Certbot


    }

     server {
    if ($host = jobinder.net) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


        server_name  jobinder.net;
    listen 80;
    return 404; # managed by Certbot


    }}
