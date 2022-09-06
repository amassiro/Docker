# Docker

INFN course

    https://hub.docker.com/

    container e' l'immagine che viene instanziata

Basis
====

    docker container run --name blablabla  hello-world


    docker image ls

    docker container ps -a

    docker container ps

    docker system df

    docker image inspect hello-world



docker cp: copiare file dall'host dentro il container
    
    docker cp bla.txt   blablabla:/


Quanto sta nell'immagine NON viene modificato, ed e' condiviso da TUTTI i container
-> file system READ ONLY



    docker rm  blablabla



docker container run --name ciccia -it ubuntu bash
	-it --> versione interattiva



    docker start ce7ad8208f0a

    docker attach ce7ad8208f0a
--> attaccare al processo principale
--> attenzione che questo puo' portare a stoppare il container!


    docker exec -it ce7ad8208f0a   bash
--> faccio partire una nuova bash, nello stesso container

    docker exec -it ce7ad8208f0a ls /

    ps aux




    docker commit blablabla hello-world:MIO
--> creo una nuova immagine!!
--> ok per test, ma poi meglio usare un docker file


    docker container prune 
--> remove all stopped containers




Variabili d'ambiente
====

--env VARIABLE1=foobar



Data manager
====

    docker volume create my-vol
--> finisce qui: /var/lib/docker/volumes/

    docker volume ls

docker container run -it --name miocont --mount type=volume,source=my-vol,destination=/tmp/   ubuntu

docker help volume

- NB: Containers must have their volumes configured on startup (si potrebbe fare hackerando diversi files ...)






    docker container run -d -p 80:80 --name nginx2 nginx




Lab challenge
====


    docker container run -d -p 8080:80 --name mio-wordpress wordpress

    docker container run --name mio-mariadb mariadb

    docker container ps


Then on chrome go to http://192.168.28.223:8080/



docker volume create db_data



docker container run --name mio-mariadb2 mariadb:10.6.4-focal

https://github.com/docker-library/docs/blob/master/mariadb/README.md

docker run --detach --name some-mariadb --env MARIADB_USER=example-user --env MARIADB_PASSWORD=my_cool_secret --env MARIADB_ROOT_PASSWORD=my-secret-pw  mariadb:10.6.4-focal



docker run --detach -v db_data:/var/lib/mysql --name some-mariadb2 --env MARIADB_USER=example-user --env MARIADB_PASSWORD=my_cool_secret --env MARIADB_ROOT_PASSWORD=my-secret-pw  mariadb:10.6.4-focal


    docker inspect some-mariadb2


--> 172.17.0.4



docker container run -d -p 8081:80 --env WORDPRESS_DB_HOST=172.17.0.4  --env WORDPRESS_DB_USER=example-user  --env WORDPRESS_DB_PASSWORD=my_cool_secret --env WORDPRESS_DB_NAME=db_data  --name mio-wordpress4 wordpress:latest


    --mount type=volume,source=my-vol,destination=/tmp/ 


-e    <---->   --env


docker volume prune





    docker volume create db_data
    docker container run -d --name db \
	--mount type=volume,source=db_data,destination=/var/lib/mysql \
	--env MYSQL_RANDOM_ROOT_PASSWORD="1" \
	--env MYSQL_DATABASE=wordpress \
	--env MYSQL_USER=wordpress-user \
	--env MYSQL_PASSWORD=wordpress-password \
	mariadb:10.6.4-focal

    #docker container inspect db | grep IPAddress ....
    DB_CONTAINER_IP=$(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' db)

    docker container run -d --name wordpress \
	-p 80:80 \
	--env WORDPRESS_DB_HOST=$DB_CONTAINER_IP \
	--env WORDPRESS_DB_NAME=wordpress \
	--env WORDPRESS_DB_USER=wordpress-user \
	--env WORDPRESS_DB_PASSWORD=wordpress-password \
	wordpress:latest






Network
====

    docker network create my-net

    docker network connect my-net my-cont

    docker network disconnect my-net my-cont


Swarm: overlay network to connect different hosts


Challenge:

    docker network create my-net

    docker volume create db_data
    
    docker container run -d --name db \
	--mount type=volume,source=db_data,destination=/var/lib/mysql \
	--env MYSQL_RANDOM_ROOT_PASSWORD="1" \
	--env MYSQL_DATABASE=wordpress \
	--env MYSQL_USER=wordpress-user \
	--env MYSQL_PASSWORD=wordpress-password \
	mariadb:10.6.4-focal

    docker network connect my-net db

    docker container run -d --name wordpress \
	-p 80:80 \
	--env WORDPRESS_DB_HOST=db \
	--env WORDPRESS_DB_NAME=wordpress \
	--env WORDPRESS_DB_USER=wordpress-user \
	--env WORDPRESS_DB_PASSWORD=wordpress-password \
	wordpress:latest




Create images
====

Write a Dockerfile


    docker build -t myfiglet .

L'immagine finisce qui:

    sudo ls /var/lib/docker/image/



Continuous Integration (CI)
====









