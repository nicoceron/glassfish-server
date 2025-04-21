# Custom GlassFish Docker Image with Oracle Wallet & Autodeployment

This project provides a Docker setup for running GlassFish 7, pre-configured with:

- Oracle JDBC drivers.
- Connection to an Oracle Database using a TNS Wallet.
- Automatic deployment of web applications (`.war`) via a staging directory.

## Prerequisites

- Docker installed and running.
- An Oracle Database accessible via TNS Wallet authentication.
- Your web application packaged as a `.war` file.

## Setup

1.  **Clone the Repository:**

    ```bash
    git clone <your-repository-url>
    cd <repository-directory>
    ```

2.  **Oracle JDBC Drivers:**

    - Download the required Oracle JDBC drivers. This setup specifically copies `ojdbc10.jar`, `oraclepki.jar`, `osdt_core.jar`, `osdt_cert.jar`, and `ucp.jar` during the build.
    - Place these downloaded JAR files into the `ojdbc10-full/` directory.

3.  **Oracle Wallet:**

    - Place your Oracle TNS Wallet files (e.g., `cwallet.sso`, `ewallet.p12`, `sqlnet.ora`, `tnsnames.ora`, `keystore.jks`, `truststore.jks`) into the `wallet/` directory.
    - **Crucially:** Edit the `wallet/tnsnames.ora` file and ensure it contains the connection details for your target database, using a service name like `yourdb_high` (or similar). You will need to update the Dockerfile to use your chosen service name.

4.  **Configure Database Connection:**

    - **Edit `7.0.23/Dockerfile`:**
      - Locate the `RUN` command section that configures the JDBC connection pool (search for `create-jdbc-connection-pool`).
      - Modify the `--property` options, **especially**:
        - `URL`: Change `@nimbledb_high` to match the TNS service name you configured in `tnsnames.ora` (e.g., `URL='jdbc:oracle:thin:@yourdb_high?TNS_ADMIN=/opt/oracle/wallet'`).
        - `User`: Change `'ADMIN'` to your actual database username.
        - `Password`: This currently uses a password alias (`nimbledb_password_alias`). The line `echo "AS_ADMIN_ALIASPASSWORD=..."` sets the password for this alias. **Change `Hastael125687` to your actual database password.** Consider using Docker secrets or environment variables for better security in production environments.
        - `oracle.net.wallet_location`: This should remain `/opt/oracle/wallet` as the wallet is copied there inside the container.
      - Rename the connection pool (`NimbleDBPool`) and JDBC resource (`jdbc/NimbleDS`) if desired, updating the corresponding lines in the `Dockerfile`.

5.  **Prepare Application:**
    - Place your web application `.war` file (e.g., `myapp.war`) into the `target/` directory.

## Build the Docker Image

Navigate to the project root directory in your terminal and run the build command:

```bash
docker build -t your-glassfish-image:latest -f 7.0.23/Dockerfile .
```

- Replace `your-glassfish-image:latest` with a suitable name and tag for your image.
- `-f 7.0.23/Dockerfile`: Specifies the location of the Dockerfile.
- `.`: Sets the build context to the current directory, allowing Docker to access `7.0.23/`, `ojdbc10-full/`, and `wallet/`.

## Run the Docker Container

Once the image is built, run the container:

```bash
# Ensure the 'target' directory exists in your current path
mkdir -p target
# (Copy your WAR file into ./target if you haven't already)

docker run -d \
  -p 8080:8080 \
  -p 4848:4848 \
  -v "$(pwd)/target":/staging \
  --name my-glassfish-app \
  your-glassfish-image:latest
```

- `-d`: Runs the container in detached mode.
- `-p 8080:8080`: Maps host port 8080 to container port 8080 (Application HTTP).
- `-p 4848:4848`: Maps host port 4848 to container port 4848 (GlassFish Admin Console HTTPS).
- `-v "$(pwd)/target":/staging`: Mounts your local `target` directory to `/staging` inside the container. The entrypoint script copies from `/staging` to the internal `autodeploy` directory on startup.
- `--name my-glassfish-app`: Assigns a name to the running container.
- `your-glassfish-image:latest`: Specifies the image you just built.

## Accessing the Application

- **Web Application:** Access your application via `http://localhost:8080/<your-app-context-root>/`. The context root is usually the name of your `.war` file without the extension (e.g., `http://localhost:8080/myapp/`).
- **GlassFish Admin Console:** Accessible at `https://localhost:4848`. The default login is `admin`/`admin` (unless changed in the Dockerfile). You might need to accept a self-signed certificate warning.

## Development Notes

- **Autodeployment:** To deploy a new version of your application, simply replace the `.war` file in the host `target/` directory and restart the container (`docker restart my-glassfish-app`). The entrypoint script will copy the new WAR on restart.
- **Database Password Security:** The current method stores the database password directly in the Dockerfile. For production or sensitive environments, use more secure methods like Docker secrets or build-time arguments with environment variables.

## Troubleshooting

- **Check Logs:**
  ```bash
  docker logs my-glassfish-app
  ```
- **Port Conflicts:** Stop conflicting containers (`docker stop <id>`) or processes using ports 8080/4848.
- **Deployment Issues:** Check logs for errors related to your application (`.war`) or the JDBC pool configuration in the Dockerfile.
- **Stop/Remove Container:**
  ```bash
  docker stop my-glassfish-app
  docker rm my-glassfish-app
  ```
