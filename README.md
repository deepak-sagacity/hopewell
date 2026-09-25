# Hopewell - Charity Website Template

Hopewell is a modern, responsive charity and NGO website template built with HTML5, CSS3, and Bootstrap 5.

- **Repository**: [https://github.com/deepak-sagacity/hopewell](https://github.com/deepak-sagacity/hopewell)
- **Live Demo (EC2)**: [http://3.111.38.182](http://3.111.38.182)
- **Original Template Demo**: [ThemeWagon Demo](https://themewagon.github.io/hopewell/)

---

## Author & Maintainer

- **Maintainer**: **Deepak Sahni** ([@deepak-sagacity](https://github.com/deepak-sagacity))
- **Original Template & Design**: [K29Solutions](https://www.templatemonster.com/authors/k29solutions/) & [ThemeWagon](https://themewagon.com)

---

## Architecture & How It Works

Hopewell is a **static web application** (HTML, CSS, Bootstrap, JavaScript) with `index.html` as the main entry point. 

Because it is a static frontend website (without a Node.js backend like Express), we serve it using **`serve`** (a lightweight production HTTP server) daemonized under **PM2** on port 80. This guarantees:
- **24/7 Continuous Uptime**: PM2 keeps the server running in the background.
- **Auto-Recovery**: Automatically restarts the server if the instance reboots.
- **Clean URLs**: Port 80 allows accessing the site directly via IP/domain without adding a port number.

---

## Quick Presentation & Demo Guide

If you are explaining or demoing this deployment to clients, team members, or stakeholders, follow this flow:

### 1. The 30-Second Elevator Pitch
> *"This is **Hopewell**, an open-source NGO and charity website template. We deployed it on a cloud **AWS EC2 Linux instance** configured with a production static web server managed by **PM2** on Port 80, delivering fast, zero-downtime hosting."*

### 2. Live Demo Checklist
1. **Show the Live Site**: Open `http://3.111.38.182` in your browser. Highlight the responsiveness and clean UI.
2. **Show the Background Process**: In the EC2 terminal, run:
   ```bash
   pm2 status
   ```
   Show that the `hopewell` process is `online`, showing active memory usage and uptime.
3. **Show How Updates Work**: Explain that pushing changes to GitHub and running `git pull` instantly refreshes production.

---

## Step-by-Step EC2 Deployment Guide (Amazon Linux / PM2)

This is the exact setup running on the live EC2 instance (`3.111.38.182`).

### Step 1: Connect to Your EC2 Instance
1. In the **AWS Management Console**, navigate to **EC2** > **Instances**.
2. Select your instance (`hopewell-web-server`).
3. Click **Connect** (top right) and choose **EC2 Instance Connect** to open the browser terminal.

### Step 2: Install Git, Node.js, PM2, and `serve`
Inside the terminal, run:

```bash
# Update packages
sudo dnf update -y

# Install Git and Node.js
sudo dnf install -y git nodejs

# Install PM2 and serve globally
sudo npm install -g pm2 serve
```

### Step 3: Clone the GitHub Repository
```bash
git clone https://github.com/deepak-sagacity/hopewell.git
cd hopewell
```

### Step 4: Start the Web Server with PM2 on Port 80
```bash
# Start serving static files on standard HTTP port 80
pm2 start serve --name "hopewell" -- -s . -l 80

# Save PM2 process list and configure auto-restart on reboot
pm2 save
pm2 startup
```
*(If `pm2 startup` gives you a command to run, copy and execute it in your terminal).*

### Step 5: Configure the AWS Security Group
To allow visitors to view your website:
1. In the **AWS Console**, click your instance and select the **Security** tab.
2. Click the active **Security Group**.
3. Under **Inbound rules**, click **Edit inbound rules** and add:
   - **Type**: `HTTP`
   - **Port Range**: `80`
   - **Source**: `Anywhere-IPv4` (`0.0.0.0/0`)
4. Click **Save rules**.

### Step 6: Verify Deployment
Open your browser and visit:
```
http://<YOUR-EC2-PUBLIC-IP>
```
*(Example: `http://3.111.38.182`)*

### Step 7: How to Deploy Updates in the Future
Whenever you push changes from your local machine to GitHub:
```bash
cd ~/hopewell
git pull origin main
```
The site updates immediately!

---

## Alternative Deployment: AWS Elastic Beanstalk

You can also host Hopewell on AWS Elastic Beanstalk for auto-scaling and managed infrastructure.

### Method A: AWS Management Console (Zip Upload)
1. **Compress project files**:
   On Windows PowerShell inside the project directory:
   ```powershell
   Compress-Archive -Path .\* -DestinationPath ..\hopewell-deploy.zip
   ```
   *(Ensure `index.html` is at the root of the `.zip` archive).*
2. In the **AWS Elastic Beanstalk Console**, click **Create application**.
3. **Application Name**: `hopewell-charity`.
4. **Platform**: Choose **PHP** or **Node.js** (Both provide pre-configured Apache/Nginx web servers).
5. **Application code**: Select **Upload your code** and choose `hopewell-deploy.zip`.
6. Select **Single instance (free tier eligible)** and click **Submit**.
7. Once environment status turns **OK (Green)**, open the generated environment URL.

### Method B: AWS EB CLI
```bash
# Install EB CLI
pip install awsebcli

# Initialize
eb init -p php hopewell --region us-east-1

# Create environment and deploy
eb create hopewell-env --single

# Open website
eb open
```

---

## Troubleshooting & FAQ

### Q: Why did `pm2 start index.js` fail with "Script not found"?
**Answer**: `index.js` is used for Node.js backends (like Express or NestJS). Hopewell is a static frontend website with `index.html`. We use `serve -s . -l 80` with PM2 so that it acts as the HTTP web server.

### Q: Why Port 80?
**Answer**: Port 80 is the default port for HTTP web traffic. Using port 80 allows users to visit `http://3.111.38.182` without needing to append port numbers like `:3000` or `:8080`.

---

## License

- Design and Code &copy; [K29Solutions](https://www.templatemonster.com/authors/k29solutions/)
- Licensed under the [MIT License](LICENSE)
- Distributed by [ThemeWagon](https://themewagon.com)
