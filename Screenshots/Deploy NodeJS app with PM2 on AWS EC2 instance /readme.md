Here is a clean, professional .md documentation file you can copy directly into GitHub (README.md).

I also removed unnecessary repetition and made the structure DevOps-friendly.

⸻

Deploy Node.js App with PM2 on AWS EC2

This guide demonstrates how to deploy a simple Node.js web application on an AWS EC2 instance and manage it using PM2, a production process manager for Node.js applications.

In this demo we will:
	•	Launch an EC2 instance
	•	Install Node.js
	•	Create a simple web server
	•	Serve an HTML page
	•	Run the app using PM2
	•	Deploy multiple Node.js apps on the same server

⸻

1. Launch EC2 Instance

Launch an EC2 instance with the following configuration:

AMI: Amazon Linux 2
Instance Type: t2.micro

Ensure the following ports are open in the Security Group:

Port 80   → HTTP Application
Port 9090 → Second Node App


⸻

2. Connect to EC2 Instance

Connect using SSH.

sudo su

You will be inside:

/home/ec2-user


⸻

3. Install NVM (Node Version Manager)

Install NVM.

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash

Load NVM into the shell session.

export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

Verify installation:

nvm --version


⸻

4. Install Node.js

Install the latest LTS version:

nvm install --lts

View available Node versions:

nvm ls-remote

Install Node.js version 16:

nvm install 16

Use Node.js version 16:

nvm use 16

Verify installation:

node -v
npm -v


⸻

5. Install Node.js using NodeSource Repository (Alternative Method)

Add official Node.js repository.

curl -sL https://rpm.nodesource.com/setup_14.x | sudo bash -

Install Node.js.

sudo yum install -y nodejs

Verify installation.

node -v
npm -v


⸻

6. Create Node.js Application

Create a server.js file.

vi server.js

Paste the following code:

const http = require('http');
const fs = require('fs');
const path = require('path');

const server = http.createServer((req, res) => {
  const filePath = path.join(__dirname, 'index.html');
  const stat = fs.statSync(filePath);

  res.writeHead(200, {
    'Content-Type': 'text/html',
    'Content-Length': stat.size,
  });

  const readStream = fs.createReadStream(filePath);
  readStream.pipe(res);
});

const PORT = 80;

server.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});

Save and exit:

ESC
:wq


⸻

7. Create HTML Page

Create index.html

vi index.html

Paste the following sample HTML:

<!DOCTYPE html>
<html>
<head>
<title>NodeJS App</title>
</head>

<body>

<h1>Welcome to NodeJS Application</h1>
<p>This application is running on AWS EC2.</p>

</body>
</html>

Save the file.

ESC
:wq


⸻

8. Install Dependencies

Install required packages.

npm install


⸻

9. Run the Node.js Application

Run the application.

node server.js

Open a browser and access:

http://<EC2-Public-IP>

You will see the web application.

⸻

10. Stop Application

Press:

CTRL + C

The application will stop.

Refreshing the browser will show:

This site can’t be reached

This happens because the app runs in interactive mode.

To run the application in background mode, we use PM2.

⸻

11. Install PM2

Install PM2 globally.

npm install -g pm2


⸻

12. Manage Application with PM2

Check running applications.

pm2 list

or

pm2 ls


⸻

13. Start Application with PM2

Start the Node.js application.

pm2 start server.js --name "kastro-app1"

Check running applications.

pm2 list

You will see:

status: online
id: 0

Access the application:

http://<EC2-Public-IP>


⸻

14. View Application Details

View application information.

pm2 show 0

or

pm2 show kastro-app1


⸻

15. Reload Application (After Code Changes)

pm2 reload 0

or

pm2 reload kastro-app1


⸻

16. PM2 Commands

Stop application:

pm2 stop 0

Restart application:

pm2 restart 0

Start application on system reboot:

pm2 startup


⸻

17. Deploy Multiple Apps on Same Server

Create a second application.

mkdir myapp2
cd myapp2

Create new server file.

vi server.js

Change the port number:

PORT = 9090

Create new HTML page.

vi index.html

Add different content.

Start second application.

pm2 start server.js --name "kastro-app2"

Check running applications.

pm2 ls

You will see:

kastro-app1 → Port 80
kastro-app2 → Port 9090


⸻

18. Monitor Applications

Monitor resource usage.

pm2 monit

Press:

ESC

to exit monitoring mode.

⸻

19. View Application Logs

View logs for the application.

pm2 logs kastro-app1


⸻

20. Access Second Application

Open the browser.

http://<EC2-Public-IP>:9090

Ensure port 9090 is open in the Security Group.

⸻

Architecture

User Browser
      │
      ▼
AWS EC2 Instance
      │
      ▼
Node.js Application
      │
      ▼
PM2 Process Manager


⸻

If you want, I can also give you a professional GitHub README version with:
	•	Architecture diagram
	•	DevOps resume project description
	•	PM2 vs Docker explanation
	•	Real production deployment architecture

That will make this project much stronger for DevOps interviews and GitHub portfolio.
