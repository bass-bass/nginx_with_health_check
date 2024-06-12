# nginx_with_health_check

nginxでactive health checkを行うためmoduleを使用した環境構築サンプル

build : `docker-compose up -d --build`

```
    upstream api {
        server backend01:80;
        server backend02:80;
        server backend03:80;
        server backend04:80;
        server backend05:80;

        check interval=5000 rise=1 fall=2 timeout=15000 type=http port=80;
        check_http_send "GET /health.html HTTP/1.0\r\n\r\n";
    }
```
上記により5000ms間隔で`api:80/health.html`へhttpリクエストを送りレスポンスが2xx,3xxx系以外であれば対象のサーバはdownとみなされる

```
    server {
        listen       80;
        listen  [::]:80;
        server_name  localhost;

    
        location / {
            access_log  off;
            error_log   /var/log/nginx/error.log;
            proxy_pass  http://api/;
        }

        location /status {
            check_status;
            access_log   off;
        }
    }
```
`curl http://localhost:80/status`でサーバのstatusリストが確認可能

### ref
* module : https://github.com/yaoweibin/nginx_upstream_check_module
* passive or active : https://docs.nginx.com/nginx/admin-guide/load-balancer/http-health-check/
* ngx_stream_upstream_module : https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html
* ngx_stream_upstream_hc_module : https://nginx.org/en/docs/stream/ngx_stream_upstream_hc_module.html
