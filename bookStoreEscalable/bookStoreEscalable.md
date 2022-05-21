# BookStore escalable

Para el diseño de la aplicación se hizo uso de los siguientes elementos:
- Configuración de arquitectura de red (explicado en Red)
- Instancias de EC2 con ubuntu 20.04 (despliegue explicado en tutorial)
- Despliegue de MongoDB
- Docker, para la contenerización del back y del front
- Desarrollo de AMIs propias
- Templates de lanzamiento
- Balanceadores de carga
- Scaling groups



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

Luego generamos una consola de mongo e insertamos los valores en la bd

```
db.books.insert({name:"Leonardo Davinci: La Biografia",image:"/images/img-ld-labiografia.jpeg",author:"Walter Issacson",description:"Basándose en las miles de páginas de los cuadernos manuscritos de Leonardo y nuevos descubrimientos sobre su vida y su obra, Walter Isaacson teje una narración que conecta el arte de Da Vinci con sus investigaciones científicas, y nos muestra cómo el genio del hombre más visionario de la historia nació de habilidades que todos poseemos y podemos estimular, tales como la curiosidad incansable, la observación cuidadosa y la imaginación juguetona. Su creatividad, como la de todo gran innovador, resultó de la intersección entre la tecnología y las humanidades. Despellejó y estudió el rostro de numerosos cadáveres, dibujó los músculos que configuran el movimiento de los labios y pintó la sonrisa más enigmática de la historia, la de la Mona Lisa. Exploró las leyes de la óptica, demostró como la luz incidía en la córnea y logró producir esa ilusión de profundidad en la Última cena.",countInStock:"2",price:"50.000"})

db.books.insert({name:"Inteligencia Genial",image:"/images/img-ld-inteligenciagenial.jpeg",author:"Michael Gelb",description:"El que, para muchos, ha sido el mayor genio de todos los tiempos, Leonardo da Vinci, puede servir de inspiración a todo aquel que quiera desarrollar al máximo su potencial intelectual y su creatividad. Paso a paso, mediante ejercicios, técnicas y lecciones, este libro muestra el camino para ampliar los horizontes de la mente",countInStock:"3",price:"30.000"})
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
Vamos a la consola de EC2, seleccionamos la instancia base para la AMI
![image](https://user-images.githubusercontent.com/53027815/169667233-682dbcc9-8060-47c3-a79d-760c878fe0b5.png)
Le damos un nombre y la desplegamos (este proceso para las instancias base del front y el back)

## Launch templates
En la consola de EC2
![image](https://user-images.githubusercontent.com/53027815/169667305-1a071509-8d90-48a0-8989-32dc095e1ae6.png)
Elegimos la AMI con la que lo queremos asociar y le ponemos su respectivo nombre (para back y front)

## Load Balancers
Se deben crear dos balanceadores de carga, uno *internet facing* para el front, y uno interno para el back.
Para esto, se deben crear unos target groups en la opción de target groups de loadbalancers
Allí se debe configurar lo siguiente
![image](https://user-images.githubusercontent.com/53027815/169667560-9a64ad81-fc52-4f1d-b53e-b1d9b6f324eb.png)
Con la ruta en la que se expone el servicio y el puerto (para front y back)
Luego, al balanceador se le debe decir en cuál zona de disponibilidad y subred va a repartir las peticiones, así como los puertos y el target group correspondiente a cada uno

## Scaling groups
Se debe generar un scaling group para el front y otro para el back.
- Se asocia el launch template correspondiente a cada grupo
- Se debe apuntar a ambas zonas de disponibilidad con las subnets correspondientes
- Se desea un estado de 2 instancias (una en cada zd)
- Un máximo de instancias de 10
- Se debe asociar a los target groups correspondientes

Nota: para la configuración del dominio, se debe tomar el dns del balanceador del frontend
