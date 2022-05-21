# Lab2 - part 2
Deploy and containerize webapp consisting of backend and frontend.

## Table of contents
* [Database configuration](#db)
* [Introduction](#intro)
* [Virtual machine configuration](#config)
* [Front configuration](#front)
* [Back configuration](#back)
* [Generating certs](#ssl)
* [Docker](#dc)

### Database configuration <a name="db"></a>
- Go to https://www.mongodb.com/cloud/atlas and create an account
- Create a cluster
- Create a new database named bookStore and new collection named books

### Introduction <a name="intro"></a>

**Configure virtual machines with Amazon EC2**:
- Log in into AWS console and click in launch instances
- Select AMI corresponding to Ubuntu 20
- Select t2.micro type of machine
- Set number of instances field with "2"
- Configure VPC and subnet
- Set storage to 20 GiB
- Configure security group, allowing traffic through port 80, 443. Also allow ICMP and ssh traffic.

**Elastic ips**:
- Go to AWS console *Network and Security* > *Elastic ips* > *Allocate elastic ip*
- Once we allocate two elastic ips, we attach each one of them to each vm created in previous step

***

**Back and front configuration**:
- Ssh into machines
- It can be good to change the hostname of both machines, first change preserve_hostname: true in /etc/cloud/cloud.cfg and then the following commands in each vm

```
$ sudo hostnamectl set-hostname back
```

```
$ sudo rebbot
```

### Front config <a name="front"></a>
- Update server
  
```
$ sudo su
$ apt update; apt upgrade -y
```

- Install nginx

```
$ apt install nginx -y
```

- Edit nginx server conf in file vim */etc/nginx/sites-available/default*, add *root /usr/share/nginx/html/bookstore*
<p align="center"><img src="https://drive.google.com/uc?export=view&id=1aLQHA_kq8vErndq9vo_ksK0kbaEWB8ev"></p>

- Then execute the following
<p align="center"><img src="https://drive.google.com/uc?export=view&id=131wX0QR3fdt1s0KKzyrIqiBXMoaE9qa9"></p>

- Then install nodejs

```
$ apt install nodejs npm
```

- Move to frontend folder and

```
$ nmp install; npm run build
```

- Move build folder to */usr/share/nginx/html/bookstore*

### Back config <a name="back"></a>
- Update server
  
```
$ sudo su
$ apt update; apt upgrade -y
```

- Then install nodejs

```
$ apt install nodejs npm
```

- Clone repo

```
$ git clone https://github.com/mherreral/Lab2-tet.git
```

- go to backend folder and edit the database connection in file config/db.js
- For obtaining this, you have to go to Atlas MongoDB page and do the following
<p align="center"><img src="https://drive.google.com/uc?export=view&id=131wX0QR3fdt1s0KKzyrIqiBXMoaE9qa9"></p>

### Generating certs <a name="ssl"></a>
- go to freenom and generate a new domain, in dns, associate the domain to your elastic ip
- In front instance, download certbot (same steps as dk_wordpress)
- Generate certs with the following command

```
certbot certonly   --manual   --preferred-challenges dns -d tet-bookstore.tk -d "*.tet-bookstore.tk" -i nginx --register-unsafely-without-email
```

It will ask for modifying some dns records, you can do this from Freenom dns management console.

### Docker config <a name="dc"></a>

#### Front config
In this configuration you will use the files located in *Lab2-tet/bookStore/front*, tet-bookstore.tk tells the server specifications to nginx and docker-compose.yml generates the container

Here we run 

```
docker-compose up -d
```


#### Back config
In this configuration you will use the files located in *Lab2-tet/bookStore/back*, Dockerfile generates the container

Here we run 

```
sudo docker build -t back2 . && sudo docker run -dit --name tet -p 5000:5000 back2
```

After that, we can see our page working
<p align="center"><img src="https://drive.google.com/uc?export=view&id=1V5xnziSrFLsnBZh93RzPaq6bqTSJkyyu"></p>