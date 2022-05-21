# Monitoreo de disponibilidad

Para monitorear nuestra página se usa `uptimerobot`, que es un servicio que cada 5 minutos hace una petición HEAD a la página web y determina el estado de la misma.

Sobre uptimerobot: https://uptimerobot.com/

A continuación instrucciones sobre cómo se configuró.

## 1. Registrarse

El primer paso es crearse un usuario en https://uptimerobot.com/. Tiene plan gratuito, aunque con funcionalidades limitadas.

## 2. Crear monitor

El monitor es lo que te va a monitorear la disponibilidad de tu página web. Luego de registrarnos veremos:

![image](https://user-images.githubusercontent.com/46629861/169666939-54f251bf-d3c1-46a1-9c05-b1e3f59967ef.png)

Le daremos click al botón de la izquierda "Add new Monitor". Nos aparece el formulario: 

![image](https://user-images.githubusercontent.com/46629861/169666970-c915b407-b9bd-4c78-a279-c801284b0b8d.png)

Y luego de llenar los datos de nuestro monitor, estará creado.

## 3. Revisar resultados

Cada 5 minutos el monitor revisar el estado de la página, y se ven los resultados de la siguiente manera. En nuestro caso la página es funcional:

![image](https://user-images.githubusercontent.com/46629861/169667018-79e6a2ba-bfd1-42d5-8fd0-35c85e1960b6.png)
