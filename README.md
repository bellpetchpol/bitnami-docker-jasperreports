[![CircleCI](https://circleci.com/gh/bitnami/bitnami-docker-jasperreports/tree/master.svg?style=shield)](https://circleci.com/gh/bitnami/bitnami-docker-jasperreports/tree/master)

# What is JasperReports Server?

> The JasperReports Server can be used as a stand-alone or embedded reporting and BI server that offers web-based reporting, analytic tools and visualization, and a dashboard feature for compiling multiple custom views. JasperReports Server supports multiple data sources including Hadoop Hive, JSON data sources, Excel, XML/A, Hibernate and more. You can create reports with their WYSIWYG tool and build beautiful visualizations, charts and graphs.

http://community.jaspersoft.com/project/jasperreports-server

# TL;DR;

## Docker Compose

```bash
$ curl -sSL https://raw.githubusercontent.com/bitnami/bitnami-docker-jasperreports/master/docker-compose.yml > docker-compose.yml
$ docker-compose up -d
```

# Why use Bitnami Images?

* Bitnami closely tracks upstream source changes and promptly publishes new versions of this image using our automated systems.
* With Bitnami images the latest bug fixes and features are available as soon as possible.
* Bitnami containers, virtual machines and cloud images use the same components and configuration approach - making it easy to switch between formats based on your project needs.
* Bitnami images are built on CircleCI and automatically pushed to the Docker Hub.
* All our images are based on [minideb](https://github.com/bitnami/minideb) a minimalist Debian based container image which gives you a small base container image and the familiarity of a leading linux distribution.

# How to deploy JasperReports Server in Kubernetes?

Deploying Bitnami applications as Helm Charts is the easiest way to get started with our applications on Kubernetes. Read more about the installation in the [Bitnami JasperReports Server Chart GitHub repository](https://github.com/bitnami/charts/tree/master/upstreamed/jasperreports).

Bitnami containers can be used with [Kubeapps](https://kubeapps.com/) for deployment and management of Helm Charts in clusters.

# Supported tags and respective `Dockerfile` links

> NOTE: Debian 8 images have been deprecated in favor of Debian 9 images. Bitnami will not longer publish new Docker images based on Debian 8.

Learn more about the Bitnami tagging policy and the difference between rolling tags and immutable tags [in our documentation page](https://docs.bitnami.com/containers/how-to/understand-rolling-tags-containers/).


* [`7-ol-7`, `7.1.0-ol-7-r97` (7/ol-7/Dockerfile)](https://github.com/bitnami/bitnami-docker-jasperreports/blob/7.1.0-ol-7-r97/7/ol-7/Dockerfile)
* [`7-debian-9`, `7.1.0-debian-9-r76`, `7`, `7.1.0`, `7.1.0-r76`, `latest` (7/debian-9/Dockerfile)](https://github.com/bitnami/bitnami-docker-jasperreports/blob/7.1.0-debian-9-r76/7/debian-9/Dockerfile)

Subscribe to project updates by watching the [bitnami/jasperreports GitHub repo](https://github.com/bitnami/bitnami-docker-jasperreports).


# Prerequisites

To run this application you need Docker Engine 1.10.0. Docker Compose is recomended with a version 1.6.0 or later.

# How to use this image

## Run the application using Docker Compose

This is the recommended way to run JasperReports Server. You can use the following docker compose template:

```yaml
version: '2'

services:
  mariadb:
    image: 'bitnami/mariadb:latest'
    environment:
      - MARIADB_USER=bn_jasperreports
      - MARIADB_DATABASE=bitnami_jasperreports
      - ALLOW_EMPTY_PASSWORD=yes
    volumes:
      - mariadb_data:/bitnami
  jasperreports:
    image: bitnami/jasperreports:latest
    environment:
      - MARIADB_HOST=mariadb
      - MARIADB_PORT=3306
      - JASPERREPORTS_DATABASE_USER=bn_jasperreports
      - JASPERREPORTS_DATABASE_NAME=bitnami_jasperreports
      - ALLOW_EMPTY_PASSWORD=yes
    depends_on:
      - mariadb
    ports:
      - '80:8080'
      - '443:8443'
    volumes:
      - jasperreports_data:/bitnami

volumes:
  mariadb_data:
    driver: local
  jasperreports_data:
    driver: local
```

Then you can access your application at http://your-ip/. Enter bitnami default username and password `user/ bitnami`

## Run the application manually

If you want to run the application manually instead of using docker-compose, these are the basic steps you need to run:

1. Create a new network for the application:

  ```bash
  $ docker network create jasperreports-tier
  ```

2. Create a volume for MariaDB persistence and create a MariaDB container

  ```bash
  $ docker volume create --name mariadb_data
  $ docker run -d --name mariadb \
    -e ALLOW_EMPTY_PASSWORD=yes \
    -e MARIADB_USER=bn_jasperreports \
    -e MARIADB_DATABASE=bitnami_jasperreports \
    --net jasperreports-tier \
    --volume mariadb_data:/bitnami \
    bitnami/mariadb:latest
  ```

3. Create volumes for JasperReports persistence and launch the container

  ```bash
  $ docker volume create --name jasperreports_data
  $ docker run -d --name jasperreports -p 80:8080 \
    -e ALLOW_EMPTY_PASSWORD=yes \
    -e JASPERREPORTS_DATABASE_USER=bn_jasperreports \
    -e JASPERREPORTS_DATABASE_NAME=bitnami_jasperreports \
    --net jasperreports-tier \
    --volume jasperreports_data:/bitnami \
    bitnami/jasperreports:latest
  ```

Then you can access your application at http://your-ip/. Enter bitnami default username and password:
`user/ bitnami`

>**Note!** If you are using **Docker for Windows** (regardless of running the application using Docker compose or manually) you must check the Docker virtual machine IP executing this command:

`docker-machine ip`

This IP address allowing you to access to your application.

## Persisting your application

If you remove the container all your data and configurations will be lost, and the next time you run the image the database will be reinitialized. To avoid this loss of data, you should mount a volume that will persist even after the container is removed.

For persistence you should mount a volume at the `/bitnami` path. Additionally you should mount a volume for [persistence of the MariaDB data](https://github.com/bitnami/bitnami-docker-mariadb#persisting-your-database).

The above examples define docker volumes namely `mariadb_data` and `jasperreports_data`. The JasperReports Server state will persist as long as these volumes are not removed.

To avoid inadvertent removal of these volumes you can [mount host directories as data volumes](https://docs.docker.com/engine/tutorials/dockervolumes/). Alternatively you can make use of volume plugins to host the volume data.

### Mount host directories as data volumes with Docker Compose

This requires a minor change to the `docker-compose.yml` template previously shown:
```yaml
version: '2'
services:
  mariadb: bitnami/mariadb:latest
  environment:
    - ALLOW_EMPTY_PASSWORD=yes
    - MARIADB_USER=bn_jasperreports
    - MARIADB_DATABASE=bitnami_jasperreports
  volumes:
    - /path/to/mariadb-persistence:/bitnami
jasperreports:
  image: bitnami/jasperreports:latest
  environment:
    - JASPERREPORTS_DATABASE_USER=bn_jasperreports
    - JASPERREPORTS_DATABASE_NAME=bitnami_jasperreports
    - ALLOW_EMPTY_PASSWORD=yes
  depends_on:
    - mariadb
  ports:
    - 80:8080
  volumes:
    - /path/to/jasperreports-persistence:/bitnami
```

### Mount persistent folders manually

In this case you need to specify the directories to mount on the run command. The process is the same than the one previously shown:

1. Create a network (if it does not exist):

  ```bash
  $ docker network create jasperreports-tier
  ```

2. Create a MariaDB container with host volume:

  ```bash
  $ docker run -d --name mariadb \
    -e ALLOW_EMPTY_PASSWORD=yes \
    -e MARIADB_USER=bn_jasperreports \
    -e MARIADB_DATABASE=bitnami_jasperreports \
    --net jasperreports-tier \
    --volume /path/to/mariadb-persistence:/bitnami \
   bitnami/mariadb:latest
  ```

3. Create the JasperReports Server container with host volume:

  ```bash
  $  docker run -d --name jasperreports -p 80:8080 \
    -e ALLOW_EMPTY_PASSWORD=yes \
    -e JASPERREPORTS_DATABASE_USER=bn_jasperreports \
    -e JASPERREPORTS_DATABASE_NAME=bitnami_jasperreports \
    --net jasperreports-tier \
    --volume /path/to/jasperreports-persistence:/bitnami \
    bitnami/jasperreports:latest
  ```

# Upgrade this application

Bitnami provides up-to-date versions of JasperReports Server, including security patches, soon after they are made upstream. We recommend that you follow these steps to upgrade your container. We will cover here the upgrade of the JasperReports Server container.

1. Get the updated images:

  ```bash
  $ docker pull bitnami/jasperreports:latest
  ```

2. Stop your container

  * For docker-compose: `$ docker-compose stop jasperreports`
  * For manual execution: `$ docker stop jasperreports`

3. Take a snapshot of the application state

```bash
$ rsync -a /path/to/jasperreports-persistence /path/to/jasperreports-persistence.bkp.$(date +%Y%m%d-%H.%M.%S)
```

Additionally, [snapshot the MariaDB data](https://github.com/bitnami/bitnami-docker-mariadb#step-2-stop-and-backup-the-currently-running-container)

You can use these snapshots to restore the application state should the upgrade fail.

4. Remove the currently running container

  * For docker-compose: `$ docker-compose rm -v jasperreports`
  * For manual execution: `$ docker rm -v jasperreports`

5. Run the new image

  * For docker-compose: `$ docker-compose up jasperreports`
  * For manual execution ([mount](#mount-persistent-folders-manually) the directories if needed): `docker run --name jasperreports bitnami/jasperreports:latest`

# Configuration

## Environment variables

When you start the jasperreports image, you can adjust the configuration of the instance by passing one or more environment variables either on the docker-compose file or on the docker run command line.

##### User and Site configuration

 - `JASPERREPORTS_USERNAME`: JasperReports Server admin username. Default: **user**
 - `JASPERREPORTS_PASSWORD`: JasperReports Server admin password. Default: **bitnami**
 - `JASPERREPORTS_EMAIL`: JasperReports Server admin email. Default: **user@example.com**

##### Use an existing database

- `MARIADB_HOST`: Hostname for MariaDB server. Default: **mariadb**
- `MARIADB_PORT_NUMBER`: Port used by MariaDB server. Default: **3306**
- `JASPERREPORTS_DATABASE_NAME`: Database name that JasperReports will use to connect with the database. Default: **bitnami_jasperreports**
- `JASPERREPORTS_DATABASE_USER`: Database user that JasperReports will use to connect with the database. Default: **bn_jasperreports**
- `JASPERREPORTS_DATABASE_PASSWORD`: Database password that JasperReports will use to connect with the database. No defaults.
- `ALLOW_EMPTY_PASSWORD`: It can be used to allow blank passwords. Default: **no**

##### Create a database for JasperReports using mysql-client

- `MARIADB_HOST`: Hostname for MariaDB server. Default: **mariadb**
- `MARIADB_PORT_NUMBER`: Port used by MariaDB server. Default: **3306**
- `MARIADB_ROOT_USER`: Database admin user. Default: **root**
- `MARIADB_ROOT_PASSWORD`: Database password for the `MARIADB_ROOT_USER` user. No defaults.
- `MYSQL_CLIENT_CREATE_DATABASE_NAME`: New database to be created by the mysql client module. No defaults.
- `MYSQL_CLIENT_CREATE_DATABASE_USER`: New database user to be created by the mysql client module. No defaults.
- `MYSQL_CLIENT_CREATE_DATABASE_PASSWORD`: Database password for the `MYSQL_CLIENT_CREATE_DATABASE_USER` user. No defaults.
- `ALLOW_EMPTY_PASSWORD`: It can be used to allow blank passwords. Default: **no**

If you want to add a new environment variable:

 * For docker-compose add the variable name and value under the application section:

  ```yaml
  jasperreports:
    image: bitnami/jasperreports:latest
    ports:
      - 80:8080
    environment:
      - JASPERREPORTS_PASSWORD=my_password
  ```

 * For manual execution add a `-e` option with each variable and value:

  ```bash
  $ docker run -d -e JASPERREPORTS_PASSWORD=my_password -p 80:8080 --name jasperreports -v /your/local/path/bitnami/jasperreports:/bitnami --network=jasperreports-tier bitnami/jasperreports
  ```

### SMTP Configuration

To configure JasperReports Server to send email using SMTP you can set the following environment variables:

 - `SMTP_HOST`: SMTP host.
 - `SMTP_PORT`: SMTP port.
 - `SMTP_EMAIL`: SMTP email.
 - `SMTP_USER`: SMTP account user.
 - `SMTP_PASSWORD`: SMTP account password.
 - `SMTP_PROTOCOL`: SMTP protocol.

This would be an example of SMTP configuration using a GMail account:

 * docker-compose:

```yaml
  jasperreports:
    image: bitnami/jasperreports:latest
    ports:
      - 80:8080
    environment:
      - MARIADB_HOST=mariadb
      - MARIADB_PORT_NUMBER=3306
      - JASPERREPORTS_DATABASE_USER=bn_jasperreports
      - JASPERREPORTS_DATABASE_NAME=bitnami_jasperreports
      - SMTP_HOST=smtp.gmail.com
      - SMTP_PORT=587
      - SMTP_EMAIL=your_email@gmail.com
      - SMTP_USER=your_email@gmail.com
      - SMTP_PASSWORD=your_password
```

 * For manual execution:

```bash
 $ docker run -d -p 80:8080 --name jasperreports --net=jasperreports-tier \
    -e MARIADB_HOST=mariadb \
    -e MARIADB_PORT=3306 \
    -e ALLOW_EMPTY_PASSWORD=yes \
    -e JASPERREPORTS_DATABASE_USER=bn_jasperreports \
    -e JASPERREPORTS_DATABASE_NAME=bitnami_jasperreports \
    -e SMTP_HOST=smtp.gmail.com \
    -e SMTP_PORT=587 \
    -e SMTP_USER=your_email@gmail.com \
    -e SMTP_PASSWORD=your_password \
    -v /your/local/path/bitnami/jasperreports:/bitnami \
    bitnami/jasperreports
```
### View Jasperserver.logs

Add this line in the jasperreports volumes section of **docker-compose.yml**
```
- ./your-path/your-folder:/bitnami/tomcat/data/jasperserver/WEB-INF/logs
```
Actually you can view any file in the jasperserver with this method

### Add new font.jar

Add this line in the jasperreports volumes section of **docker-compose.yml**
```
- ./your-path/fontname.jar:/bitnami/tomcat/data/jasperserver/WEB-INF/lib/fontname.jar:ro
```

# Contributing

We'd love for you to contribute to this container. You can request new features by creating an [issue](https://github.com/bitnami/bitnami-docker-jasperreports/issues), or submit a [pull request](https://github.com/bitnami/bitnami-docker-jasperreports/pulls) with your contribution.

# Issues

If you encountered a problem running this container, you can file an [issue](https://github.com/bitnami/bitnami-docker-jasperreports/issues). For us to provide better support, be sure to include the following information in your issue:

- Host OS and version
- Docker version (`docker version`)
- Output of `docker info`
- Version of this container (`echo $BITNAMI_IMAGE_VERSION` inside the container)
- The command you used to run the container, and any relevant output you saw (masking any sensitive information)

# License

Copyright 2016-2018 Bitnami

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
