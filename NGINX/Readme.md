## Setup for NGINX Reverse Proxy

### Important files

> /etc/nginx/nginx.conf  

This is where we maintain our nginx.configuration

> /var/log/nginx/access.log
> /var/log/nginx/error.log

This where the logs will be preserved

> /usr/share/nginx/html/

This is the place where we maintain the static website data like index.html, css, images etc

## Reverse Proxy Configuration

```nginx
events{}


http {
        server {

                location / {
                        proxy_pass http://IP_OF_BACKEND_MACHINE/;
                }
                listen 80;
        }
}
```

## Load Balancing Configuration

```nginx
events{}
http {
        upstream backend_servers {
                server IP_OF_BACKEND_SERVER_1;
                server IP_OF_BACKEND_SERVER_2;
        }
        server {
                location / {
                        proxy_pass http://backend_servers/;
                }
                listen 80;
        }
}
```