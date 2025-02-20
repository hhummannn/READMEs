# Local development setup of ksu24

This is a README for setting up a development environment on a local machine of choice

## Pre-setup
### Windows

From an `ADMINISTRATIVE` PowerShell,
 
[Install chocolatey](https://chocolatey.org/install#individual)
```powershell
Set-ExecutionPolicy AllSigned
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```
[Install OpenSSH Client](https://docs.sunfounder.com/projects/picar-x/en/latest/appendix/install_openssh_powershell.html)
```powershell
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
```
* Install nodejs with `choco install nodejs.install`
---
* [Install Git for Windows](https://gitforwindows.org/)
* [Install latest Python](https://www.python.org/downloads/windows/)
* [Install Docker Desktop](https://docs.docker.com/desktop/setup/install/windows-install/)

### Linux

I take it You're a more advance user, given you are using an adequate OS! A person of culture indeed

Here's a quick recap for what you should have installed before proceeding

* `sudo apt install openssh-client openssh-server` [A "Because" to your "Why"](https://ubuntu.com/server/docs/openssh-server)
* `sudo apt update && sudo apt install nodejs` [A "Because" to your "Why](https://monovm.com/blog/install-npm-on-ubuntu/#Install-Node.js-and-npm-from-the-Ubuntu)
* `sudo apt install git-all` [Git says to do it so](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* `sudo apt update && sudo apt install python3-dev python3-pip python3-venv`
* `sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin` [As per](https://docs.docker.com/engine/install/ubuntu/)
* [To make sure you can run docker as a non-root user](https://docs.docker.com/engine/install/linux-postinstall/)
```shell
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
docker run hello-world
```

## Setup

Preferably done in Git Bash on Windows

### 1. Create and cd to a folder dedicated for ksu24 repositories

```shell
mkdir ksu24 ; cd ksu24
```
[mkdir](https://en.wikipedia.org/wiki/Mkdir)

[cd](https://en.wikipedia.org/wiki/Cd_(command))
### 2. [git clone] all required ksu24 repositories

This is done preferrably in your `$HOME` directory
  * `git clone https://github.com/ksu-24/ksu24.back` [ksu24.back]
  * `git clone https://github.com/ksu-24/ksu24.docker` [ksu24.docker]
  * `git clone https://github.com/ksu-24/ksu24.front-new` [ksu24.front-new]
### 3. Frontend setup:

  * `cd ksu24.front-new`
  * `npm i` (alias for [npm-install](https://docs.npmjs.com/cli/v8/commands/npm-install))
  * `cd ../` back to ksu24 folder
### 4. Backend setup:

  * `cd ksu24.back`
  * Setup a virtual environment
    * WINDOWS: 
      * `python -m venv venv`
      * `source venv/Scripts/activate`
      * `python -m pip install -r src/requirements.txt`
    * LINUX: 
      * `python3 -m venv venv`
      * `source venv/bin/activate`
      * `python -m pip install -r src/requirements.txt`
  * Create a local environment file by copying it from the template: `cp .env.local.template .env`
  * Edit `DB_HOST` value in the `.env` file to `db`
  * `cd ../` back to ksu24 folder
### 5. Docker setup:
  
  * `cd ksu24.docker`
  * Create a local (development) environment file by copying it from the existing template: `cp .env.local.template .env`
  * Edit `.env` file
    * set`COMPOSE_FILE` variable to `docker-compose.development.yml`
    * set `FRONT_DIR` variable to `../ksu24.front-new`
  * Edit `docker-compose.development.yml` file
    * Remove the entire `frontend` block
### 6. Bring up the project 

In the `ksu24.docker` folder
  * `docker compose up --build --detach` ([docker compose up])
  * Wait for the project to come up (This might take a while) (take a coffee or a break or a ~~shit~~)
  * Ensure the containers are running: 
    * `docker ps -a` ([docker ps])
    * You should see `ksu24.backend` and `ksu24.postgres` in `Running` state
### 7. Make the thing usable

  * Run database migrations
    * `docker compose exec backend python manage.py collectstatic --no-input` ([docker compose exec])
    * `docker compose exec backend python manage.py migrate`
    * `docker compose exec backend python manage.py populate_history --auto`
By now you should be able to access the admin server at http://127.0.0.1:8000/admin or http://localhost:8000/admin
  * Create an admin user for the backend access
    * `docker compose exec backend DJANGO_SUPERUSER_PASSWORD=admin python manage.py createsuperuser --no-input --username=admin --email=admin@example.com`
  * From `ksu24.front-new` folder run `npm run dev`
### 8. Check everything works.
  
  * Login to the administrator panel with `admin:admin` (`user:password`)

<!-- References -->

[ksu24.back]: https://github.com/ksu-24/ksu24.back
[ksu24.docker]: https://github.com/ksu-24/ksu24.docker
[ksu24.front-new]: https://github.com/ksu-24/ksu24.front-new
[git clone]: https://git-scm.com/docs/git-clone
[docker compose up]: https://docs.docker.com/reference/cli/docker/compose/up/
[docker compose exec]: https://docs.docker.com/reference/cli/docker/compose/exec
[docker ps]: https://docs.docker.com/reference/cli/docker/container/ls
