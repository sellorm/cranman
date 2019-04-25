# cranman

A small tool for managing a CRAN-like repository.

Requires Python 2 (sadly still the default python on RHEL) and an R installation.

## Usage

As the `cranadmin` user, copy R package files to the `/srv/cran/cranroot` directory.

Then run the following to create the Archive structure and PACKAGES files.

```
$ cranman /srv/cran/cranroot
```

## Configuring R

Configure R to use `http://servername:1234` as a CRAN-like repository.

# Installation

## Install cranman

Copy the `cranman` file to `/usr/local/bin` and make sure it's executable.

## Create the cranadmin user and set a password

```
# useradd cranadmin
# passwd cranadmin
```



## create the directory layout and set ownership

```
# mkdir -p /srv/cran/cranroot
# mkdir /srv/cran/www
# chown -R cranadmin:cranadmin
```

Copy `index.html` to `/srv/cran/www`.


## Install and configure nginx

Note: to install nginx using yum you must first enable the EPEL

```
# yum install -y nginx
```

Add the following server block to `/etc/nginx/nginx.conf`

```
    server {
        listen       1234 default_server;
        listen       [::]:1234 default_server;
        server_name  _;
        root         /srv/cran/www;

        location / {
          index index.html;
        }

        location /src/contrib {
          alias /srv/cran/cranroot;
          autoindex on;
        }

        location /src {
          return 302 /;
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
```

If you want to use port 1234 you'll need to let SELinux know:

```
semanage port -m -t http_port_t -p tcp 1234
```
