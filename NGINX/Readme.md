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

## Backup server ( FailOver )
> Only incase of primary fails, backup server will receive the request

```nginx
events{}
http {
        upstream backend_servers {
                server IP_OF_BACKEND_SERVER_1;
                server IP_OF_BACKEND_SERVER_2 backup;
        }
        server {
                location / {
                        proxy_pass http://backend_servers/;
                }
                listen 80;
        }
}
```


## TLS/SSL Configuration with Self-Signed Certificates

Generate Self Signed TLS/SSL certificates
CN/CommonName: Provide Public IP Address of AWS Instance
```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
```

```nginx
events{}


http {
        server {

                listen 443 ssl;
                server_name 52.87.203.39;

                ssl_certificate /etc/ssl/certs/nginx-selfsigned.crt;
                ssl_certificate_key /etc/ssl/private/nginx-selfsigned.key;

                location / {
                        proxy_pass http://52.87.203.39:80/;
                        proxy_set_header Host $host;
                        proxy_set_header X-Real-IP $remote_addr;
                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                }
        }
        server {

                location / {
                        proxy_pass http://52.87.203.39:80/;
                }
                listen 80;
        }
}
```