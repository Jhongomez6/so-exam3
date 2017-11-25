### Examen 3
**Universidad ICESI**  
**Curso:** Sistemas Operativos  
**Docente:** Daniel Barragán C.  
**Tema:** Descubrimiento de servicios, Microservicios  
**Correo:** daniel.barragan at correo.icesi.edu.co  
**Estudiantes:** Jhon Eduar Tobar Gómez - A00316212  
                 Carlos Andres Torres - A00141804 
                 
**GitHub URL:**  https://github.com/Jhongomez6/so-exam3


### Objetivos
* Implementar servicios web que puedan ser consumidos por usuarios o aplicaciones
* Conocer y emplear tecnologías de descubrimiento de servicio

### Prerrequisitos
* Virtualbox o WMWare
* Máquina virtual con sistema operativo CentOS7
* Framework consul, zookeper o etcd

### Descripción
El tercer parcial del curso sistemas operativos trata sobre la creación de servicios web y el uso de tecnologías para el descubrimiento de servicio

![][1]
**Figura 1.** Despliegue básico de microservicios

### Actividades
1. Incluir nombre, código (5%)
2. Ortografía y redacción cuando sea necesario (5%)
3. Despliegue un esquema como el mostrado en la **figura 1**. Empleen un servicio web de su preferencia (puede usar alguno de los ejemplos de clase). No es necesario incluir los componentes para monitoreo (Elasticsearch, Kibana, Logstash) (30%)
4. Adicione un microservicio igual al ya desplegado. Muestre a través de evidencias como las peticiones realizadas al balanceador son dirigidas a la replica del microservicio (30%)
5. Describa los cambios o adiciones necesarias en el diagrama de la **figura 1** para adicionar un microservicio diferente al ya desplegado en el ambiente, tenga en cuenta los siguientes conceptos en su descripción: API Gateway, paradigma reactivo, load balancer, protocolo publicador/suscriptor (interconexión de microservicios) (20%)
6. El informe debe ser entregado en formato pdf a través del moodle y el informe en formato README.md debe ser subido a un repositorio de github. El repositorio de github debe ser un fork de https://github.com/ICESI-Training/so-exam3 y para la entrega deberá hacer un Pull Request (PR) respetando la estructura definida. El código fuente y la url de github deben incluirse en el informe (10%)  

---------------------------------------------------------------------------------------------------------------------------------------

### Solución:

3. Para desplegar el esquema mostrado en la figura lo primero que hacemos es configurar el componente consult server en un nodo:

### Consul Server

Primero instalamos las dependencias necesarias:

![GitHub Logo2](Imagenes/Captura.PNG)

Creamos un usuario consul para ejecutar el agente consul como servidor con adduser consul y passwd consul para definir la contraseña del usuario. Luego de esto, le damos permisos al usuario creado y abrimos los puertos necesarios en el firewall para el agente consul:

![GitHub Logo2](Imagenes/Captura0.PNG)

Ahora bien, iniciamos el agente consul en modo servidor con el siguiente comando (Se recomienda usar una sesión de screen para correr el proceso en background):

![GitHub Logo2](Imagenes/Captura0.0.PNG)

Vemos como el agente empieza a correr y queda activo:

![GitHub Logo2](Imagenes/Captura1.PNG)

Asi mismo, podemos verificar la lista de los miembros del ambiente de descubrimiento de servicio con el comando: consul members y verificar que el agente servidor esté en la lista:

![GitHub Logo2](Imagenes/Captura2.PNG)

### Microservico A

Ahora en un nodo diferente configuramos el primer microservicio y lo primero para esto es instalar las dependecias necesarias:

![GitHub Logo2](Imagenes/dependenciasA.PNG)

Como lo hicimos anteriormente, creamos un usuario consul, le asignamos permisos y abrimos los puertos en el firewall para el agente:

![GitHub Logo2](Imagenes/Captura3.PNG)

Para ejecutar el microservicio creamos un usuario llamado microservices y asi mismo, creamos un ambiente llamado microservice_a  y lo activamos:

![GitHub Logo2](Imagenes/Captura4.PNG)

Instalamos la libeeria flask en el ambiente, creamos y ejecutamos el scrip microservice_a.py:

![GitHub Logo2](Imagenes/Captura4.1.PNG)

![GitHub Logo2](Imagenes/Captura5.PNG)

Y ejecutamos el microservicio con el comando:: $python microservice_a.py.

Se recomienda hacerlo en una sesión de screen para que corra en otro plano.

Despues de esto, creamos un archivo de configuración para el microservicio con un healthcheck:

# su consul
$ echo '{"service": {"name": "microservice-a", "tags": ["flask"], "port": 8080,
  "check": {"script": "curl localhost:8080/health >/dev/null 2>&1", "interval": "10s"}}}' >/etc/consul.d/microservice-a.json
  
 Iniciamos el agente en modo cliente (Usando una sesión de screen):
 
 ![GitHub Logo2](Imagenes/consul cliente.PNG)
 
 Unimos el cliente al ambiente de servicio (Le pasamos la ip de nuestro agente servidor):
 
 $ consul join 192.168.107.42
 
 Podemos verificar la lista de miembros del ambiente y ver que aparece nuestro agente cliente:
 
 ![GitHub Logo2](Imagenes/consul members.PNG)
 
 
 
 










5. Dentro del nodo que  esta sirviendo de web service se crea un nuevo contenedor y dentro de este contenedor se despliega un nuevo servicio 
web que en este caso seria un nuevo artefacto phyton source (un nuevo contenedor, con un servicio web diferente desplegado dentro de él), aqui 
se entraria a hacer uso del patron APi Gateway, ya que la implementación de este patron permite redijir la solicitud hacia el servicio web solicitado,
funciona como una especie de router (mirandolo desde el punto de vista de redes informaticas).  
Para la solución que nosotros proponemos es usar API Gateway. Sin embargo, hay diferentes formas de darle solución a este problema como por ejemplo:

Protcolo publicador/suscriptor



### Referencias
https://github.com/ICESI/so-microservices-python  
http://microservices.io/patterns/microservices.html
http://microservices.io/patterns/apigateway.html

[1]: images/Microservices_Deployment.png
