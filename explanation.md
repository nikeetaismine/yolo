This document explains how the YOLO React/Node.js/MongoDB application is provisioned, deployed, and run using Vagrant, Ansible, Docker, and Docker Compose.

📁 Project Structure (Overview)
----------------------------

yolo/
├── backend/                 # Node.js backend code
│   └── Dockerfile
├── frontend/                # React frontend code
│   └── Dockerfile
├── roles/
│   └── yolo-deployment/    # Single role for deploying the full app
│       ├── tasks/
│       │   └── main.yml
│       ├── files/
│       │   └── docker-compose.yml
│       └── vars/
├── playbook.yaml           # Ansible playbook entry point
├── Vagrantfile             # VM provisioning
├── ansible.cfg             # Ansible configuration
├── hosts                   # Ansible inventory file
└── .dockerignore           # Ignore files when building Docker images

🧰 Tools Used
-----------
Tool	          Purpose
Vagrant	        Spins up a virtual machine (Ubuntu) for deployment
Ansible	        Automates installation of Docker, Docker Compose, and app setup
Docker	        Runs the frontend, backend, and MongoDB in containers
Docker Compose	Defines and manages multi-container setup

⚙️ How It All Works
--------------------

1. Start the App

  Run this from the root of the project:

    vagrant up --provision

  This does everything for you:

  - Starts a new Ubuntu VM.

  - Installs Docker, Docker Compose, and Ansible.

  - Runs Ansible to deploy the app.

2. Provisioning with Vagrant

  - The Vagrantfile sets up a VM with Ubuntu 22.04 (ubuntu/jammy64).

  - It installs Docker and the Docker Compose plugin.

  - It installs Ansible and required Python dependencies.

3. Deploying with Ansible

  The Ansible playbook.yaml applies the yolo-deployment role.

  This role:

    - Copies the docker-compose.yml file to the VM.

    - Runs docker compose up -d to build and start the services.

4. Running the App

  After provisioning:

    - Your React frontend is available at http://localhost:3000

    - The Node.js backend is available at http://localhost:5000

    - MongoDB runs in a private container and connects to the backend internally

🧱 Containers Launched
-----------------------

Service	            Port	  Description
---------------------------------------------------------
brian-yolo-client	  3000	  React frontend
brian-yolo-backend	5000	  Node.js backend
yolo-ip-mongo	      27017	  MongoDB database (internal use)

🧼 Ignored Files
------------------

- Both frontend and backend have .dockerignore files that:

- Exclude node_modules, logs, and dev files

- Prevent secrets and local setup from being copied into containers

📦 Build Your Own Images
-------------------------

- If you want to build your own images instead of using prebuilt ones:

- Modify the docker-compose.yml to include build: context instead of image:

- Then re-run vagrant up --provision or ansible-playbook playbook.yaml

🧩 Tips
----------

- To stop the app: vagrant ssh → cd /home/vagrant/yolo → docker compose down

- To destroy the VM completely: vagrant destroy

- To test a change in code: Edit, rebuild the image, and re-run compose