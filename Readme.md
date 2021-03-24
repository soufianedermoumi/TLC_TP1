# Projet Docker

Hello très chers étudiants,

Le but de ce TP est de regarder comment nous pouvons utiliser docker pour faciliter le déploiement d'une application avec la mise en place d'un serveur web en reverse proxy etc...


<!--more-->

### Etape -1: Docker

Si vous utilisez une machine perso, installez docker (voir [ici](http://olivier.barais.fr/blog/posts/teaching/istic/m2/french/2018/09/10/Operation_portable_M2_ISTIC.html))



### Etape 0: Test de votre installation (peut etre passé si vous utiliser Katacoda)###

```bash
docker run hello-world
```

Vous devriez avoir le message suivant. 


----

Hello from Docker.
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker Hub account:
 https://hub.docker.com

For more examples and ideas, visit:
 https://docs.docker.com/userguide/

 
----


```bash
docker run -t -i ubuntu /bin/bash
```

Vous récupérez un shell qui est différent de votre propre distribution. 

Tapez la commande 

```bash
apt-get update
apt-get install net-tools
/sbin/ifconfig
```

Vous constatez que l'interface réseau n'est pas la même dans le container et dans la machine hote. 

Le container vient avec sa propre interface réseau. 



### Etape 1 (si vous utilisez docker sur votre machine ou à l'ISTIC): Jouons avec docker: mise en place d'un load balancer et d'un reverse proxy avec docker et nginx

Pour le nginx en resolproxy nous allons partir de l'image [suivante](https://github.com/jwilder/nginx-proxy)

L'explication du fonctionnement est disponible [ici](http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/). 


Lancement de nginx en resolvproxy

```bash
docker run -d -p 8080:80 -v /var/run/docker.sock:/tmp/docker.sock -t jwilder/nginx-proxy 
```


Dans le suite nous allons utiliser terminator pour visualiser les effets du load-balancing (uniquement pour ceux qui sont sur leur propre portable). 

```bash
apt-get install terminator
```

Lancez Terminator en root.

```bash
sudo terminator
```


Si vous êtes sur votre propre portable, modifiez votre fichier /etc/hosts pour faire correspondre **m** vers localhost. Ce serait à faire sur votre gestionnaire de nom de domaine en temps normal.

Vous devez avoir une ligne qui ressemble à cela. 

```txt
127.0.0.1	localhost localhost.localdomain localhost4 localhost4.localdomain m
```


Pour ceux qui n'ont pas les droits root, exécutez les commandes suivantes

```bash
echo 'm localhost' >> ~/.hosts
export HOSTALIASES=~/.hosts
curl m:8080
```


Puis créer n fenètre dans votre navigateur terminator (clic droit puis split horizontal ou vertical). 
Dans ces terminales, lancez la commande suivante pour tester votre resolve proxy.

```bash
docker run -e VIRTUAL_HOST=m -t -i  nginx
```

Testez votre resolv proxy en lançant la commande suivante. 

```bash
curl m:8080
```


En tapant la commande suivante, vous pouvez regarder le fichier de configuration nginx qui sera généré à l'adresse suivante /etc/nginx/conf.d/default.conf. (N'oubliez pas de remplacer  865c1e67a00e par l'id de votre nginx en resolve proxy ($docker ps) pour récupérer la liste des containers en cours d'exécution.

```bash
docker exec -it 865c1e67a00e bash
```

- [source](http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/)


Tuez tous les dockers nginx démarrer. 

```bash
docker ps #pour avoir la liste
docker kill "IDDOCKER" #pour tuer un docker. 
```

### Etape 1 (si vous utilisez KATAcoda): Jouons avec docker: mise en place d'un load balancer et d'un reverse proxy avec docker et nginx

Pour le nginx en resolproxy nous allons partir de l'image [suivante](https://github.com/jwilder/nginx-proxy)

L'explication du fonctionnement est disponible [ici](http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/). 


Lancement de nginx en resolvproxy

```bash
docker run -d -p 8080:80 -v /var/run/docker.sock:/tmp/docker.sock -t jwilder/nginx-proxy 
```


Lancez ensuite view port (fenètre de gauche) une url qui ressemble à (https://2886795287-frugo01.environments.katacoda.com)

Sélectionnez le port 8080.


Puis créer n terminal dans votre katacode . 
Dans ces terminales, lancez la commande suivante pour tester votre resolve proxy.

```bash
# remplacer la variable virtual host en fonction de l'URL fournit par dakota quand vous avez ouvert le *viewport*
docker run -e VIRTUAL_HOST=2886795287-8080-frugo01.environments.katacoda.com -t -i  nginx
```



Testez votre resolv proxy en lançant la commande suivante. 

```bash
curl 2886795287-8080-frugo01.environments.katacoda.com:8080
```

ou dans votre navigateur
https://2886795287-8080-frugo01.environments.katacoda.com/


En tapant la commande suivante, vous pouvez regarder le fichier de configuration nginx qui sera généré à l'adresse suivante /etc/nginx/conf.d/default.conf. (N'oubliez pas de remplacer  865c1e67a00e par l'id de votre nginx en resolve proxy ($docker ps) pour récupérer la liste des containers en cours d'exécution.

```bash
docker exec -it 865c1e67a00e bash
```

- [source](http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/)


Tuez tous les dockers nginx démarrer. 

```bash
docker ps #pour avoir la liste
docker kill "IDDOCKER" #pour tuer un docker. 
```



### Etape 2: Utilisation de docker compose
Utilisez docker compose pour déployer votre vos 4 services nginx et votre loadbalancer. 


### Etape 3: Dockeriser une application existante

Nous souhaitons partir d'une application Web de détection de visage. 

Src dans ce repository

https://github.com/barais/TPDockerSampleApp (Une documentation pour compiler et lancer cette application est disponible à la fin de ce README.

Construisez le fichier docker file permettant de créer l'image docker pour cette application. 


Je vous fournis une version compilé de la librairie opencv (en 64 bit) et du jar d'opencv. 
Pour faire tourner votre application, il faudra installer le jar d'open CV dans votre repo local maven. (voir ci-après)

Testons cette application. Tout d'abord installons les dépendances nécessaire. 

Pour faire tourner dans kadacoda
```bash
apt-get update
apt-get install -y openjdk-8-jdk
apt-get install -y maven
apt-get install -f libpng16-16
apt-get install -f libjasper1
apt-get install -f libdc1394-22
```

Puis clonons le repo, vous pouvez faire un fork avant si vous souhaitez modifier l'application. 

```bash
git clone https://github.com/barais/TPDockerSampleApp
cd TPDockerSampleApp
     mvn install:install-file -Dfile=./lib/opencv-3410.jar \
     -DgroupId=org.opencv  -DartifactId=opencv -Dversion=3.4.10 -Dpackaging=jar
```

Lancez cette application.

```bash
     # Pour compiler
      mvn package
     # Pour lancer l'application
     java -Djava.library.path=lib/ -jar target/fatjar-0.0.1-SNAPSHOT.jar
     ## Si vous avez une version récente, il se peut qu'il faille utiliser, si aucune des deux versions ne marchent, recompiler opencv ou faites la tourner dans un container avec une image de docker *ubuntu:16.04*
     java -Djava.library.path=lib/ubuntuupperthan18/ -jar target/fatjar-0.0.1-SNAPSHOT.jar
```

Dans katacoda, sélectionnez sur le symbole **+** (création d'un nouveau terminal), *select port to view on client 1ù. Sélectionnez le port 8080. 

Voici votre application. 

Si vous tournez localement http://localhost:8080


**TRAVAIL A FAIRE** Construisez un fichier dockerfile permettant de créer une image docker permettant de lancer cette application. 
Vous aurez besoin d'ajoutez le répertoire *lib* et le répertoire *haarcascades* à votre image. 

Nous souhaitons faire en sorte de fournir une image docker finale la plus petite possible. (Un paquet de carambar à la plus petite image fonctionnelle)


**Version longue (non obligatoire)**

Si l'on voulait vraiment être reproductible, vous auriez besoin de construire open cv depuis les src (principalement depuis la version 3.4)

https://github.com/opencv/opencv

Vous pourrez utiliser cette documentation pour la compilation d'opencv sur ubuntu.

https://advancedweb.hu/2016/03/01/opencv_ubuntu/

j'ai mis un exemple de dockerfile pour batir opencv dans le repository

N'oubliez pas d'installer ant au sein de votre image docker ainsi que la jvm et maven. 

Nous souhaitons faire en sorte de fournir une image docker finale la plus petite possible. (Un paquet de carambar à la plus petite image fonctionnelle ;). Besoin d'utiliser alpine pour créer opencv pour Java et builder le fatjar de ce projet puis besoin de créer un runtime avec juste le fatjar de l'appli la libopencv et ses dépendances.

Fournissez donc deux fichiers docker file, un premier pour construire l'image qui permet de compiler opencv et compiler votre application. Un deuxième qui permet de construire l'image minimale pour votre application. 

### Etape 4: Dockeriser une application existante

Fournir un docker compose qui permet de mettre en place une application avec 4 instances de votre serveur Web. 


### Etape 5: Dockeriser une application existante

En utilisant [https://labs.play-with-k8s.com/](https://labs.play-with-k8s.com/), déployer votre service à l'aide de kubernetes. 



# How to compile this application

Simple example of using OpenCV in a Web application build using jersey.

This application takes a picture using web browsers camera API (available  in modern browsers) 
and runs OpenCV face recognition algorithm (using [CascadeClassifier](http://docs.opencv.org/java/org/opencv/objdetect/CascadeClassifier.html) ) for it. If a face is detected a "troll face" is added  on top of it.

![Screenshot](/screenshot.png?raw=true "Screenshot")

This application was inspired by the ingenious ["Trollator" mobile Android application](https://play.google.com/store/apps/details?id=com.fredagapps.android.trollator).

1. OpenCV Installation for local Maven repository
---
OpenCV is a native library with Java bindings so you need to install this to your system.
 - *libopencv_java3410.so* installed in you java.library.path (
 - *opencv-3410.jar* availble for application

There are good instructions how to build OpenCV with Java bindings for your own platform here: http://docs.opencv.org/doc/tutorials/introduction/desktop_java/java_dev_intro.html

Once you have built the Java library you can install the resulting jar file to your local Maven repository using
     mvn install:install-file -Dfile=./lib/opencv-3410.jar \
     -DgroupId=org.opencv  -DartifactId=opencv -Dversion=3.4.10 -Dpackaging=jar


2. Building this application
----
Once OpenCV jar library is available as a local Maven dependency, you can clone and build this application simply using Git and Maven:

```bash
     mvn install
```

And run the application using the embedded Jetty plugin in http://localhost:8080

```bash
     mvn package

     java -Djava.library.path=lib/ -jar target/fatjar-0.0.1-SNAPSHOT.jar
	# Do not forget to update the path to your opencv install in Main.java
	# You can change the image trollface ;)
```

# Fork this repo


If you fork this repo, to be up to date. 

```sh
git remote add upstream https://github.com/barais/TPDockerSampleApp
git fetch upstream
git checkout master
git merge upstream/master
```

