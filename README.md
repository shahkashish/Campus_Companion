# Hosting Static Website on EC2 using Docker

Deploying multiple static websites on EC2 instances using Docker involves several steps, from setting up your EC2 instance to configuring Docker containers for each website. Below, I outline a comprehensive approach to achieve this, assuming you have basic familiarity with AWS EC2, Docker, and static website structures.

### Step 1: Set Up Your EC2 Instance

1. **Launch an EC2 Instance**:
   - Log in to your AWS Management Console.
   - Navigate to the EC2 dashboard and click "Launch Instance".
   - Choose an Amazon Machine Image (AMI), preferably Amazon Linux 2 or Ubuntu, as they come with Docker available.
   - Select an instance type, then click "Review and Launch".

2. **Configure Security Group**:
   - Add rules to allow inbound traffic on HTTP (port 80) and SSH (port 22).

3. **Launch the Instance**:
   - Review your settings and click "Launch".
   - Select a key pair or create a new one, then launch your instance.

### Step 2: Install Docker on EC2

1. **Connect to Your EC2 Instance**:
   - Use SSH to connect to your instance. Replace `your-public-dns` with your instance's public DNS:
     ```bash
     ssh -i /path/to/your-key.pem ec2-user@your-public-dns
     ```

2. **Install Docker**:
   - Update your package repository (specific commands may vary based on the OS).
   - Install Docker using the package manager:
     ```bash
     sudo yum update -y # For Amazon Linux 2
     sudo yum install docker -y
     ```
   - Start the Docker service:
     ```bash
     sudo service docker start
     ```
   - Add your user to the Docker group to run Docker commands without sudo (optional):
     ```bash
     sudo usermod -a -G docker $USER
     ```

### Step 3: Deploy Static Websites with Docker

1. **Create Dockerfile for Each Website**:
   - For each static website, create a directory with its content and a Dockerfile.
   - Example `Dockerfile` for a static website:
     ```Dockerfile
     FROM nginx:alpine
     COPY . /usr/share/nginx/html
     ```
   - This uses the `nginx:alpine` image and copies the static site content into the Nginx web server directory.

2. **Build Docker Images**:
   - Navigate to each website directory and build the Docker image:
     ```bash
     docker build -t website1:latest .
     ```
   - Repeat for each website, changing the tag accordingly (e.g., `website2:latest`).

3. **Run Containers**:
   - Run each Docker container on a different port:
     ```bash
     docker run -d -p 8080:80 website1:latest
     docker run -d -p 8081:80 website2:latest
     ```
   - These commands run the containers in detached mode, mapping port 80 inside the container to ports 8080 and 8081 on the host.

### Step 4: Access Your Websites

- Now, you can access your static websites using the EC2 instance's public IP or DNS:
  - Website 1: `http://your-ec2-public-dns:8080`
  - Website 2: `http://your-ec2-public-dns:8081`

### Screenshots & Documentation

- For the purposes of this guide, screenshots cannot be directly provided. However, to document your process for personal or team use:
  1. **Take Screenshots**: As you complete each major step (e.g., setting up the EC2 instance, installing Docker, running containers), take screenshots.
  2. **Document Steps**: Create a document in your preferred format (e.g., Markdown, Google Docs) and describe each step, embedding or linking to the corresponding screenshots.
  3. **Share Your Documentation**: Ensure your documentation is accessible to your team or stored securely for personal reference.

This approach provides a scalable and flexible way to host multiple static websites using Docker on AWS EC2, with each site isolated in its container and accessible via different ports.
