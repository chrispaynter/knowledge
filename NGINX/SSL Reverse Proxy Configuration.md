# An NGINX conf for Team City reverse proxy


```
server {
    listen 443;
    server_name <yourdomain>;

    # Secure these certicates how you see fit. These are the paths that Let's Encrypt would create the certificates for you.
    ssl_certificate /opt/letsencrypt/letsencrypt.sh/certs/<yourdomain>/cert.pem;
    ssl_certificate_key /opt/letsencrypt/letsencrypt.sh/certs/<yourdomain>/privkey.pem;

    ssl on;

    # Allows the certicate signing service to validate your DNS
    location /.well-known {
       root /var/www/html;
    }

    # The proxying to Team City
    location / {
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   Host      $http_host;
        proxy_pass         http://127.0.0.1:8111;

        access_log      /var/log/nginx/<yourdomain>.access.log;
        error_log       /var/log/nginx/<yourdomain>.error.log;
     }
}
```
