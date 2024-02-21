# Hosting Multiple Static Website on EC2 using Docker

Certainly! Let's delve into a more detailed explanation, focusing on deploying multiple static websites using Docker on an Amazon EC2 instance. This walkthrough will cover setting up your EC2 instance, installing Docker, pulling an Nginx image (a popular choice for serving static websites), creating Dockerfiles for your static sites, building and running your Docker containers, and finally, accessing your sites.

### Prerequisites

- An AWS account.
- An EC2 instance running (Amazon Linux, Ubuntu, etc.).
- SSH access to your EC2 instance.

### Step 1: Setting Up Your EC2 Instance

- **Launch an EC2 Instance**: Choose an Amazon Linux 2 AMI or Ubuntu AMI for simplicity. Ensure the instance type matches your expected traffic load.
- **Security Group Configuration**: Allow inbound traffic on port 80 (HTTP) and 22 (SSH). You might also want to open additional ports if you plan to serve websites on different ports.

### Step 2: Installing Docker on EC2

1. **SSH into Your EC2 Instance**:
   ```
   ssh -i /path/to/your-key.pem ec2-user@your-public-dns
   ```
2. **Install Docker**:
   - **For Amazon Linux 2**:
     ```
     sudo yum update -y
     sudo amazon-linux-extras install docker
     sudo service docker start
     sudo usermod -a -G docker ec2-user
     ```
   - **For Ubuntu**:
     ```
     sudo apt-get update
     sudo apt-get install docker.io
     sudo systemctl start docker
     sudo systemctl enable docker
     sudo usermod -aG docker ${USER}
     ```

### Step 3: Pulling the Nginx Image

- **Pull the Nginx Image** from Docker Hub:
  ```
  docker pull nginx:latest
  ```

### Step 4: Preparing Your Static Websites

1. **Create a Directory for Each Website**: For each static website, create a separate directory. Place your website files (HTML, CSS, JS, etc.) in their respective directories.

2. **Create a Dockerfile**: Inside each directory, create a `Dockerfile` that specifies how to build the container. Here's an example for a static site:

   ```Dockerfile
   # Use the Nginx image from Docker Hub
   FROM nginx:alpine

   # Remove the default Nginx configuration file
   RUN rm /etc/nginx/conf.d/default.conf

   # Copy a custom configuration file from the host to the container
   COPY nginx.conf /etc/nginx/conf.d

   # Copy the static site files to the Nginx server
   COPY . /usr/share/nginx/html
   ```

   - **Note**: You'll need an `nginx.conf` file in your website directory that configures Nginx to serve your site. Here’s a simple example of what `nginx.conf` might look like:

     ```nginx
     server {
         listen 80;
         location / {
             root   /usr/share/nginx/html;
             index  index.html index.htm;
         }
     }
     ```

### Step 5: Building and Running Your Docker Containers

1. **Build the Docker Image** for each site from its respective directory:
   ```
   docker build -t website1:latest .
   ```

2. **Run the Docker Container** for each site on a unique port:
   ```
   docker run -d -p 8080:80 website1:latest
   docker run -d -p 8081:80 website2:latest
   ```

### Step 6: Accessing Your Static Websites

- After the containers are running, you can access your websites using the EC2 instance's public IP address or DNS name followed by the port number:
  - `http://your-ec2-public-dns:8080`
  - `http://your-ec2-public-dns:8081`

### Additional Notes

- **Custom Domain**: If you want to use custom domain names for your static sites, you'll need to configure DNS settings (like A records) to point to your EC2 instance's IP address. You might also need to set up reverse proxy settings in Nginx to serve multiple domains from the same server.
- **HTTPS**: For secure connections, consider setting up SSL/TLS certificates using Let's Encrypt and adjusting your Nginx configuration to serve content over HTTPS.

This guide offers a comprehensive overview for deploying static sites with Docker on an EC2 instance. Each step, from installing Docker to accessing your deployed sites, is crucial for a successful deployment. Remember, managing production environments requires attention to security, scalability, and ongoing maintenance.
