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
#### Prepare `.env` file
Before editing the configuration file, you need to create it. The Git repository contains an example of a `.env` file named `.env.example`.
```shell
cp .env.example .env
```

Now, you can start editing the `.env` file.
#### Automatization service
- **`AUT_SCRIPTS_PATH`**: Path to the folder containing all scripts used in the automation service. This folder must include a subfolder named `install` with vCenter install files and a `configs` folder containing information for installing vCenter on a specific host.
- **`VM_VCENTER_IP`**: IP address of the specific vCenter that will host all VMs.
- **`VM_VCENTER_USER`**: Username for connecting to the vCenter.
- **`VM_VCENTER_PASSWORD`**: Password for connecting to the vCenter.

#### Backend service
- **`DB_CONNECTION_STRING`**: Connection string for the database in EF format.
- **`SALT`**: Random variable for hashing passwords and tokens.
- **`AUTOMATION_URL`**: Link to the automation service. No change needed if all services are in the same Docker Compose file.
- **`EMAIL_TEMPLATES`**: Folder containing all email templates.
- **`MAIL_SMTP_ADDRESS`**, **`MAIL_SMTP_PORT`**, **`MAIL_LOGIN`**, **`MAIL_PASSWORD`**: Credentials for connecting to the SMTP server for sending emails.
- **`AD_CONNECTION_STRING`**: Connection string to Active Directory for syncing classes, teachers, and students.

#### Frontend service
- **`FR_PUBLIC_CLIENT_API`**: Subpath configured in the proxy for making requests to the backend.
- **`FR_PUBLIC_BACKEND_API`**: Internal link to the backend service.

### Step 4
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