# angular server-side rendering服务端渲染

### 常见错误
#### window is undifined, document is undifined, localStorage...

    constructor(@Inject(PLATFORM_ID) private platformId: any) {}
      ngOnInit() {
        if (isPlatformBrowser(this.platformId)) {
         /* jQuery here */
         $("#test-button").click(function () {
           alert('WOOOW');
           $(this).css("background","#000");
        });
      }
    }
pm2, nodejs版本过低会可能会报错
升级nodejs

    sudo npm install n -g
    sudo n latest
升级之后发现active的不是最新版

    ln -snf /usr/local/bin/node /usr/bin/node
在执行

    pm2 start server/main.js
的时候，一定要在browser,server的上一级文件夹中执行，因为nodejs会在执行的目录之下寻找各个文件。

#### ERROR NetworkError

1. created "universal-relative.interceptor.ts" file
2. put this interceptor code in "universal-relative.interceptor.ts" file

        import { HttpHandler, HttpInterceptor, HttpRequest } from '@angular/common/http';
        import { Inject, Injectable, Optional } from '@angular/core';
        import { REQUEST } from '@nguniversal/express-engine/tokens';
        import { Request } from 'express';

        // case insensitive check against config and value
        const startsWithAny = (arr: string[] = []) => (value = '') => {
            return arr.some(test => value.toLowerCase().startsWith(test.toLowerCase()));
        };

        // http, https, protocol relative
        const isAbsoluteURL = startsWithAny(['http', '//']);

        @Injectable()
        export class UniversalRelativeInterceptor implements HttpInterceptor {
            constructor(@Optional() @Inject(REQUEST) protected request: Request) {}

            intercept(req: HttpRequest<any>, next: HttpHandler) {
                if (this.request && !isAbsoluteURL(req.url)) {
                    const protocolHost = `${this.request.protocol}://${this.request.get(
                        'host'
                    )}`;
                    const pathSeparator = !req.url.startsWith('/') ? '/' : '';
                    const url = protocolHost + pathSeparator + req.url;
                    const serverRequest = req.clone({ url });
                    return next.handle(serverRequest);
                } else {
                    return next.handle(req);
                }
            }
        }

3. Go to your "app.server.module.ts" file
4. add your interceptor like this

        import { NgModule } from '@angular/core';
        import {
        ServerModule,
        ServerTransferStateModule,
        } from "@angular/platform-server";

        import { AppModule } from './app.module';
        import { AppComponent } from './app.component';
        import { UniversalRelativeInterceptor } from 'src/universal-relative.interceptor';
        import { HTTP_INTERCEPTORS } from '@angular/common/http';

        @NgModule({
        imports: [AppModule, ServerModule, ServerTransferStateModule],
        providers: [
            {
            provide: HTTP_INTERCEPTORS,
            useClass: UniversalRelativeInterceptor,
            multi: true,
            },
        ],
        bootstrap: [AppComponent],
        })
        export class AppServerModule {}

### nginx配置

     server {
        listen       80;
        listen       [::]:80;
        server_name  jobpool.jp;
        root         /usr/share/nginx/html/browser; # root改到有index.html的文件夹下
        if ($http_x_forwarded_proto = 'http'){
            return 301 https://$host$request_uri;
        }
    
        # upload file max size is 100m.
        client_max_body_size 300m;
    
        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
    
        location / {
            #try_files $uri /index.html;  # index.html删掉
            try_files $uri  @universal;   # 添加重定向
    
            #auth_basic "Restricted";                  
            #auth_basic_user_file /etc/nginx/.htpasswd;
            location /health {
                satisfy any;
                allow   all;
            }
    
        }
        
         location @universal {
                proxy_pass http://localhost:4000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }
    
    
        location /admin {
            proxy_set_header 'Access-Control-Allow-Origin' 'http://localhost:9000';
            proxy_set_header 'Access-Control-Allow-Methods' 'POST, GET, OPTIONS, DELETE';
            proxy_set_header 'Access-Control-Allow-Credentials' 'true';
            proxy_set_header 'Access-Control-Max-Age' '3600';
            proxy_set_header 'Access-Control-Allow-Headers' 'X-Requested-With, Content-Type, Accept, uid, sid';
            #rewrite ^/api/(.+) $1 break;
            #proxy_pass http://127.0.0.1:8088/admin/$1;
            proxy_pass http://127.0.0.1:8081/admin;
        }
    
        location /api {
            proxy_set_header 'Access-Control-Allow-Origin' '*';
            proxy_set_header 'Access-Control-Allow-Methods' 'POST, GET, OPTIONS, DELETE';
            proxy_set_header 'Access-Control-Allow-Credentials' 'true';
            proxy_set_header 'Access-Control-Max-Age' '3600';
            proxy_set_header 'Access-Control-Allow-Headers' 'X-Requested-With, Content-Type, Accept, uid, sid';
            #rewrite ^/api/(.+) $1 break;
            #proxy_pass http://127.0.0.1:8080/api/$1;
            proxy_pass http://127.0.0.1:8081/api;
        }
    
        error_page 404 /404.html;
            location = /40x.html {
        }
    
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
### jenkins shell

    npm install
    #ng build --prod
    npm run build:ssr
    
    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem ec2-user@54.199.10.91 sudo rm -rf /tmp/fpool-front/*
    scp -r -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem /var/lib/jenkins/workspace/jobpool-front-prod/dist/jobpool-front/ ec2-user@54.199.10.91:/tmp
    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem ec2-user@54.199.10.91 sudo rm -rf /usr/share/nginx/html/*
    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem ec2-user@54.199.10.91 sudo cp -r /tmp/jobpool-front/* /usr/share/nginx/html/
    # 这里是有一个health check
    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem ec2-user@54.199.10.91 "cd /usr/share/nginx/html/browser/; sudo mkdir health; cd health/; sudo touch index.html"
    # 重启一下pm2
    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem ec2-user@54.199.10.91 "cd /usr/share/nginx/html/; sudo pm2 restart server/main.js"
    
    
    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem ec2-user@54.95.125.234 sudo rm -rf /tmp/fpool-front/*
    scp -r -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem /var/lib/jenkins/workspace/jobpool-front-prod/dist/jobpool-front/ ec2-user@54.95.125.234:/tmp
    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem ec2-user@54.95.125.234 sudo rm -rf /usr/share/nginx/html/*
    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem ec2-user@54.95.125.234 sudo cp -r /tmp/jobpool-front/* /usr/share/nginx/html/
    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem ec2-user@54.95.125.234 "cd /usr/share/nginx/html/browser/; sudo mkdir health; cd health/; sudo touch index.html"
    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/workspace/jobpool-prod-tokyo.pem ec2-user@54.95.125.234 "cd /usr/share/nginx/html/; sudo pm2 restart server/main.js"

[facebook](https://developers.facebook.com/tools/debug/)
[twitter](https://cards-dev.twitter.com/validator)
[line](https://poker.line.naver.jp/)

[Angular Universal: a Complete Practical Guide](https://blog.angular-university.io/angular-universal/)

https://stackoverflow.com/questions/61450145/how-to-resolve-error-networkerror-at-xmlhttprequest-send-dist-fxcore-server
