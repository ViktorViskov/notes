### Requirements
- PC
- Docker Compose

### Additional Documentation
[How to install docker](https://docs.docker.com/engine/install/)

### Step 0 (Optional)
Need to create deploy keys in git provider
[How to create deploy keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys)

### Step 1

#### Create db
First, you need to create a folder in the user directory
```shell
mkdir db
```

You need to create a docker-compose.yml file to start the database
```shell
nano docker-compose.yml
```

`docker-compose.yml`
```yaml
services:
  db:
    image: mysql
    container_name: mysql
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    ports:
      - 3306:3306
    environment:
      MYSQL_ROOT_PASSWORD: somepassword
      MYSQL_DATABASE: vmware-booking
```
In the file, you need to replace `somepassword` with a more secure password

After saving, you need to start the database
```shell
docker compose up -d
```

### Step 2
#### Clone the Project from GitHub/GitLab Repository
[link to project](https://gitlab.pcvdata.dk/team/vmware-docker-compose.git)

```shell
git clone https://gitlab.pcvdata.dk/team/vmware-docker-compose.git
```

After that, need to change directory to project folder
```shell
cd vmware-docker-compose
```

### Step 3
#### Prepare the Clone Script
Open the `clone_services.sh` file with a text editor and make the following changes:
```shell
nano clone_services.sh
```
1. Check and update every Git link to the correct Git link.

#### Order for links
1. Main project Git link
2. Automation Git link
3. Backend Git link
4. Frontend Git link
### Step 4
1. Потрібно положити файли встановлення `vcenter` в папку `folder`
2. Потрібно створити для кожного `vcenter` файл налаштувань та названи файл так як має бути в майбутньому ip адресса vcenter. Файли налаштувань знаходяться в папці `configs`

### Step 5
#### Prepare `.env` file
Before editing the configuration file, you need to create it. The Git repository contains an example of a `.env` file named `.env.example`. Need to make copy of this file and edit
```shell
cp .env.example .env
nano .env
```

### Step 6
#### Start Up the Project

Use the following commands to manage the project with Docker Compose.

#### Create
```shell
docker compose up -d
```
#### Stop
```shell
docker compose stop
```
#### Restart
```shell
docker compose restart
```
#### Delete
```shell
docker compose down
```
#### Rebuild and start
```shell
docker compose up -d --build
```


[[work]]
[[vmware]]