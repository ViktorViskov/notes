### Requirements
- PC
- Docker Compose

### Additional Documentation
[How to install docker](https://docs.docker.com/engine/install/)

### Step 0 
Copy unpack project files and change directory
```shell
tar -xf vmware-docker-compose.tgz
cd vmware-docker-compose
```
### Step 1

#### Configre and create db service
First, you need to change to db directory
```shell
cd db
```

You need to create `.env` file with password whitch one will be use to connect to db
```shell
cp .env.example .env
nano .env
```

After saving, you need to start docker compose to build and start database
```shell
docker compose up -d
```

You can successful back to project directory
```shell
cd ..
```

### Step 2
#### Prepare `.env` file
Before editing the configuration file, you need to create it. The Git repository contains an example of a `.env` file named `.env.example`. Need to make copy of this file and edit
```shell
cp .env.example .env
nano .env
```

### Step 3
#### Start Up the Project

Use the following commands to manage the project with Docker Compose.

#### Create and start
```shell
docker compose up -d # -d mean detached
```
#### Start
```shell
docker compose start -d
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
docker compose up -d --build # --build mean force build
```


[[work]]
[[vmware]]