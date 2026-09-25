# Hopewell - Charity Website Template

Hopewell is a modern, responsive charity and NGO website template built with HTML5, CSS3, and Bootstrap 5.

- **Repository**: [https://github.com/deepak-sagacity/hopewell](https://github.com/deepak-sagacity/hopewell)
- **Demo**: [ThemeWagon Demo](https://themewagon.github.io/hopewell/)

---

## Author & Maintainer

- **Maintainer**: **Deepak Sahni** ([@deepak-sagacity](https://github.com/deepak-sagacity))
- **Original Template & Design**: [K29Solutions](https://www.templatemonster.com/authors/k29solutions/) & [ThemeWagon](https://themewagon.com)

---

## Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/deepak-sagacity/hopewell.git
cd hopewell
```

### 2. Local Preview
Open `index.html` directly in any web browser, or use VS Code's **Live Server** extension.

---

## Deployment Guide

### Option 1: Deploy on AWS EC2 (Direct / Manual Deployment)

This method deploys the website on an Ubuntu EC2 instance using **Nginx** without requiring CI/CD pipelines.

#### 1. Launch EC2 Instance
1. In the **AWS Management Console**, go to **EC2** > **Launch Instance**.
2. **Name**: `hopewell-web-server`.
3. **OS**: **Ubuntu Server 24.04 LTS** or **22.04 LTS**.
4. **Instance Type**: `t2.micro` or `t3.micro` (Free Tier eligible).
5. **Key Pair**: Select an existing key or create a new one (download the `.pem` file).
6. **Network & Security Groups**: Ensure the following inbound ports are open:
   - **Port 22** (SSH)
   - **Port 80** (HTTP)
   - **Port 443** (HTTPS)
7. Launch the instance and copy its **Public IPv4 Address**.

#### 2. Connect via SSH
On Windows PowerShell:
```powershell
# Set proper key permissions (Windows)
icacls "C:\path\to\your-key.pem" /inheritance:r
icacls "C:\path\to\your-key.pem" /grant:r "$($env:USERNAME):(R)"

# Connect to instance
ssh -i "C:\path\to\your-key.pem" ubuntu@<YOUR-EC2-PUBLIC-IP>
```

#### 3. Install Nginx and Git
Inside your EC2 terminal:
```bash
sudo apt update
sudo apt install nginx git -y
sudo systemctl enable nginx
sudo systemctl start nginx
```

#### 4. Clone Project onto Server
```bash
sudo git clone https://github.com/deepak-sagacity/hopewell.git /var/www/hopewell
sudo chown -R www-data:www-data /var/www/hopewell
sudo chmod -R 755 /var/www/hopewell
```

#### 5. Configure Nginx
Edit the default site configuration:
```bash
sudo nano /etc/nginx/sites-available/default
```

Update the `root` directive to point to `/var/www/hopewell`:
```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/hopewell;
    index index.html index.htm;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
Save and exit (`CTRL+O`, `Enter`, `CTRL+X`).

Test and reload Nginx:
```bash
sudo nginx -t
sudo systemctl reload nginx
```

#### 6. Access the Website
Open your browser and visit:
```
http://<YOUR-EC2-PUBLIC-IP>
```

#### 7. Future Updates
Whenever you push changes to GitHub, update the server with:
```bash
cd /var/www/hopewell
sudo git pull origin main
```

---

### Option 2: Deploy on AWS Elastic Beanstalk (PaaS Deployment)

AWS Elastic Beanstalk manages server provisioning, load balancing, and scaling automatically.

#### Method A: Using AWS Console (Web UI)

1. **Prepare the Project Archive**:
   Compress your project files into a `.zip` archive.
   > **Note**: Zip the contents directly (so `index.html` is at the root of the zip), not the parent folder.
   
   On Windows PowerShell:
   ```powershell
   Compress-Archive -Path .\* -DestinationPath ..\hopewell-deploy.zip
   ```

2. **Create Elastic Beanstalk Application**:
   - Go to the **AWS Elastic Beanstalk Console** > **Create application**.
   - **Application name**: `hopewell-charity`.
   - **Platform**: Select **PHP** or **Node.js** (Both provide a preconfigured web server like Apache/Nginx out-of-the-box that serves static `index.html` files).
   - **Application code**: Choose **Upload your code** and upload `hopewell-deploy.zip`.
   - **Preset**: Select **Single instance (free tier eligible)**.
   - Click **Next** through the setup wizard (use default service roles).
   - Review and click **Submit**.

3. **View Live Application**:
   - Elastic Beanstalk will provision the environment in 2–5 minutes.
   - Once the health status shows **OK (Green)**, click the provided **Environment URL** (e.g., `http://hopewell-charity.eba-xxxxxx.region.elasticbeanstalk.com`).

#### Method B: Using EB CLI (Command Line)

1. **Install EB CLI**:
   ```bash
   pip install awsebcli
   ```

2. **Initialize Elastic Beanstalk**:
   ```bash
   eb init -p php hopewell --region us-east-1
   ```

3. **Create Environment and Deploy**:
   ```bash
   eb create hopewell-env --single
   ```

4. **Open in Browser**:
   ```bash
   eb open
   ```

5. **Deploy Updates**:
   ```bash
   eb deploy
   ```

---

## License

- Design and Code &copy; [K29Solutions](https://www.templatemonster.com/authors/k29solutions/)
- Licensed under the [MIT License](LICENSE)
- Distributed by [ThemeWagon](https://themewagon.com)
