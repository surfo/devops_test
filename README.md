##CI/CD

Levantar Jenkins
docker run -p 8080:8080 -p 50000:50000 --name jenkinsblue jenkins/blueocean_ci



Ciclo completo
Realizar modificaciones en el codigo
Enviar la definicion de una imagen
Ejecutar un pipeline para que realice las validaciones del codigo y test
Que se construya la imagen y se envie a un repositorio de imagenes (Docker Hub)
Desplegar la imagen en un cluster de Kubernetes
	Levantar minikube
	$ minikube start
	$ minikube status
	$ minikube dashboard
	$ dockes ps 
conectar al contenedor de Jenkins (para instalar el kubectl con el nombre del contenedor de jenkins(jenkinsblue))
$ docker exec -it --user=root jenkinsblue /bin/bash
Comando de Instalacion de kubectl en el contenedor de jenkins
$# curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl" && chmod +x kubectl
Lo movemos al local donde tiene que quedar los binarios
mv ./kubectl /usr/local/bin/kubectl
Verficamos la version del kubectl
$# kubectl version --client
Nos desconectamos del contenedor
$# exit
Conectar el contenedor de jenkins a la misma red del contenedor de minikube
$ docker network ls
Nos interesa el network ID de minikube y lo conectamos con el nombre del contenedor de jenkins
$ docker network connect minikube jenkinsblue
Verificamos 
$ docker container inspect jenkinsblue 
IMPORTANTE: Luego de usar esta coneccion desconectarlo con el comando $ docker network disconnect minikube jenkinsblue
En jenkins instalamos el plugins de kubernetes
Preparar los recursos en los archivos yaml
Aplicamos los archivos yaml al cluster de minikube desde el directorio donde lo alojamos
$ kubectl apply -f jenkins-account.yaml
Vemos la configuracion del cluster
$ kubectl config view
Tomamos la url de los certificados del cluster, la url del cluster
vemos el service account desde este comando
$ kubectl --namespace default get serviceaccount
vemos los detalles de ese service account
Obtenemos el name: del token de ese service account y lo usemos (jenkins-token-tp259)
$ kubectl describe secrets/jenkins-token-tp259
Obtenemos el token para poder conectar a la api del cluster de kubernetes, vamos a jenkins y agregamos el token en el usuario admin de jenkins - Credentials - Stores from parent - add credentials - secret text y pegamos el token en secret y en ID pegamos el credentialsId del archivo jenkinsfile (kubernete-jenkis-server-account)
Instalo el plugin de google Cloud Kubernetes
Luego vamos a panel de control de jenkins - administrar jenkins - configurar el sistema y buscamos la opcion cloud
Configuramos la conectividad del cluster de kubernetes (50)
Crear el Pipeline
