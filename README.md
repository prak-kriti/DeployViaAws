# Hosting a Static Website on AWS EC2 with Docker

## Prerequisites
- AWS account
- SSH key pair for your EC2 instance
- Basic knowledge of Docker and AWS

## Steps

### 1. Create a Security Group
1. Sign in to the AWS Management Console.
2. Navigate to the EC2 Dashboard.
3. Click on "Security Groups" under "Network & Security".
4. Click "Create Security Group".
5. Name your security group and add the following inbound rules:
   - HTTP (port 80)
   - SSH (port 22)
6. Click "Create".

### 2. Launch an EC2 Instance
1. Go to the EC2 Dashboard.
2. Click "Launch Instance".
3. Choose an Amazon Machine Image (AMI), such as Ubuntu.
4. Select an instance type (e.g., t2.micro for free tier).
5. Configure instance details and select the security group you created.
6. Add storage if needed.
7. Review and launch the instance.
8. Download the key pair (.pem file) for SSH access.

### 3. Connect to Your EC2 Instance
1. Open your terminal.
2. Connect to your instance using SSH:
   ```sh
   ssh -i /path/to/your-key-pair.pem ubuntu@your-ec2-public-dn
### 4.Create an EC2 Instance using below provision shell script
#!/bin/bash
# Update package lists and upgrade all packages
sudo apt-get update -y
sudo apt-get upgrade -y

# Install necessary packages
sudo apt-get install wget curl git vim ca-certificates gnupg lsb-release -y

# Create the directory for Docker's keyrings
sudo mkdir -p /etc/apt/keyrings

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Set up the Docker repository
sudo echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update the package index
sudo apt-get update -y

# Verify that Docker is available in the apt-cache
sudo apt-cache policy docker-ce

# Install Docker and related components
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

# Add the current user to the Docker group
sudo usermod -aG docker <USERNAME>

# Start Docker and enable it to start on boot
sudo systemctl start docker
sudo systemctl enable docker

# Download Docker Compose
sudo wget https://github.com/docker/compose/releases/download/v2.14.0/docker-compose-linux-x86_64 -O /usr/local/bin/docker-compose

# Apply executable permissions to the Docker Compose binary
sudo chmod +x /usr/local/bin/docker-compose

# Script completed successfully
echo "Docker and Docker Compose have been installed successfully."
exit 0

### 5. Create Dockerfile 
Dockerfile

```sh
FROM nginx:latest
RUN apt-get update && apt-get upgrade -y
RUN apt-get install wget unzip -y
WORKDIR /usr/share/nginx/html
COPY default.conf /etc/nginx/sites-enabled/
ADD https://bootstrapmade.com/bootslander-free-bootstrap-landing-page-template/
RUN unzip Ninestars.zip
RUN mv Ninestars/* .
RUN rm -rf Ninestars Ninestars.zip
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 6. nginx configuration
```sh
server {
        listen 80 default_server;
        root /usr/share/nginx/html;
        index index.html;
        server_name mysite.com;
}
```

 ### 7. Build Docker Image
```sh
 docker build -t testimg:v1 .
```
 
 ### 8.Run the Container
 ```sh
 docker run -it --rm -d -p 80:80 testimg:v1
```
 
