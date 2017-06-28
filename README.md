
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    log_format  main  ' $remote_user [$time_local]  $http_x_Forwarded_for $remote_addr  $request '  
                      '$http_x_forwarded_for '  
                      '$upstream_addr '  
                      'ups_resp_time: $upstream_response_time '  
                      'request_time: $request_time';  
    access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;
	upstream nw{ server 10.0.1.247:9001; }
    upstream ww{ server 120.27.165.105; }


    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   "F:/trunk/";
            index  index.html;
        }
        location ^~ /zp/ {
                alias F:/trunk/activity/;
            }           

        location ~/* {
          proxy_pass http://nw;
        }		
        location ^~/wd_api/ {
          proxy_pass http://nw;
        }
		
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
		
		location ~ (.*\.json) {
			root  D:/project/m/trunk/;
			error_page 405 =200 $1;
		}

    }

}
