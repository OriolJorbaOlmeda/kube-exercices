Para este apartadom, vamos a hacer uso de un deployment y un servicio basados en una imagen de nginx. Se puede ver la configuración en los fichero service.yaml y deploy.yaml en esta msima carpeta. La configuración de las resources de los pods la he bajado a la mitad ya que no me escalava si no lo bajaba. Una vez creados y corriendo hay que crear el hpa. Este lo creamos en el fichero hpa.yaml. Con un mínimo de 2 replicas y un máximo de 10. El umbral serà del 50%.
Al aplicar kubectl apply -f hpa.yaml y deespués k get all. podemos ver que como no es requerida mucha cpu, hace uso solo del mínimo que necesita (Imagen_11.png).
Una vez el comando de stress (kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://nginx; done") ha hecho passar el umbral, poco a poco se han ido añadiendo nuevos pods, hasta llegar a 4, como se puede ver en la (Imagen_12.png), es decir, ha realizado un autoescalado cuando ha pasado ese umbral. Si miramos los pods que tenemos, podremos ver que habrá 4 levantados (Imagen_13.png).