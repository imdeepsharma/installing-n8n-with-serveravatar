
# Installing n8n.io with Serveravatar  

This tutorial assumes that you are a Serveravatar user and have installed a nginx server on your Ubuntu 22.+ linux hosting



### Steps necessary

Step 1
Your server is connected in Serveravatar and running nginx

Step 2
Install Docker

Step 3
Modify the config file in nginx

Step 4
Install dockerised n8nio

*******************************************
### Step 1: Serveravatar is connected
Assuming that you have connected your Serveravatar account and installed an nginx web server

*******************************************
### Step 2: Installing Docker
Upadte the system packages by using the following command:

```shell
sudo apt update
```

Then, install Docker by running:

```shell
sudo snap install docker
```

*******************************************
### Step 3: Modify your nginx config
To modify you need to know the nginx config path. You can use the locate command to find the Nginx configuration directory. Open a terminal and run:

```shell
sudo updatedb
locate nginx.conf
```
If locate command is not available it will ask you to add package plocate

In case of serveravatar users, you will find the config file somewhere along this directory

/etc/nginx/sites-available/

cd into that directory and create the config for your website using 

```
sudo nano /etc/nginx/sites-available/n8n
```
Add the following configuration:

```
server {
server_name yourdomain.com www.yourdomain.com;

location / {
        proxy_pass http://localhost:5678;
        proxy_set_header Connection "keep-alive";
        proxy_http_version 1.1;
        chunked_transfer_encoding off;
        proxy_buffering off;
        proxy_cache off;


    }
}

server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$host$request_uri;
}

```
Replace yourdomain.com, with your actual domain name, the SSL certificate path, and SSL certificate key path, will be added after we install the SSL Certificate.

Save the text and exit.

Now we need to create a symbolic link to enable the site:

```
sudo ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
```
Test Nginx configuration:
```
sudo nginx -t
```
If the test is successful, reload Nginx:

```
sudo systemctl reload nginx
```

If test was unsuccessful then recheck the config file for typos. Once this is done we will need SSL for our website.

>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
To install SSL certificate for the domain follow this

First, install Certbot and Nginx plugin by running:

```shell
sudo apt install certbot python3-certbot-nginx
```

Then, you can run Certbot and follow the on-screen instructions:

```shell
sudo certbot --nginx -d yourdomain.com
```
Copy the path to your cert files and keep it handy in case your config did not update automatically.
If you follow the prompts correctly nginx will automatically update the config file for the certificate locations and your config file will look like this

```
server {
server_name yourdomain.com www.yourdomain.com;

location / {
        proxy_pass http://localhost:5678;
        proxy_set_header Connection "keep-alive";
        proxy_http_version 1.1;
        chunked_transfer_encoding off;
        proxy_buffering off;
        proxy_cache off;


    }

    listen 443 ssl;
 # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
 # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;
 # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf;
 # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
 # managed by Certbot

}

server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$host$request_uri;
}

```


*******************************************
### Step 4: Starting n8n in Docker

To start n8n in Docker, you can use the following command: (If not found locally will pull from cloud)
First come out of nginx directory by typing cd + enter_key

```shell
sudo docker run -d --restart unless-stopped -it \
    --name n8n \
    -p 5678:5678 \
    -e N8N_HOST="yourdomain.com" \
    -e VUE_APP_URL_BASE_API="https://yourdomain.com/" \
    -e WEBHOOK_TUNNEL_URL="https://yourdomain.com/" \
    -v ~/.n8n:/root/.n8n \
    n8nio/n8n
```

Please replace `mydomain.com` with the domain you want n8n to be installed. After this, you can access n8n on `yourdomain.com`. 

If everything went as needed you can directly access the website and register as admin for your n8n. If your SSL isnt available for any reason, you can check your install at yourdomain.com:5678

*********************************
If you are not a Serveravatar user, you can use my link to credit me, Thanks
[Serveravatar](https://app.serveravatar.com?affiliate_code=jdintllc&key=yqVQp0K1)

Everything you need, at one place!
Cloud hosting made easier for everyone. It does not matter if you are a Pro or just a newbie!

For me its mainly the ease of Server management
