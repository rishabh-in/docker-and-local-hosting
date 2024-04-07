### Dockerizing Files Insight App Documentation

#### Introduction to Docker and Docker Compose

[Docker](https://www.docker.com/) is a platform that enables developers to package applications and their dependencies into standardized units called containers. Containers allow for consistent deployment across various environments, ensuring that applications run seamlessly regardless of the underlying infrastructure.

[Docker Compose](https://docs.docker.com/compose/) is a tool used to define and manage multi-container Docker applications. It simplifies the process of orchestrating containers, allowing developers to define their application's services, networks, and volumes in a single YAML file.

#### Normal Containerization by Dockerfile

1. **Installation and Setup**:
    - Install Docker on your Ubuntu system.
    - Create Dockerfiles for both the React and Node applications to define their build environments and configurations.

2. **React Dockerfile**:
    ```Dockerfile
    FROM node:16
    
    WORKDIR /usr/src/apps
    
    COPY package*.json ./
    
    RUN npm install
    
    COPY . .
    
    RUN npm run build
    
    EXPOSE 3000
    
    CMD [ "npm", "start" ]
    ```

3. **Node Dockerfile**:
    ```Dockerfile
    FROM node:18
    
    WORKDIR /usr/src/apps
    
    COPY package*.json ./
    
    RUN npm install
    
    COPY . .
    
    EXPOSE 4001
    
    CMD ["npm", "start"]
    ```

4. **Building Images**:
    - Build the images using Docker.
        ```bash
        sudo docker build -t <image_name> .
        ```
    - Check the built images using:
        ```bash
        sudo docker images
        ```

5. **Running Containers**:
    - Run the containers individually with port mapping:
        ```bash
        sudo docker run -d -p 4001:4001 <image_id|name>
        ```
    - Alternatively, run containers using host network configuration:
        ```bash
        sudo docker run -d --network host <image_id|name>
        ```

#### Docker Compose Setup

1. **Installation**:
    - Install Docker Compose.

2. **Compose Configuration** (`docker-compose.yml`):
    ```yaml
    version: '3.3'
    
    services:
      react-app:
        build: 
          context: ./client
          dockerfile: Dockerfile
        network_mode: "host"
        environment:
          - NODE_ENV=production
        restart: always
        depends_on:
          - node-app
      
      node-app:
        build: 
          context: ./server
          dockerfile: Dockerfile
        network_mode: "host"
        environment:
          - NODE_ENV=production
        restart: always
    ```

3. **Building and Running with Docker Compose**:
    - Build the images using Docker Compose:
        ```bash
        docker-compose build
        ```
    - Run the application using Docker Compose:
        ```bash
        docker-compose up -d
        ```
        (`-d` flag is used to run the application in detached mode)

#### Conclusion

In conclusion, Docker and Docker Compose provide powerful tools for containerizing and orchestrating applications. By utilizing Dockerfiles and Docker Compose configurations, developers can streamline the deployment process and ensure consistency across different environments.

---

### Running React App Locally with a Custom Domain

1. **Editing Hosts File**:
   - Modified the hosts file (`/etc/hosts` in Linux/macOS, `C:\Windows\System32\drivers\etc\hosts` in Windows) to map the custom domain name to `127.0.0.1`.

2. **Setting Up NGINX**:
   - Installed and configured NGINX as a reverse proxy to forward incoming requests for the custom domain to the local development server.

3. **NGINX Configuration**:
   - Created a server block configuration file for NGINX, typically located in `/etc/nginx/sites-available/`.
   - Defined a server block for the custom domain, specifying the domain name and the local server's IP and port.
   - Enabled the server block by creating a symbolic link to it in `/etc/nginx/sites-enabled/`.
   - Reloaded NGINX configuration using `sudo systemctl reload nginx`.

4. **Request Routing**:
   - Incoming requests for the custom domain are received by NGINX.
   - NGINX forwards these requests to the local development server running the React app.
   - The React app responds to the requests, and NGINX serves the responses back to the client.

5. **Caching**:
   - NGINX can cache responses from the React app, improving performance by serving cached content for subsequent requests.
   - Cached content is stored locally on the server, reducing the need to fetch resources from the React app for every request.

6. **Host Resolution**:
   - When a request is made to the custom domain, the operating system first checks its hosts file to resolve the domain to an IP address.
   - If the domain is found in the hosts file, it resolves to `127.0.0.1`, directing the request to the local machine.

7. **DNS Resolution**:
   - If the domain is not found in the hosts file, the operating system performs a DNS lookup to resolve the domain to an IP address.
   - The DNS lookup involves querying DNS servers specified in the system configuration or provided by the network.

By following these steps, we were able to run the React app locally using a custom domain name, facilitated by NGINX's reverse proxy configuration. This setup allows for easier testing and development with a more realistic environment, mimicking production configurations.
