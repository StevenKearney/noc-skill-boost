🏁 Completed

Created: AUG-17-2025  
Last Updated: AUG-17-2025

# Day 2 — Zabbix (Network Monitoring)

## Goals for the day

1. Download and set up Zabbix on Windows (Docker Desktop to make this more accessible)
2. Add a device to Zabbix and monitor with ICMP (pings)
3. Trigger an alert and see how Zabbix captures and displays the problem/event details
4. Correct the cause of the alert, and verify revocery
5. A short write-up of what I learned from this (see **summary.md** in today's folder)

## What we'll accomplish

1. Install Docker Desktop on Windows and run a Zabbix server locally
2. Create a Zabbix host using the ICMP template
3. Simulate an outage and observe the alert
4. Record the event ID and the recovery details

## Why this matters in a NOC

* Gives us hands-on experience with an uptime monitor, congiguration, alerts, and recovry validation
* Mirrors real network tools and allows us to investigae problems before they are reported

## Lab steps

#### 1. Download and set up Zabbix on Windows (Docker Desktop to make this more accessible)

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
* Create the image
    * In Docker Desktop, you should see a search bar at the top
        * Search for "**zabbix/zabbix-appliance**"
        * Click **Pull**, this may take a minute
        * Navigate to the **Images** tab on the left
            * You should now see the image we just pulled
* Create a data volume
    * 📌This step is optional but recommended if you intend to save any changes. Without this, you will lose everything you do in Zabbix when you restart the container or your PC.
    * Navigate to the **Volumes** tab on the left
        * Select **Create a Volume**
            * We will name it **zbxdbdata**
            * Click **Create**
    * Navigate back to the **Images** tab
* Click **Run** (the play button) next to the image we just downloaded
* Expand the **optional settings** (screenshot/optional-settings.png)
    * Container name: **zabbix-lab**
    * Ports
        * 10051:10051 (look on the right side of each field)
        * 0:443
        * 8080:80
    * If we did the optional step of creating a data volume
        * Volumes
            * Host path: **zbxdbdata**
            * Container path: **/var/lib/mysql**
    * Environment variables
        * Variable: "**PHP_TZ**
        * Value: "**America/New_York**" (can change if you know what yours is)
* Click **Run**
    * This may take a moment to complete. It is setting up our Zabbix server
    * You do not need to wait for anything to happen to continue, try the next step and if it works, it's done
* In a browser, navigate to **http://localhost:8080/**
* This will take us to the Zabbix login page
    * Username: **Admin**
    * Password: **zabbix**
* Once logged in, we should see the **Dashboard** page (screenshots/zabbix-dashboard.png)
    * Please note I am using a dark mode extension, the default is a light theme
    * You also may see an alert for Zabbix server, you can ignore it


#### 2. Add a device to Zabbix and monitor with ICMP (pings)

* In Zabbix, navigate to **Configuration** -> **Hosts** -> **Create host** (top right corner)
    * Here we will configure a "router" using a public IP for training purposes
        * Host name: **Branch-RTR-01**
        * Groups: **Router (new)**
        * Agent interfaces
            * IP address: **1.1.1.1** (this is an ip address used by Cloudflare, we are simply pinging it, so no harm here)
        * Just above **Host name**, navigate to **Templates** (make sure you are **NOT** choosing the option all the way at the top)
            * In the search field, type "**Template Module ICMP Ping**"
                * Click on the matching template that appears
        * Click **Add**
    * Now we will test the "**router**" is functioning properly
        * Navigate to **Monitoring** -> **Latest data**
        * In the **Hosts** search, type "**Branch-RTR-01**" and select it
        * Click Apply
        * We should now see successful pings (screenshots/latest-data.png)

#### 3. Trigger an alert and see how Zabbix captures and displays the problem/event details

* Now we will simulate an outage
    * We will do this by changing the IP address to a non-routable address. This it not something that would normally be done intentionally
    * Navigate to **Configuration** -> **Hosts** -> **Branch-RTR-01** -> **Agent Interfaces**
        * We will change our IP address from **1.1.1.1** to something we cannot reach like **10.255.255.254**
        * Click **Update**
    * Navigate back to the **Dashboard**
        * **Monitoring** -> **Dashboard** (This should also appear in **Monitoring** -> **Problems**)
        * Now we will wait for the alert
            * This may take a few minutes to be picked up as an issue within Zabbix
        * After some time, we should be alerted of the issue (screenshots/alert.png)

#### 4. Correct the cause of the alert, and verify revocery

* You should now see the alert, and we can resolve it
    * Navigate to **Configuration** -> **Hosts** -> **Branch-RTR-01**-> **Agent Interfaces**
        * We will change our IP address back to an alive IP, for example **1.1.1.1**
        * Click **Update**
* We will now verify the problem is resolved
    * Navigate to **Monitoring** -> **Problems**
        * Here we should see the **Status** is "**RESOLVED**"
        * We can click the time stamp under "**Time**" to view more information about the event (screenthost/resolved.png)
* Now that we see the problem is resolved, we can do a manual verification
    * Navigate to **Monitoring** -> **Latest data**
        * In the **Hosts** search, type "**Branch-RTR-01**" and select it (if not already populated)
        * Here we verify the ICMP pings are successful, latency, and any packet loss (screenshots/verify.png)
* 📌 Optionally, you can now create a ServiceNow incident for this outage

#### 5. A short write-up of what I learned from this (see **summary.md** in today's folder)

* Please see **summary.md** in the directory for today if you are interested in what I learned creating and running this lab

* If you are reading this, thank you so much and I hope you found something of value in here