# Nginx

## Install

```bash 
sudo yum install nginx

sudo amazon-linux-extras install nginx1
```

## Start

``` bash 
sudo service nginx start
```

## Config

* etc/nginx/nginx.conf
    ```
    user    ec2-user;
  
    server {
        listen       80;
        root         /home/ec2-user/apps/mondrian-lab-ui/docs;
        location / {
                try_files $uri $uri/ /index.html;
        }
        location /api {
                proxy_pass http://localhost:10080;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
        }
        location /spider {
                proxy_pass http://localhost:10080;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
        }
    }
    ```

* for SSl
    ``` 
    server {
      if ($host = www.mondrianlab.com) {
          return 301 https://$host$request_uri;
      }
 
      if ($host = map.mondrianlab.com) {
          return 301 https://$host$request_uri;
      }
 
      if ($host = blog.mondrianlab.com) {
         return 301 https://$host$request_uri;
      }
 
      listen       80 ;
      listen       [::]:80 ;
      server_name blog.mondrianlab.com www.mondrianlab.com map.mondrianlab.com;
      return 404;
   }
   ```
  
## SSL

### certbot-auto

:::danger 
이제 이거 안된다.
:::

``` bash 
wget https://dl.eff.org/certbot-auto
chmod a+x certbot-auto
sudo ./certbot-auto --nginx --debug
```

* 갱신
    ``` bash 
    sudo ./certbot-auto renew --debug
    ```
* 갱신 시 오류 발생
    ``` bash 
    sudo rm -rf /root/.local/share/letsencrypt/
    sudo rm -rf /opt/eff.org/certbot/
    ``` 
    * https://com-topia.tistory.com/36

### 자동갱신

letsencripty는 유효기간이 3개월  
자동갱신 스크립트를 크론탭에 등록
``` bash
$ sudo -i
$ crontab -e
30 2 * * Sun /home/ec2-user/certbot-auto renew
```

### Amazon Linux 2 with EPEL and Let’s Encrypt

* https://aws.amazon.com/ko/blogs/compute/extending-amazon-linux-2-with-epel-and-lets-encrypt/
* https://serverfault.com/questions/890212/looking-for-a-way-to-get-certbot-running-on-amazon-linux-2
* https://certbot.eff.org/instructions

1. Install and enable EPEL
``` bash 
cd /tmp
wget -O epel.rpm –nv \
https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum install -y ./epel.rpm
```

1. Install certbot

``` bash 
sudo yum install certbot

sudo yum install python-certbot-nginx
```

1. Certbot

``` bash
sudo certbot --nginx
```

1. Certbot Renew

``` bash 
sudo certbot renew --dry-run
```
