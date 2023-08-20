# VPC setup 

we will start preparing our vpc by picture by picture :
when ever you enter into google cloud console , you will see some thing like , we called it sandbox :

![sandbox-front](https://github.com/RaihanIIUC/backend-deploy-in-vpc-with-load-balance/assets/51045712/2497f9f0-6a89-45be-b87b-46fd3c0ade5c)

# Steps : 
## 1. create vpc 
![vpc-front](https://github.com/RaihanIIUC/backend-deploy-in-vpc-with-load-balance/assets/51045712/913cd3f4-4ae2-47a3-9327-5e8c936e88fb)

![vpc-form-filled](https://github.com/RaihanIIUC/backend-deploy-in-vpc-with-load-balance/assets/51045712/b90e38ea-c69d-46de-8a3c-5454838d8f13)

after filling this vpc form , it will take some time , if everything ok , then you have a vpc for you .

## 2. create NAT 
![nat-form-filled](https://github.com/RaihanIIUC/backend-deploy-in-vpc-with-load-balance/assets/51045712/6431a4f1-9aae-4f29-acf5-da0f4ca8a5bd)


## Now we are ready with our VM to GO.
filled the form like avobe picture , but you can change as your needs.
![nat-router--form](https://github.com/RaihanIIUC/backend-deploy-in-vpc-with-load-balance/assets/51045712/d31f5a73-68a4-4807-9ebd-58762cf2fbb2)

with inside it , you need a router for your nat , add it as picture describes.

## 3. VM For Backend 

 ### first we create a vm template instance with private ip configuration 


 ![vm-private-ip-template-filled](https://github.com/RaihanIIUC/backend-deploy-in-vpc-with-load-balance/assets/51045712/62f5cdb3-6e1c-4b1f-aed5-d268aee9ad5d)

we showed how to filled it . the main difference of it with pubic ip ones is 
`External ip : Network subnet tab `




# Setup Backend in VM 

### clone this project locally
* `clone this project git clone address`
* `cd backend-deploy-in-vpc-with-load-balance`
* `go to : https://github.com/nodesource/distributions , take binary 16 version and install `
* `node --version`
* `sudo corepack enable` will enable yarn and other npm related corepack.
* `yarn add nodemon`
* `yarn run dev`


### setup database 

* `sudo apt update`
* `sudo apt install default-mysql-server`
* `sudo systemctl start mysql`
* `sudo systemctl enable mysql`
* `sudo mysql_secure_installation`
* `mysql -u root -p`

#### if any issue with mysql :

`sudo mysqld_safe --skip-grant-tables &`
`mysql -u root`
`sudo systemctl stop mysql`
`sudo systemctl start mysql`

### setup redis 
* `sudo apt install redis`
* `redis-cli --version`

### test primsa database connection 



# Load Balance Setup 

![vpc-public-ip-template](https://github.com/RaihanIIUC/backend-deploy-in-vpc-with-load-balance/assets/51045712/f3a6439d-b06d-475b-b0a9-d15654e53a54)

we create a public ip available vm instance template so that we can make a load balance cloing the publically setted template.

## Inside LoadBalance

`cd /etc/nginx`
`ls`
`sudo nano nginx.conf` change this conf file as needed :


`events {
    # empty placeholder
}


http {

    server {
        listen 80;

        location / {
            proxy_pass http://frontend;
        }

        location /api/ {
            rewrite ^/api/(.*)$ /$1 break;
            proxy_pass http://backend;
        }
    }

    upstream frontend {
        server client-service:80;
    }

    upstream backend {
        server api-service:5000;
        server api-service:5000;
    }
}`

`location /api/ {
            rewrite ^/api/(.*)$ /$1 break;
            proxy_pass 10.10.0.2;
}`

`upstream backend {
        server api-service:5000;
        server api-service:5000;
}` set two backend there so that whenever you have huge request it will auto transfer user to other one.

### if lag / cache issue :
`sudo nginx -s reload`

