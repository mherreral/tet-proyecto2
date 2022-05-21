# BookStore escalable

Para el diseño de la aplicación se hizo uso de los siguientes elementos:
- Configuración de arquitectura de red (explicado en Red)
- Instancias de EC2 con ubuntu 20.04 (despliegue explicado en tutorial)
- Despliegue de MongoDB
- Docker, para la contenerización del back y del front
- Desarrollo de AMIs propias
- Templates de lanzamiento
- Scaling groups
- Balanceadores de carga

## MongoDB
Para el despliegue de las bases de datos, se deben seguir los siguientes pasos:

```
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -
sudo apt-get install gnupg
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list
sudo apt-get install -y mongodb-org
```

Luego vamos al archivo */etc/mongod.conf* y allí cambiamos el bindIp

```
# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0
```

## Configuración de las máquinas docker
Para obtener un ambiente de docker, se siguieron los siguientes comandos en una instancia del front y otra del back

```
# Uninstall all docker packages
sudo apt-get remove docker docker-engine docker.io

# Update mirror index
sudo apt-get update

# Install needed packages
sudo apt-get install -y \
	apt-transport-https \
	ca-certificates \
	curl \
	gnupg \
	lsb-release vim git

# Add docker GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add stable directory
echo \
	  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
	    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update mirror index
sudo apt-get update

# Install docker
sudo apt-get install -y docker-ce

# Init docker service
sudo systemctl start docker
sudo systemctl enable docker
```

Y para la instalación de docker compose:

```
# Install docker compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.28.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# Change permissions
sudo chmod +x /usr/local/bin/docker-compose
```

### Front
Para la cofiguración del front, se hizo uso de la explicación dada en https://github.com/mherreral/Lab2-tet/blob/main/bookStore/README.md, haciendo uso del source del front de este repositorio.

### Back
Para el despliegue del back, se adicionó un docker-compose.yml y una configuración especial en .env
- En el *.env* se debe especificar la conexión con la base de datos dependiendo de la zona de disponibilidad.

Para correr el back:

```
apt install nodejs npm
sudo docker build -t back2 .
docker-compose up -d
```

## Generación de AMIs

### Back


## Launch templates
