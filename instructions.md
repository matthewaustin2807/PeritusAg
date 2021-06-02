### Steps To Copy FarmOS 2.x Source Code In Your Local Environment:
1. Open https://docs.farmos.org/hosting/install/. On the right-hand side, select the link "Packaged Releases".
2. Go to the link https://github.com/farmOS/farmOS/releases. Select version **2.0.0-alpha1**
3. Download and extract the farmOS packaged release tarball, **farmOS-2.0.0-alpha1.tar.gz** into any directory of your choice. Extract it.
4. Once extracted, the folder **farmOS** should show in the current directory.
5. Open the **farmOS** folder and **web** folder will contain all the source code used to build the farmOS website.

### Steps to run the farmOS website on our local web server through nginx
1. Make sure that you have Nginx ver 1.14 or later installed on your local environment. If not, install Nginx through the command line
    - On the command line, type **sudo apt-get install nginx**. Wait for it to download.
    - Make sure that nginx is downloaded properly by typing **nginx -v**. It should be version 1.14
2. Install **php** with version 7.3 or later. 
    - Follow instructions from https://cloudbooklet.com/how-to-install-php-7.3-on-ubuntu-18.04
3. Install and Set Up **PostgreSQL** version 10 or later.
    - Follow instructions from https://support.f5.com/csp/article/K49481224. Make sure that we follow the instructions for Ubuntu 18.04 and also for PostgreSQL 12 (Only do this step when PostgreSQL is not installed in your local environment, otherwise set up PostgreSQL DB for use by farmOS app)
    - Set up PostgreSQL database for use by the farmOS app by following the instructions from **Preparing PostgreSQL database for NGINX Controller**. (Make sure you remember the DB username, DB name, and DB password)
4. Point the web server root to the /web folder inside the main farmOS folder.
    - Locate the nginx directory in your local environment. If Ubuntu 18.04 is used, it would probably be located at **/etc/nginx**. So navigate there.
    - Navigate into **/sites-enabled** directory and sudo nano into the **default** file.
    - Modify the default file so it looks like this:
        -       server {
                    listen 80;
                    listen [::]:80;
                    root #this would be your farmOS /web directory
                    index  index.php index.html index.htm;
                    server_name  localhost #for local development

                    location / {
                        try_files $uri /index.php?$query_string;        
                    }

                    location @rewrite {
                            rewrite ^/(.*)$ /index.php?q=$1;
                        }

                    location ~ [^/]\.php(/|$) {
                        include snippets/fastcgi-php.conf;
                        fastcgi_pass unix:/var/run/php/php7.1-fpm.sock;
                        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                        include fastcgi_params;
                    }

                    location ~ ^/sites/.*/files/styles/ { # For Drupal >= 7
                            try_files $uri @rewrite;
                    }

                    location ~ ^(/[a-z\-]+)?/system/files/ { # For Drupal >= 7
                        try_files $uri /index.php?$query_string;
                    }
                }
        - *X.X* will be your current php version (If you downloaded php 7.3, X.X will be 7.3)
5. After all these steps, farmOS should be able to be run on your local environment
    - Go to any web browser and type in **localhost** in the search bar.
    - Follow the installation process and it should be ready to go.