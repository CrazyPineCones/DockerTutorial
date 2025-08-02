# How to Set Up and Use Docker for Web Development Projects

## Table of Contents
1. [Overview](#overview)
2. [Step-by-Step Instructions](#step-by-step-instructions)
3. [Learning Outcomes](#learning-outcomes)
4. [Potential Issues](#potential-issues)
5. [What to do Next? (Making a to-do list project)](#what-to-do-next-making-a-to-do-list-project)
6. [Resources](#resources)

## Overview
![DockerVsVirtualization](https://github.com/user-attachments/assets/62b47f4f-7810-4ad2-847a-a0fb54796d00)

*Docker and Containerized Applications vs Virtualization Architectures*

Docker is a software development tool that allows developers to build, run, and deploy applications and web servers quickly through packages. These packages contain everything needed for these processes including libraries, system tools, code, and any other dependencies in containerized applications. Docker is similar to Virtualization in the sense that they both are used to deploy applications, but how they do so differs slightly.

With Virtualization, applications are run on virtual machines that each have their independent operating system and applications, all on a greater host computer and operating system. In other words, Virtualization containerizes on the operating system layer, allowing one computer to have multiple internal operating systems installed. Docker, however, containerizes on the application level, allowing applications to be run independently of the greater operating system. Docker has to be configured for the host operating system, but once it is, applications can be run on that computer, essentially virtually. 

Both have their advantages, but for Web Development projects, Docker is typically the way to go. It’s more portable and scalable than VMs because it removes the worry of any differences in development vs deployment architecture (developing on Linux but deploying on Windows for example). Docker containers are also much smaller than isolated VMs because those VMs require their operating systems. All of these factors contribute to a (typically) faster deployment cycle with easier sharing capabilities.

These instructions will detail how to make and run a simple web application using Docker.

## Step-by-Step Instructions
*A step-by-step guide to setting up Docker for a simple Node.js web application. This setup will create a lightweight environment to host a basic web server.*
<details>
    <summary>What is Node.js? </summary>
         Node.js is a JavaScript runtime environment that lets you execute JavaScript code outside of a web browser, typically on a server. This is beneficial as you can now use the popular JavaScript language, invented for the frontend, in your backend as well, improving readbility, sacalability, and simplifying your application logic. Node.js is designed for building fast, scalable network applications. 
</details>

### 1. Install Docker
Download Docker Desktop, go to the Docker [website](http://docs.docker.com/get-started/get-docker/) and download Docker Desktop for your OS (Windows, Mac, or Linux).
    
Install Docker, and follow the installation instructions for your system. Note that, installing Docker Desktop requires administrator privileges. However, once installed, it can be used without administrative access. Now open Docker Desktop to ensure it’s running.

If Docker is already installed on your operating system, and your administrator account is different to your user account, you must add the user to the docker-users group.
<details>
    <summary>Click Drop-Down Arrow for How To Add Users to a Group</summary>
    
    1. Open Command Prompt as Administrator
    Search “cmd”, right-click → Run as administrator

    2. Run this command : net localgroup docker-users your_username /add
    Replace your_username with your actual Windows username.
    
</details>

### 2. Navigate to Project Directory
Create a directory for your project and navigate into it using a command line interface (CLI) of your choice.
```bash
mkdir simple-node-app
cd simple-node-app
```

### 3. Initialize a Node.js Project
Run the following command to create a package.json file for your Node.js app.
```bash
npm init -y
```

![InitializeNodejs](https://github.com/user-attachments/assets/919547fe-3af0-4497-a63e-37eb24427c18)

*A `package.json` file is used by the `npm` CLI to identify your project and understand how to handle its dependencies. It enables `npm` to start your project, run scripts, etc.*

### 4. Create a Basic Web Server
Inside the project directory, create an index.js file with a simple Node.js (JavaScript) web server, you can do this using the code editor of your choice: 
```js
// index.js

// Include the Node.js http module
const http = require('http');

// Create a server object (req is the HTTP request, res is the response)
const server = http.createServer((req, res) => {
  res.statusCode = 200; // Return HTTP status code 200/OK
  // Show "Hello, Dockerized World!" on the webpage in plain text
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello, Dockerized World!\n');
});

// Run the server on port 3000
const port = 3000;
server.listen(port, () => {
  console.log(`Server running at http://localhost:${port}/`);
});
```

### 5. Create a `Dockerfile`
In the root of your project directory, create a file called `Dockerfile` that describes the environment and steps for running the Node.js app.
```docker
# Use an official Node.js runtime as the base image
FROM node:14

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json to install dependencies
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application files
COPY . .

# Expose the app port
EXPOSE 3000

# Run the application
CMD ["node", "index.js"]
```
### 6. Add a `docker-compose.yml` for Easier Setup
To simplify running the app and for live reloading, create a `docker-compose.yml` file: 

```yml
version: '3'
services:
  web:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app  # Mount current directory for live code updating
    environment:
      - NODE_ENV=development
```

### 7. Build and Run the Docker Container
In the terminal, run the following command to build and run the app:
```bash
docker compose up --build
```
This will build your Docker image based on the Dockerfile, start a container, and map your local port 3000 to the container’s port 3000.

![ServerRunning](https://github.com/user-attachments/assets/d9494cf6-cf2b-4279-a4c2-455a04c63608)


### 8. Access the Application
Open your browser and go to http://localhost:3000 to see the script served from your Dockerized Node.js application.

### 9. Stop the Application
To stop the container, press Ctrl + C in the terminal, or run:
```bash
docker-compose down
```

### All done!
![DockerWhale](https://github.com/user-attachments/assets/6dcf142b-441f-4040-94be-7b0931b86ea2)

With this setup, you have a basic Dockerized Node.js application. Using Docker Compose helps keep your development environment isolated and allows you to add other services easily. 

## Learning Outcomes
- Familiarity with fundamental Docker concepts, including images, containers, and Dockerfiles. Building and running a Docker image, you’ll see how Docker packages your application with its dependencies, ensuring consistency across - different environments.
- Creating and Managing Dockerfiles
- Running and Testing Dockerized Applications
- Configuring Isolated Development Environments
- Container Management, Scaling, and Portability Skills

## Potential Issues
If there is a mistake in the setup process, it likely won’t be revealed until trying to access the new application at http://localhost:3000/ in which the site can’t be reached.

The first sanity check should be to make sure the application is running. If building in a terminal, the terminal should display a “Server running” message as shown in Task 7. This can also be verified in the Containers tab of Docker Dashboard. If the desired container you want running has a triangle symbol under actions:

![DockerNotRunning](https://github.com/user-attachments/assets/55aca11a-9c62-41a1-a951-b7cbdd1ac56c)

The application is not running. Click it to run the build process outlined in Task 7, then verify if the application actions has changed to a square:

![DockerRunning](https://github.com/user-attachments/assets/30034543-c1cf-4c5a-b40e-1690e06d1311)

Try accessing the application again. If the site still can’t be reached, or the application couldn’t be built successfully, it means the issue is within the project itself.

Check the project structure to ensure every necessary file is in the correct location and the files are named correctly:

![ProjectStructure](https://github.com/user-attachments/assets/8f3ef6ec-da5f-4a5b-aeee-90fea0e6c9ee)

This picture shows the structure of the project as shown in the Visual Studio Code editor. Since the code and commands in each of the files all reference each other, if a filename is changed, the references to the file must also be changed. For instance, the auto-generated package.json file references `index.js` as its main file, and the Dockerfile lists `index.js` in the run command, but if this file was named `index.txt`, it could cause issues.

If the file structure is correct, it’s best to check the contents of each file to see if they match what’s outlined in Tasks 3-6. The code is provided for copying in Tasks 4-6, and the `package.json` file in Task 3 is auto-generated, but an image is provided to show the expected output. Ensure every file has what’s expected and continue with Task 7 to try building and accessing your newly created application.

## What to do Next? (Making a to-do list project)
The purpose of these instructions is to walk you through the basics of how Docker and its required elements (packages, containers, images) can be used to develop Web applications through a typical Hello World example project. This project only prints to a barebones site, but learning the process to get there is the end goal.

Another common software development project is a to-do list. Docker has a guide for how to make such an application with more in-depth development tasks like sharing the app, persisting a database, and multi-container apps. To access their guide for a to-do list manager application enter the command into your terminal:

```bash
docker run -dp 80:80 docker/getting-started
```

and open the browser to http://localhost

## Resources
https://docs.docker.com/get-started/get-docker/
