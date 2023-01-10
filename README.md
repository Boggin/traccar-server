# Traccar Server

Create the traccar-server with a standalone MySQL database and Traefik reverse proxy via a docker-compose file.

## VPS configuration (minimum)

- OS: Ubuntu 16.04 x64
- RAM: 512 MB
- CPU: 1
- Disk space: 10 GB
- 1GB swap: see [Swapfile](#swapfile) below

## Setup Instructions

### Swapfile

DigitalOcean instructions on adding a 1GB swapfile: [How To Add Swap Space on Ubuntu 22.04](https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-22-04).

### Docker

DigitalOcean instructions for installing Docker: [How To Install and Use Docker on Ubuntu 22.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)

### Docker Compose

DigitalOcean instructions for installing Docker Compose: [How To Install and Use Docker Compose on Ubuntu 22.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-22-04)

### Install the repository into the VPS

1. Clone this repository.
1. Step into the just created folder.

### Configure environment variables

1. Set your mysql username and password parameters as environment variables:  
`export MYSQL_USER=YOUR_USERNAME` `export MYSQL_ROOT_PASSWORD=YOUR_ROOT_PASSWORD` `export MYSQL_PASSWORD=YOUR_USER_PASSWORD` `export MYSQL_DATABASE=YOUR_DATABASE_NAME`
2. Set your domain:  
`export DOMAIN=YOUR_DOMAIN.com`
3. Alternatively you can set all these variables in `.env` file and export all at once on the system startup by adding the following command to your .bashrc:  
`export $(<PATH_TO_THE_REPOSITORY/.env)`

### Customize your volumes in the `docker-compose.yml` file according to the customizations you want to have

1. Set your mysql username and password parameters as environment variables:  
`export MYSQL_USER=YOUR_USERNAME`  
`export MYSQL_ROOT_PASSWORD=YOUR_ROOT_PASSWORD`  
`export MYSQL_PASSWORD=YOUR_USER_PASSWORD`  
`export MYSQL_DATABASE=YOUR_DATABASE_NAME`
2. Set your domain:  
`export DOMAIN=YOUR_DOMAIN.com`
3. Alternatively you can set all these variables in `.env` file and export all at once on the system startup by adding the following command to your .bashrc:  
`export $(<PATH_TO_THE_REPOSITORY/.env)`

### Create the traccar configuration file

1. Create a `traccar.xml` file with your configuration inside the folder `traccar/conf/`
1. A sample configuration file is provided on `traccar/conf/sample_traccar.xml`
1. Replace `[DATABASE]` with your MySQL database name
1. Replace `[USER]` with your MySQL username
1. Replace `[PASSWORD]` with your MySQL password
1. For more details on the available options see the [Traccar Configuration File](https://www.traccar.org/configuration-file/) documentation.

### Create the traefik configuration file

1. Create a `traefik.toml` file with your configuration inside the folder `traefik/`
1. A sample configuration file is provided on `traefik/traefik_sample.toml`. The standard login\username for the Web UI is admin\admin
1. For Let's Encrypt support see [Docker-compose with let's encrypt: TLS Challenge](https://doc.traefik.io/traefik/user-guides/docker-compose/acme-tls/)

### Restore the database

#### IF YOU HAVE A DATABASE BACKUP, ENSURE YOU DO THIS BEFORE STARTING TRACCAR-SERVER

1. In the machine and the respective folder where the backup is located, transfer the backup file to the VPS:  
`rsync -avzhe ssh ./backup.sql user@VPS_ip:~/backup.sql`
1. Connect to the VPS via SSH:  
`ssh user@VPS_ip`
1. Step into the clonned repository:  
`cd traccar-server`
1. Run the MySQL database:  
`docker-compose up -d db`
1. Check if everything is working via the commands:  
`docker-compose ps` or `docker ps`  
`docker-compose logs`
1. Transfer the last backup to the database:  
`docker exec -i db sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD" "$MYSQL_DATABASE"' < ~/backup.sql`

### Additional customization

Customize your volumes in the `docker-compose.yml` file according to the customization you want to have.

### Execute all the services

`docker-compose up -d` or `docker-compose up -d --build --force-recreate` to force recreation of image and container.  
Check if everything is working via the commands:  
`docker-compose ps` or `docker ps`  
Check the logs of each container via the command:  
`docker-compose logs container_name`

### Other useful repositories

- **[mysql-autobackup](https://github.com/RafaelMiquelino/mysql-autobackup):** Perform periodic backup of your traccar MySQL database.  
- **[rclone-autobackup](https://github.com/RafaelMiquelino/rclone-autobackup):** Sync your database backups to the cloud.  
- **[flask-text-reader](https://github.com/RafaelMiquelino/flask-text-reader):** Make your Traccar log file reachable from the browser  

To run all the services together, clone all the repositories to your machine, define the same compose project name for all of them at the `.env` file located on each repository and run each service through the `docker-compose up -d` command on each repository.
