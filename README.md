# GlassFish 7 Docker with Oracle Wallet & Autodeployment

This repository provides a ready-to-run Docker setup for GlassFish 7. It's pre-configured to use Oracle JDBC drivers with a TNS Wallet and automatically deploy web applications placed in the `target` directory.

## Prerequisites

- Docker installed and running.

## Included Configuration

This repository already contains:

- A `Dockerfile` configured to install GlassFish, copy necessary drivers and wallet files, and set up a specific JDBC connection pool (`NimbleDBPool`) and resource (`jdbc/NimbleDS`) pointing to an Oracle database via the included wallet configuration.
- Required Oracle JDBC drivers in the `ojdbc10-full/` directory.
- Oracle TNS Wallet files configured for a specific database connection in the `wallet/` directory.
- An entrypoint script that copies applications from a staging volume for correct permissions.

**Note:** The database connection details (service name, username, password alias) are hardcoded in the `7.0.23/Dockerfile`. If you need to connect to a _different_ database, you will need to modify the `wallet/` files and the `Dockerfile` accordingly (see original `README.md` in git history or comments in the Dockerfile for details).

## Related Repositories

This GlassFish server setup is designed to host the backend for the Nimble Task Management system.

- **Backend (Source of the .war file):** [nicoceron/nimble-backend](https://github.com/nicoceron/nimble-backend)
- **Android Client (Consumes the backend):** [nicoceron/nimble-android](https://github.com/nicoceron/nimble-android)

## Quick Start

1.  **Clone the Repository:**

    ```bash
    git clone https://github.com/nicoceron/glassfish-server.git
    cd glassfish-server
    ```

2.  **Build the Docker Image:**

    ```bash
    docker build -t glassfish-oracle-app:latest -f 7.0.23/Dockerfile .
    ```

    _(This uses the configurations already present in the repository.)_

3.  **Run the Docker Container:**
    ```bash
    docker run -d \
      -p 8080:8080 \
      -p 4848:4848 \
      -v "$(pwd)/target":/staging \
      --name my-glassfish-app \
      glassfish-oracle-app:latest
    ```

- **Explanation of `-v "$(pwd)/target":/staging`:**
  - `-v` mounts a volume, linking a host directory to a container directory.
  - `$(pwd)` is replaced by the shell with the **full path to your current working directory** (where you run the `docker run` command, which should be the repository root).
  - So, `"$(pwd)/target"` becomes the full path to the `target` directory _on your machine_.
  - This host `target` directory is linked to the `/staging` directory _inside_ the container.
  - Using `$(pwd)` makes the command portable, as it works correctly no matter where you cloned the repository.

## Accessing the Application

- **Web Application:** Access your application via `http://localhost:8080/nimblev5-1.0-SNAPSHOT/`. (Context root derived from `target/nimblev5-1.0-SNAPSHOT.war`).
- **GlassFish Admin Console:** Accessible at `https://localhost:4848`. Default login: `admin`/`admin`.

## Managing the Container

- **View Logs:**
  ```bash
  docker logs my-glassfish-app
  ```
- **Stop Container:**
  ```bash
  docker stop my-glassfish-app
  ```
- **Start Container:**
  ```bash
  docker start my-glassfish-app
  ```
- **Restart Container:** (Useful after updating the `.war` file in the `target` directory)
  ```bash
  docker restart my-glassfish-app
  ```
- **Remove Container:** (Only after stopping)
  ```bash
  docker rm my-glassfish-app
  ```

## Security Warning

The database password is set via a password alias within the `Dockerfile`. For production use, consider more secure methods like Docker secrets.
