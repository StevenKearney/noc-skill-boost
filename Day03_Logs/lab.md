🚧 WIP - Initial setup complete, want to add more things from Graylog in here. struggled to get this going within Docker. Way easier with CLI than with the UI like I tried to do initially to make it less "scary" to people or something I guess? Should have just started with CLI.

Created: AUG-19-2025  
Last Updated: AUG-19-2025

# Day 3 — Graylog (Syslog & Log Analysis)

## Goals for the day

* Learn and understand how syslogs work
* Spin up Graylog on Windows using Docker Desktop (we are using Docker Desktop to make this more accessible)
* Create a syslog **UDP** input, capture real events, apply filters, and search through them

## What we'll accomplish

0. 📌 (optional) Set up **Docker Desktop** for **Windows** (can skip if completed already)
1. Create a Docker network and spin up containers: **MongoDB**, **OpenSearch**, and **Graylog**
2. Create a **Syslog UDP** input on port **1514** (to minimize risk of potential conflicts, normally port **514** is used for syslog.)
3. Send 2 test messages (output & recovery) with a **busybox** container
4. A short write-up of what I learned from this (see **summary.md** in today's folder)


## Why this matters in a NOC

* NOC's rely on logged data to make informed decisions on incidents
* Graylog provides **centralized visibility** of syslogs from many devices (such ase **routers**, **switches**, and **firewalls**) so the NOC isn't manuually checking **CLIs** of devices
    * This also improves **SLAs**
* These logs provide evidence which can be attached to incidents in systems such as **ServiceNow**
* Understanding how to filter out noise from the logs, and focus on important events is crucial to a well functioning NOC
* **Syslogs** are utilized in many networking environments, and are **vendor agnostic**
* Monitoring **datastore readiness**, **container health**, and **port management** are daily tasks in a NOC

## Lab steps

#### 0. (optional) Set up **Docker Desktop** for **Windows** (skip if completed already completed)

📌 **You can skip the Docker setup below if you already followed the instructions in another lab.**

* Before we begin, you **do not** need to know what Docker is, I will assume you have no familiarity with it. I will walk you through all steps to set up the server.

* Install/enable Docker Desktop
    * Download Docker Desktop and install
        * https://www.docker.com/products/docker-desktop/
            * **Download Docker Desktop** -> **Download for Windows AMD64**
            * Save to a location, most likely your **Downloads** folder
            * Once downloaded, run the exe
                * We will be using WSL 2, so make sure that box is checked, as well as the desktop shortcut
                * Click **OK**
                * Docker will now install
                * When complete, click **Close**
* Open Docker Desktop
    * Read and accept their terms and conditions
    * It may prompt you to create an account or sign in
        * You can do so if you want
        * Otherwise, click **skip**


#### 1. Create a Docker network and spin up containers: **MongoDB**, **OpenSearch**, **BusyBox** and **Graylog**

* We will be using the CLI to speed up this process. I will provide full commands for what we need, all you have to do is copy/paste

##### **Creating a Docker Network and Volumes**

* To make setup easier, we will create a **Docket Network**
    * This will allow the different container to talk to eachother without a more involved configuration
    * In the bottom right corner of **Docker Desktop**, click the "**>_ Terminal**" button
        * Copy/paste the following
            ```
            docker network create graylog-net
            docker volume create gl_mongo_data
            docker volume create gl_os_data
            docker volume create gl_graylog_data
            ```
        * You should see a string of text appear, as well as the volume names indicating succcess

##### **Pulling the images and creating containers**

* We will manually pull the images first (not *needed*, but to help you troubleshoot if there is a problem)
* In the same terminal, copy/paste the following and press **enter**
    ```
    docker pull mongo:7.0
    docker pull opensearchproject/opensearch:2.15.0
    docker pull graylog/graylog:6.3.1
    docker pull busybox:latest
    ```
    * This may take a bit to download, good time for some coffee

* After pulling the images we need to run the containers
    * **MongoDB**, copy/paste:
        ```
        docker run -d --name mongo --network graylog-net `
        -v gl_mongo_data:/data/db mongo:7.0

        ```
        * You should be able to see the container now in the **Containers** tab on the left
    * **OpenSearch**, copy/paste:
        ```
        docker run -d --name opensearch --network graylog-net `
            -p 9200:9200 -p 9600:9600 `
            -e "discovery.type=single-node" `
            -e "DISABLE_SECURITY_PLUGIN=true" `
            -e "DISABLE_INSTALL_DEMO_CONFIG=true" `
            -e "OPENSEARCH_JAVA_OPTS=-Xms512m -Xmx512m" `
            -v gl_os_data:/usr/share/opensearch/data `
            opensearchproject/opensearch:2.15.0
        ```
        * 🚨 As a reminder, this is not a security oriented course
        * You should be able to see the container now in the **Containers** tab on the left
    * **Graylog**, copy/paste:
        ```
        docker run -d --name graylog --network graylog-net `
            -p 9000:9000 -p 1514:1514/udp `
            -v gl_graylog_data:/usr/share/graylog/data `
            -e "GRAYLOG_PASSWORD_SECRET=TNi4x0NnS9kZ6B2xQw8rV1uY3p5s7d9f" `
            -e "GRAYLOG_ROOT_PASSWORD_SHA2=766af3e91e94cb8e4e14fbdf040c9f37b0cc994f2fa6d44bf53617373641332a" `
            -e "GRAYLOG_HTTP_EXTERNAL_URI=http://localhost:9000/" `
            -e "GRAYLOG_MONGODB_URI=mongodb://mongo:27017/graylog" `
            -e "GRAYLOG_ELASTICSEARCH_HOSTS=http://opensearch:9200" `
            graylog/graylog:6.3.1
        ```
        * 🚨 As a reminder, this is not a security oriented course
        * Admin credentials
            * User: admin
            * Password: Graylog123!
        * You should be able to see the container now in the **Containers** tab on the left
        * We can open a browser and navigate to http://localhost:9000/
            * Log in using the credentials above

* The **Containers** tab in **Docker Desktop** should look like the screenshot (screenshots/docker-containers.png)
* We should now successfully be logged into the Graylog interface


#### 2. Create a **Syslog UDP** input on port **1514** (to minimize risk of potential conflicts, normally port **514** is used for syslog.)

* Next we need to bind a port to listen for UDP traffic
    * Navigate to **System** -> **Inputs**
    * In the dropdown box labaled **Select input** select **Syslog UDP**
    * Click **Launch new input** (screenshots/input-1.png)
        * Check the **Global** box
        * **Title**: Syslog/UDP 1514
        * **Bind Address**: 0.0.0.0
        * **Port**: 1514
        * Scroll down and click **Launch Input**
        * You should now see the input running (screenshots/input-running.png)
            * Note the blue "**Stop input**" button on the right to indicate it is running
* Now that the port is bound, we can show all received messages for this input by selecting "**Show received messages**" on the right side of the screen

#### 3. Send 2 test messages (output & recovery) with a **busybox** container

* Finally we can send some test messages to **Graylog**

    * To do this, we will use the **Docker** CLI again and simulate a network device with **BusyBox**
        * First we will simulate a device going down
            * Copy/paste this first command to simulate BRANCH-RTR-01 going down
            ```
            docker run --rm --network graylog-net busybox sh -c "printf '<134>Aug 20 22:58:00 LAB Branch-RTR-01: WAN link down\n' | nc -u -w1 graylog 1514"
            ```
            * And after a moment refresh the browser page you have open with the messages from the input
                * You should now see the router log message stating it is down

        * Then we will simulate a device coming back up
            * Copy/paste this first command to simulate BRANCH-RTR-01 coming up
            ```
            docker run --rm --network graylog-net busybox sh -c "printf '<134>Aug 20 23:07:00 LAB Branch-RTR-01: WAN link restored\n' | nc -u -w1 graylog 1514"
            ```
            * And after a moment refresh the browser page you have open with the messages from the input
                * You should now see the router log message stating it has been restored

#### 4. A short write-up of what I learned from this (see **summary.md** in today's folder

* Please see **summary.md** in the directory for today if you are interested in what I learned creating and running this lab

* If you are reading this, thank you so much and I hope you found something of value in here