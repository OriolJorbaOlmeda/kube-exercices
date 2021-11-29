Aparatdo 1: Primero hay que crear diferentes objetos para garantizar su funcionamiento. El primero serà un service, el cual se va a encargar de gestionar las IPs de los Pods. Se puede ver la configuración del service en el fichero service.yaml. Lo más importante a remarcar de este fichero es el spec.clusterIP: none. Con esto creamos un service headless, de esta forma podremos acceder a cada pod de forma individual, muy útil para aplicaciones con estado, justo lo que utilizamos en este ejercicio. Al aplicar:
kubectl apply -f service.yaml y después kubectl get svc, veremos que no hay clusterIP como se puede ver en la (Imagen_5.png).
En segundo lugar hay que crear un objeto StorageClass, este hace una funcion parecida a los volumenes de Docker. Es decir, son volumenes persistentes. En el fichero storage.yaml, se puede ver su estructura.
A continuación, se debe crear el objeto SatefulSet como se puede ver en el fichero state.yaml. Utilizaremos la imgen de mongodb: mongo:3.6, con 3 replicas.
Una vez esten estos tres objetos levantados, escribiendo kubectl get pods se deberia ver lo que podemos ver en la (Imagen_6.png).

Con todos los objetos levantados, hace falta que los tres pods se puedan administrar con un administrador y un replicaSet. para ello, debemos entrar dentro del pod mongod-0 con:
kubectl exec -it mongod-0 bash.
Una vez dentro, tenemos que comprobar cual es el hostname de nuestro cluster de mongo con hostname -f, el resultados e puede ver la (Imagen_7.png).
A continuación debemos crear el replicaSet. introduciremos el comando -mongo el cual nos va apermitir configurar la configuración de mongodb. A continuación, debemos introducir los camando para hacer a los tres Pods miembros:

rs.initiate({ \_id: "MainRepSet", version: 1,
members: [
{ _id: 0, host: "mongod-0.mongodb-service.default.svc.cluster.local:27017" },
{ _id: 1, host: "mongod-1.mongodb-service.default.svc.cluster.local:27017" },
{ _id: 2, host: "mongod-2.mongodb-service.default.svc.cluster.local:27017" } ]});

Si a continuación escribimos rs.status(), se debe ver a los diferentes miembros del cluster, con sus diferentes id. En la (Imagen_8.png), se puede ver esta configuración.
Si depués entramos en ostro de los pods y escribimos -mongo y depués -rs.statu(), también se verá es configuración.

Apartado 2: Una vez habilitado el clúster de MongoDB. Debemos hacer algún cambio en uno de los nodos secundarios de modo que este cambio se pueda ver en cualquiera de los otros pods.
En este caso, he decidido crear un usuario en mongod-0, y verlo en mongod-1.
Para crearlo, he utilizado los comandos que se pueden ver en la (Imagen_9.png) Se puede ver que se ha creado un usuario nombrado "myTester".
Si a continuación nos dirigimos a mongod-1, e introducimos primero rs.secondaryOk(), para que nos de permisos de lectura en los otros miembros que no sean users, y después aplicamos show users, veremos que este usuario tambien esta creado en el otro nodo. (Imagen_10.png). Así que podemos verificar que el cambio se ha aplicado.

Apartado 3: Con un replicaSet, habriamos tenido la desventaja de que todas las replicas comparten un mismo volumen, mientras que en el SatetfulSet cada replica tiene el suyo propio, así cada uno tiene su propio storage y si por si algún caso, se elimina uno de los pods, esta información no se pierde. En cambio, con un replicaSet, si matamos un pod, y lo volvemos a crear, este no tiene indicado que información tiene, ya que sería aleatorio. Por eso, en aplicsciones con estado (bases de datos por ejemplo), necesitan de esta ventaja.
