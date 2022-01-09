# MI SHAJIB'S LARAVEL DOCKER BOILERPLATE

### Technologies Used

- Docker: DevOPS Technology used for setting up virtual environments for running containerized applications
- MySQL: Database technology
- Nginx: Web-server application
- PHP Laravel: Server-side Web Framework
- PHPMYADMIN: Database Administration
- Bootstrap: Front-end CSS styling framework

# Docker for developer environment

### Install Docker

Visit https://docs.docker.com/get-docker for instructions.

### Install Docker Compose (linux only)

Visit https://docs.docker.com/compose/install for instructions.

### Switch to Linux containers (windows only)

Right-click on the Docker icon in Taskbar and choose "Switch to Linux containers..."

### Prepare repositories

Clone this repository first. And then follow next step.

### Prepare app folder

App requires permissions to write into `storage` and `bootstrap/cache` folders.

**Linux/macOS:**

```
sudo chmod -R ag+w <path-to-backend-folder>/bootstrap/cache
sudo chmod -R ag+w <path-to-backend-folder>/storage
```

**Windows:**  
Use File Explorer to change permissions for these folders.

### Configure `docker-compose`

In app folder, copy `.env.example` to `.env` and edit it accordingly:

* `COMPOSE_CONVERT_WINDOWS_PATHS=1`  
  Uncomment this line on Windows OS. That will tell Docker to interconvert unix/win paths.
* `LISTEN_IP`  
  IP address to listen incoming HTTP connections. If you already have `nginx` listening on `127.0.0.1:80` port then you
  can bind docker containers to listen on another IP (default `127.0.0.2`).  
  **Warning:** If already have `nginx` working on your host, make sure they are not bound to `0.0.0.0`, that will
  prevent `nginx` container from starting. Rebind your host `nginx` to `127.0.0.1` instead.  
  **macOS users:** By default macOS doesn't bind other 127.0.0.* to loop back adapter, so you have to do that
  yourself:  
  ```sudo ifconfig lo0 alias 127.0.0.2 up```

**macOS** / **Windows** only:
You have to allow docker engine to bind mount your app folder into containers:

1. Open Docker Agent preferences
2. Select **Resources** &rarr; **File Sharing** in the left menu.
3. Add the folder you cloned.

### Configure hosts

Docker containers configured to work on `laravel-docker.local`.  
You have to add these records to your `/etc/hosts` (linux/macos) or `%WIN_DIR%\System32\drivers\etc\hosts` (win).

```
127.0.0.2 laravel-docker.local
```

You'll need root/administrator privileges to edit this file.

### Build and run containers

Open terminal/console and run:

```
docker-compose build
```

Building could take some time, because Docker will pull all needed images from Docker Hub.

When it's done you're ready to run containers:

```
docker-compose up -d
```

First run could take some time, because application will install needed dependencies. WAIT for sometimes (like - 5 to 10
minutes) after executing `docker-compose up -d` for the first time.

To start/stop containers you can use `docker-compose stop` and `docker-compose start`.  
To remove containers you can use `docker-compose down`.  
To show containers status you can use `docker-compose ps`, it should show something like that:

```
   Name                  Command               State                  Ports               
------------------------------------------------------------------------------------------
lr-docker-backend           /entrypoint.sh php-fpm           Up      9000/tcp                           
lr-docker-mysql             docker-entrypoint.sh mysqld      Up      127.0.0.2:3337->3306/tcp, 3306/tcp
lr-docker-nginx             /docker-entrypoint.sh ngin ...   Up      127.0.0.1:32782->80/tcp            
lr-docker-phpmyadmin        /docker-entrypoint.sh ngin ...   Up      127.0.0.1:32782->80/tcp            
```

### Containers description

There are 4 containers:

* `lr-docker-nginx`  
  Listens incoming web requests (HTTP) and dispatches them to **app**.

* `lr-docker-mysql`  
  MySQL database for **app**. On first run it will be seeded with admin user account and dev hotel id/context.  
  Login/password: `admin@laravel-docker.local`/`123123123`.  
  You can access this DB from your host using `127.0.0.2:3337`.  
  Database content preserved between container restarts, but cleaned up upon `down` command.

* `lr-docker-phpmyadmin`  
  **PhpMyAdmin**. We can manage database by this. You can access this by - 127.0.0.2:8200.

* `lr-docker-backend`  
  **Backend**. Server using `php-fpm` (7.4). Exposes port `9000`.

You can restart container using `docker-compose restart <container-name>`, for
example: `docker-compose restart lr-docker-backend`.  
If you'll omit container name, then all containers will be restarted.

---

*Happy coding, do something wonderful ;)*
