# Docker-compose erpnext13 template without traefik
This is a docker-compose erpnext13 template with nginx reverse proxy instead of traefik.

The container does not expose any ports. In order to connect to the interface (through erpnext-nginx) you have to configure an external reverse proxy, like nginx.

Here is an example nginx reverse proxy configuration:


    server {
          listen        443 ssl;
          server_name   <SITENAME>;

          if ($host ~* ^www\.(.*)) {
            set $host_without_www $1;
            rewrite ^(.*) https://$host_without_www$1 permanent;
          }

          client_max_body_size  64M;

          ssl_certificate /etc/ssl/private/fullchain.pem;
          ssl_certificate_key /etc/ssl/private/privkey.pem;

          add_header 'Access-Control-Allow-Origin' "*" always;
          add_header 'Access-Control-Allow-Headers' "Authorization" always;
          add_header 'Access-Control-Allow-Credentials' "true" always;
          add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS, PUT' always;

        location / {
            proxy_set_header        X-Frappe-Site-Name erp.bitbot.eu;
            proxy_set_header        Origin $scheme://$http_host;
            proxy_set_header        Host $host;
            proxy_pass  http://erpnext-nginx:8080;
         }
    }
    
    
The erpnext-nginx container must be in the same network as the reverse proxy. In my case "proxy-default". 
The containers files, will directly mounted to the folder. All folder except mysqlconf and files need to be 777 accessable. mysqlconf only 775, because MariaDB doesnt like full access.

    <SITENAME>
    <MYSQL_ROOT_PASSWORD>
    <ADMIN_PASSWORD>
    
need to be replaced.

