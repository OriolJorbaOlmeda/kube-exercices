Creación de los objetos: El primer objecto que debemos crear es el replicaSet, ya que es el que nos permite crear el número de copias deseadas del Pod con sus especificaciones. Se puede ver en el fichero replica.yaml, como queda el código.
El segundo paso es crear el service, el qual va a permitir exponer nuestra aplicación. Como se ve en el fichero service.yaml, se expone el purto 80 de los pods.
Finalmente, para poder manejar de forma externa los servicios, hay que crear el IngressController, como se ve en el fichero ingress.yaml. El host serà oriol-jorba.student.lasalle.com y el path '/'

- kubectl apply -f replica.yaml
- kubectl apply -f service.yaml
- kubectl apply -f ingress.yaml

Pregunta a: Para que se pueda acceder a http://oriol-jorba.student.lasalle.com hay que modificar el fichero host de nuestro local. Una vez detectado ese fichero, hay que introducirle la IP de minikube, la cual se puede obtener con el comando minkube ip (192.168.59.100), y el host con el cual queremos entrar, oriol-jorba.student.lasalle.com.
Con estos valores introducidos, y todos los objetos en funcionamiento, solo debemos ir al buscador e introducir: http://http://oriol-jorba.student.lasalle.com/ , y aparecera la pantalla de nginx, como se puede ver en la (Imagen_2.png)

Pregunta b: En segundo lugar, y para poder ver la misma pàgina web, pero esta vez con https://, debemos crear un certificado tls. En este caso, yo lo he hecho con openssl. Una vez creados el certificado, debemos crear un objeto secret. Este se puede crear de forma senzilla con el comando:
k create secret tls secret --cert=server.cer --key=server.key
El certificado y la llave los he guardado en la misma carpeta, así es más senzillo acceder a ellos. Con el objeto secret creado, solo nos falta crear un nuevo Ingress, o modificar le del apartado anterior, para que haga uso del secret. En este caso he creado uno nuevo con el nombre Ingres2.yaml, donde se indica que en tls: haga uso del objeto secret con el host oriol-jorba.student.lasalle.com.
Si finalmente nos dirigimos al buscador y escribimos:
https://http://oriol-jorba.student.lasalle.com, primero nos idicara que no es seguro entrar, pero entramos igualmente, como indica el enunciado. Finalmente podremos ver la pàgina de nginx, como se puede ver la (Imagen_3.png). Si le damos al candado, o en este caso al símbolo de atención, podremos ver el certificado que se ha usado, como en la (Imagen_4.png).
En este caso, he tenido que añadir el certificado a la raiz CA global para que fuera válido, ya que sino no se vería como tal en la última imágen.
