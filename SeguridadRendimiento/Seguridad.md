# Seguridad


## Certificado SSL

Procedemos a buscar un dominio en la web [Freenom](https://www.freenom.com/es/index.html?lang=es).
Y luego registramos el dominio.

![imagen](https://user-images.githubusercontent.com/53051637/169668267-01f9c692-5831-4c37-99dd-5fa9ae57d970.png)

Posteriormente, Creamos una cuenta en [CloudFlare](https://www.cloudflare.com/es-es/) y procedemos a registrar nuestro nombre de dominio en la plataforma

![imagen](https://user-images.githubusercontent.com/53051637/169669609-c6e9fdbb-1072-40f6-9a9f-bd56af6fc357.png)

Una vez realizado el proceso la plataforma nos indicará que realicemos algunos cambios en los *namesercers* de [Freenom](https://www.freenom.com/es/index.html?lang=es).

![imagen](https://user-images.githubusercontent.com/53051637/169669667-b0ca12bd-d922-48f2-b531-62c70c1c09ed.png)

Luego, en [CloudFlare](https://www.cloudflare.com/es-es/) procedemos a buscar la sección *DNS* para registrar nuestras entradas, una apuntando al LB y otra al nombre de dominio.

![imagen](https://user-images.githubusercontent.com/53051637/169669697-0b683b11-32c8-46ea-9392-3ccde3cd873e.png)

Al final del proceso se mostraran algunas configuraciones recomendadas que vamosa  aplicar.

![imagen](https://user-images.githubusercontent.com/53051637/169669722-9ad56bbc-58a5-4ab5-ab56-4c089e685432.png)


# Rendimiento

El anterior procedimiento además de generar ek certificado SSL, también garantiza el funcionamiento del *CDN*.

Como podemos apreciar a continuación usando la consola de desarrollo del navegador podemos visualizar el estatus **HIT** en los headers

![imagen](https://user-images.githubusercontent.com/53051637/169669768-e9b174ae-76ea-4acb-b900-86bbce6fa5af.png)
