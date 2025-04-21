# Running Custom GlassFish with Oracle Wallet Autodeployment

This document explains how to build and run the custom GlassFish Docker image configured to:

- Use Oracle JDBC drivers.
- Connect to an Oracle Database using a provided TNS Wallet.
- Automatically deploy a web application (`.war`) placed in the `target` directory.

## Prerequisites

- Docker installed and running.

## Directory Structure

Ensure the following directory structure exists in your project root (`/Users/ceron/Developer/glassfish server`):

```
.
├── 7.0.23/
│   ├── Dockerfile             # The modified Dockerfile
│   └── docker-entrypoint.sh   # The modified entrypoint script
│   └── ... (other files from original GlassFish image source)
├── ojdbc10-full/              # Contains required Oracle JDBC JARs
│   ├── ojdbc10.jar
│   ├── oraclepki.jar
│   ├── osdt_core.jar
│   ├── osdt_cert.jar
│   └── ucp.jar
│   └── ... (other Oracle drivers/files, only listed are copied)
├── wallet/                    # Contains Oracle TNS Wallet files
│   ├── cwallet.sso
│   ├── ewallet.p12
│   ├── sqlnet.ora
│   ├── tnsnames.ora
│   └── ... (other necessary wallet files like keystore.jks, truststore.jks)
├── target/                    # Place your web application .war file here
│   └── nimblev5-1.0-SNAPSHOT.war  # Example WAR file name
└── README.md                  # This file
```

**Important:**

- Populate the `wallet/` directory with your actual Oracle TNS Wallet files. The configuration expects `tnsnames.ora` to define the service `nimbledb_high`.
- Ensure the specified Oracle JDBC JAR files (`ojdbc10.jar`, `oraclepki.jar`, `osdt_core.jar`, `osdt_cert.jar`, `ucp.jar`) are present in the `ojdbc10-full/` directory.
- Place the `.war` file you want to deploy into the `target/` directory. The example uses `nimblev5-1.0-SNAPSHOT.war`.

## Build the Docker Image

Navigate to the project root directory (`/Users/ceron/Developer/glassfish server`) in your terminal and run the build command:

```bash
docker build -t glassfish-custom:7.0.23-oracle -f 7.0.23/Dockerfile .
```

- `-t glassfish-custom:7.0.23-oracle`: Assigns a name and tag to the image.
- `-f 7.0.23/Dockerfile`: Specifies the location of the Dockerfile.
- `.`: Sets the build context to the current directory, allowing Docker to access `7.0.23/`, `ojdbc10-full/`, and `wallet/`.

## Run the Docker Container

Once the image is built, run the container:

```bash
docker run -d -p 8080:8080 -p 4848:4848 -v "$(pwd)/target":/staging --name nimble-glassfish glassfish-custom:7.0.23-oracle
```

- `-d`: Runs the container in detached mode (in the background).
- `-p 8080:8080`: Maps host port 8080 to container port 8080 (Application HTTP).
- `-p 4848:4848`: Maps host port 4848 to container port 4848 (GlassFish Admin Console HTTPS).
- `-v "$(pwd)/target":/staging`: **Important:** Mounts your local `target` directory to a `/staging` directory inside the container. The container's entrypoint script will copy the contents from `/staging` to the actual `autodeploy` directory, ensuring correct permissions and preventing redeployment loops.
- `--name nimble-glassfish`: Assigns a convenient name to the running container.
- `glassfish-custom:7.0.23-oracle`: Specifies the image to run.

## Accessing the Application

- **Web Application:** If your `.war` file was `nimblev5-1.0-SNAPSHOT.war`, it should be accessible at `http://localhost:8080/nimblev5-1.0-SNAPSHOT/`. Adjust the context root based on your WAR file name.
- **GlassFish Admin Console:** Accessible at `https://localhost:4848`. The default login is `admin`/`admin` (unless changed during the build or via environment variables not covered here). You might need to accept a self-signed certificate warning in your browser.

## Troubleshooting

- **Check Logs:** If the application doesn't deploy or you encounter issues, check the container logs:
  ```bash
  docker logs nimble-glassfish
  ```
- **Port Conflicts:** If `docker run` fails with a "port is already allocated" error, another process (potentially an older container) is using port 8080 or 4848. Stop the conflicting process/container. You can list running containers with `docker ps` and stopped ones with `docker ps -a`. Stop a container with `docker stop <container_id_or_name>`.
- **Deployment Issues:** Check the logs for deployment errors related to your application or the database connection pool (`NimbleDBPool`).
- **Stop the Container:**
  ```bash
  docker stop nimble-glassfish
  ```
- **Remove the Container:** (Optional, after stopping)
  ```bash
  docker rm nimble-glassfish
  ```
