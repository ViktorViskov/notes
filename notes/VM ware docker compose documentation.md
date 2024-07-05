#### Project structure
![[Pasted image 20240625103230.png]]

#### Example of docker compose file
```yml
services:
  nginx:
    container_name: nginx-dev
    image: nginx
    ports:
      - "443:443"
    volumes:
      - ./nginx:/etc/nginx/templates
      - ./ssl:/ssl
  automatization:
    container_name: automat-dev
    restart: unless-stopped
    build: ./automatization
    # ports:
    #   - "6004:5000" # Optional for debugging
    environment:
      - VERSION=8.3.0
      - SCRIPTS_PATH=/scripts
      - VM_VCENTER_IP=${VM_VCENTER_IP}
      - VM_VCENTER_USER=${VM_VCENTER_USER}
      - VM_VCENTER_PASSWORD=${VM_VCENTER_PASSWORD}
    volumes:
      - ./automatization/scripts:/scripts
  backend:
    container_name: backend-dev
    restart: unless-stopped
    build: ./backend
    # ports:
    #   - "6002:5000" # Optional for debugging
    environment:
      - VERSION=8.4.1
      - DB_CONNECTION_STRING=server=${DB_ADDRESS};database=${DB_NAME};port=${DB_PORT};user=${DB_USER};password=${DB_PASSWORD};
      - SALT=jakX9CaStSKCFT0XQoOM
      - AUTOMATIZATION_URL=http://automatization:5000
      - EMAIL_TEMPLATES=/app/EmailTemplates/
      - MAIL_SMTP_ADDRESS=${MAIL_SMTP_ADDRESS}
      - MAIL_SMTP_PORT=${MAIL_SMTP_PORT}
      - MAIL_LOGIN=${MAIL_USER}
      - MAIL_PASSWORD=${MAIL_PASSWORD}
      - UNIT_TESTING=
      - AUTO_MIGRATIONS=true
      - USE_SWAGGER=true
      - ALLOW_CORS=true
      - AD_CONNECTION_STRING=${AD_ADDRESS}__${AD_DOMAIN}__${AD_USER}__${AD_PASSWORD}
      - ASPNETCORE_ENVIRONMENT=Development # Temporary. Remove in production
  frontend:
    container_name: frontend-dev
    build: ./frontend
    # ports:
    #   - 6003:6003 # Optional for debugging
    environment:
      - PORT=6003
      - PUBLIC_VERSION=8.2.0
      - PUBLIC_CLIENT_API=/api
      - PUBLIC_BACKEND_API=http://backend:5000
  
  db-backup:
    build: ./backup
    container_name: backup-dev
    restart: unless-stopped
    volumes:
      - ./backup/backup-files:/backup
      - ./backup/backup_script.sh:/etc/periodic/daily/backup_script.sh
    environment:
      - DB_CONNECTION_STRING=server=${DB_ADDRESS};database=${DB_NAME};port=${DB_PORT};user=${DB_USER};password=${DB_PASSWORD};
```

## Project structure
The project consists of various docker containers. Each container performs a specific role and generally represents a service.

## Services
This documentation provides detailed information on the services defined in the `docker-compose.yml` file. Each service is described with its purpose, configuration details, and environment variables.

### nginx

**Description:** The `nginx` service acts as a proxy server to route requests between the frontend and backend services. It ensures that incoming requests are directed to the appropriate service based on the request path or other criteria. This service is essential for managing traffic and providing secure connections via HTTPS.

**Configuration:**

- **Container Name:** `nginx-dev`
- **Image:** `nginx`
- **Ports:**
    - `443:443` - Exposes port 443 for HTTPS connections to the host.
- **Volumes:**
    - `./nginx:/etc/nginx/templates` - Mounts the local `nginx` directory to the container, allowing custom nginx configuration templates.
    - `./ssl:/ssl` - Mounts the local `ssl` directory to the container, providing SSL certificates for HTTPS.

**Key Features:**

- Acts as a reverse proxy.
- Secure communication using SSL/TLS.
- Customizable nginx configurations through mounted templates.

---

### automatization

**Description:** The `automatization` service is responsible for executing shell and PowerShell scripts directly on vCenter or ESXi hosts. It uses the ASP.NET 8.0 framework to provide a web interface and API for managing automation tasks. This service facilitates the automation of various administrative tasks on virtual infrastructure.

**Configuration:**

- **Container Name:** `automat-dev`
- **Restart Policy:** `unless-stopped` - Ensures the service restarts unless explicitly stopped.
- **Build Context:** `./automatization`
- **Environment Variables:**
    - `VERSION=8.3.0` - Specifies the version of the automatization service.
    - `SCRIPTS_PATH=/scripts` - Path to the directory containing automation scripts.
    - `VM_VCENTER_IP=${VM_VCENTER_IP}` - IP address of the vCenter server.
    - `VM_VCENTER_USER=${VM_VCENTER_USER}` - Username for vCenter authentication.
    - `VM_VCENTER_PASSWORD=${VM_VCENTER_PASSWORD}` - Password for vCenter authentication.
- **Volumes:**
    - `./automatization/scripts:/scripts` - Mounts the local `scripts` directory to the container, providing the scripts to be executed.

**Key Features:**

- Executes shell and PowerShell scripts.
- Integrates with vCenter and ESXi hosts.
- Provides a web interface for automation management.

---

### backend

**Description:** The `backend` service handles the core functionality of user management, authentication, authorization, reservation creation and deletion, and database interactions. Built using the ASP.NET 8.0 framework, this service is crucial for maintaining the application's business logic and data integrity. It interacts with a MySQL database for data storage and retrieval.

**Configuration:**

- **Container Name:** `backend-dev`
- **Restart Policy:** `unless-stopped`
- **Build Context:** `./backend`
- **Environment Variables:**
    - `VERSION=8.4.1` - Specifies the version of the backend service.
    - `DB_CONNECTION_STRING=server=${DB_ADDRESS};database=${DB_NAME};port=${DB_PORT};user=${DB_USER};password=${DB_PASSWORD};` - Connection string for the MySQL database.
    - `SALT=jakX9CaStSKCFT0XQoOM` - Salt value for hashing.
    - `AUTOMATIZATION_URL=http://automatization:5000` - URL for the automatization service.
    - `EMAIL_TEMPLATES=/app/EmailTemplates/` - Path to email templates used by the service.
    - `MAIL_SMTP_ADDRESS=${MAIL_SMTP_ADDRESS}` - SMTP server address for sending emails.
    - `MAIL_SMTP_PORT=${MAIL_SMTP_PORT}` - SMTP server port.
    - `MAIL_LOGIN=${MAIL_USER}` - SMTP server login username.
    - `MAIL_PASSWORD=${MAIL_PASSWORD}` - SMTP server login password.
    - `UNIT_TESTING=` - Flag for enabling unit testing.
    - `AUTO_MIGRATIONS=true` - Enables automatic database migrations.
    - `USE_SWAGGER=true` - Enables Swagger for API documentation.
    - `ALLOW_CORS=true` - Allows Cross-Origin Resource Sharing (CORS).
    - `AD_CONNECTION_STRING=${AD_ADDRESS}__${AD_DOMAIN}__${AD_USER}__${AD_PASSWORD}` - Connection string for Active Directory.
    - `ASPNETCORE_ENVIRONMENT=Development` - ASP.NET Core environment setting.

**Key Features:**

- Manages user synchronization through Active Directory.
- Handles authentication and authorization.
- Manages reservations and database interactions.
- Provides API documentation via Swagger.
- Supports automatic database migrations.

---

### frontend

**Description:** The `frontend` service handles the rendering of web pages using Server-Side Rendering (SSR) technology with SvelteKit. This service is responsible for delivering the user interface of the application, providing a responsive and dynamic user experience.

**Configuration:**

- **Container Name:** `frontend-dev`
- **Build Context:** `./frontend`
- **Environment Variables:**
    - `PORT=6003` - Port on which the frontend service runs.
    - `PUBLIC_VERSION=8.2.0` - Version of the frontend application.
    - `PUBLIC_CLIENT_API=/api` - Path to the client API.
    - `PUBLIC_BACKEND_API=http://backend:5000` - URL for the backend API.

**Key Features:**

- Renders web pages using SSR with SvelteKit.
- Connects to backend APIs for data.
- Provides a responsive user interface.

---

### db-backup

**Description:** The `db-backup` service is responsible for creating daily database dumps to ensure data is regularly backed up and can be restored in case of data loss. This service runs on an Alpine 3.19 container and executes a backup script at a scheduled interval.

**Configuration:**

- **Container Name:** `backup-dev`
- **Restart Policy:** `unless-stopped`
- **Build Context:** `./backup`
- **Volumes:**
    - `./backup/backup-files:/backup` - Mounts the local `backup-files` directory to the container, storing backup files.
    - `./backup/backup_script.sh:/etc/periodic/daily/backup_script.sh` - Mounts the local `backup_script.sh` file to the container, scheduling the backup script to run daily.
- **Environment Variables:**
    - `DB_CONNECTION_STRING=server=${DB_ADDRESS};database=${DB_NAME};port=${DB_PORT};user=${DB_USER};password=${DB_PASSWORD};` - Connection string for the MySQL database.

**Key Features:**

- Performs daily database backups.
- Stores backups in a specified directory.
- Ensures data safety and recovery.

[[work]]