# 4. Sobre el diseño

## Sobre sistema para 1000 usuarios

Se nos pide una concurrencia del 10%, por lo que esperamos máximo 100 usuarios simultáneamente. Nuestras mediciones marcan que cada petición toma 0.25 segundos de CPU en el servidor, aproximadamente. Con esto, 100 * 0.25 nos da alrededor de 25 cores si todos hicieran peticiones simultáneamente. Como lo más probable es que no lo hagan, tenemos 1 core por servidor y nuestra configuración de autoscaling puede aumentar hasta 10 servidores para una suma total de 10 cores.

## Sobre almacenamiento 1TB

Estimamos que lo ideal sería tener un servidor de base de datos con 100 GiB máximo, para que no se presenten muchos retrasos. Por cuestiones de limitaciones con AWS nosotros desplegamos 2 nodos de base de datos.
