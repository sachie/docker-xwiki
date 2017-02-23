Provides several full Docker environments for running XWiki with an automated HTTPS server using Nginx and Let's Encrypt.

The following configurations are currently supported:
* Two Docker containers with one container for running latest MySQL 5.x database (configured to use UTF8 and be 
case-insensitive) and another container for running the latest Tomcat 8 + Java 8 + XWiki (the version depends on the 
branch/tag you use).

All source files are under the LGPL 2.1 license.

# Assumptions

The goal is to provide a production-ready XWiki system running in Docker. This why:
* The OS is based on Debian and not on some smaller-footprint distribution like Alpine
* Several containers are used with Docker Compose: for the DB, XWiki + Servlet, NGINX and Let's Encrypt. This 
  allows the ability to run them on different machines for example. 

# Using

You should first install [Docker](https://www.docker.com/) on your machine.

* Install Git and run `git clone https://github.com/xwiki-contrib/docker-xwiki.git` or download the sources from
the GitHub UI.
* Go to the directory with the configuration file: `cd xwiki-mysql-tomcat`.
* Run `docker-compose up` 
* Start a browser and point it to `http://localhost:8080`

Note that if you want to set a custom version of XWiki you can checkout `master` and edit the `env` file and set the 
values you need in there. It's also possible to override them on the command line with 
`docker-compose run -e "XWIKI_VERSION=8.4.4"`.

Note that `docker-compose up` will automatically build the XWiki image on the first run. If you need to rebuild it 
you can issue `docker-compose up --build`. You can also build the image with
`docker build . -t xwiki-mysql-tomcat:latest` for example.

# Details for xwiki-mysql-tomcat

## Configuration Options

The first time you create a container out of the xwiki image, a shell script (/usr/local/bin/start_xwiki.sh`) is 
executed in the container to setup some configuration. The following environment variables can be passed:

* `MYSQL_USER`: The MySQL user name used by XWiki to read/write to the DB.
* `MYSQL_PASSWORD`: The MySQL user password used by XWiki to read/write to the DB.

## Miscellaneous

Volumes:
* Three volumes are created:
  * A volume named `<prefix>_mysql-data` that contains the database data.
  * A volume named `<prefix>_tomcat-data` that contains the tomcat server data.
  * A volume named `<prefix>_xwiki-data` that contains XWiki's permanent directory.
* To find out where those volumes are located on your local host machine you can inspect them with `docker volume inspect <volume name>`. To find the volume name, you can list all volumes with `docker volume ls`. 

MySQL:
* To issue some mysql commands:
 * Find the container id with `docker ps` 
 * Execute bash in the mysql container: `docker exec -it <containerid> bash -l`
 * Once inside the mysql container execute the `mysql` command: `mysql --user=xwiki --password=xwiki`
