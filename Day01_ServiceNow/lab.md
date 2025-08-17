🏁 Completed

Created: AUG-16-2025  
Last Updated: AUG-16-2025

# Day 1 — ServiceNow (Incident Management)

## Goals for the day

1. Spin up a free ServiceNow Personal Developer Instance (PDI)
2. View simulated incidents and create a new one
3. Work through the incident like a NOC technician
4. See SLAs in action (built-in timers, no custom configuration)
5. A short write-up of what I learned from this (see **summary.md** in today's folder)

## What we'll accomplish

1. Set up a ServiceNow developer account and open your PDI
2. Create a realistic **Incident** with a caller
3. Set **Impact/Urgency** so **Priority** auto-calculates
4. Work the ticket from start to finish: **New -> In Progress -> On Hold -> Resolved -> Closed**

## Why this matters in a NOC

* Demonstrates ticket **workflow** and **documentation**
* Gets us familiar with **SLAs** and how incident response works
* Hands-on practice using **ServiceNow**

## Lab steps

#### 1. Spin up a free ServiceNow Personal Developer Instance (PDI)

* Sign up using this link
    - https://developer.servicenow.com/dev.do
* Once signed up, click **Start Building**, this may take a few minutes to initialize

#### 2. View simulated incidents and create a new one

* In the new tab that opens, navigate to: **All** -> **Service Desk** -> **Incidents**
* You should now see the **Incidents** list. It should look similar to our screenshot "Incidents.png" in the screenshots/ folder
    * For easier access, we can add this page to our "**Favorites**" by selecting the **star icon** at the top of the page.
    * Here we can sort, filter, and view all incidents. Feel free to click on things to see how it behaves

#### 3. Work through the incident like a NOC technician

* Now we will simulate creating a case from a caller
    * From the main page, click the "**New**" button in the top right corner
        * This will open the "**Incident - Create**" page (screenshot **"Create-Incident.png**")
            * This page shows us the basic information about the issue, such as the **Incident Number** (auto generated), the **caller**, a **description** of the issue, who the incident is **assigned to**, and other information
        * For our example, **Bryan Rovell** is calling in to report his office has lost internet connectivity (Screenshots/**Incident-Filled-Out**)
            * We will put his name into the "**Caller**" field. This should find his name and allow us to select it
            * Bryan states he lost internet access
            * We will then fill out the incident (feel free to use your own verbiage or even create your own incident) using the below information
                * **Channel**: Phone
                * **Category**: Network
                * **Impact**: Medium
                * **Urgency**: High
                    * Notice the **Priority** field auto updated to "**2 - High**" based on our **Impact** and **Urgency** selections
                * **Configuration item**: Branch-RTR-01 (for this example, but it will not work because it isn't valid in this test environment)
                * **Short description**: Branch Router Down - packet loss 100%
                * **Description**: 
                    ``` 
                        Reported by: Bryan Rovell (Branch Manager – Cleveland) at 2025-08-16 18:07 UTC
                        Detected by: LAB-ALERT-001 (simulated) – ICMP to Branch-RTR-01 failed
                        Symptoms: 100% packet loss for 5+ minutes; users report no WAN access
                        Impact: 1 site (~35 users) offline – VoIP/SaaS unreachable
                        Initial checks: Ping 0/10; tracert stops at ISP edge (screenshot attached)
                        Actions: Opened ISP ticket #987654; notified Bryan with status; set Priority = P2
                        Next step: Await ISP ETR; escalate to Major Incident if >30 min
                    ```
                * **Assignment group**: Network
                * **Assigned to**: ITIL User (for this example)
            * Click "**Submit**" to generate the incident and add it to our list
                * You may **Resolve** the incident immediately and can use **Resolve** instead
            * In our list of incidents, locate the new incident we just created. There are multiple ways to do this, so use whichever you think is best; and open the incident
        * Simulating workflow
            * Once you have the incident opened again, we will update the **Work notes** as the **State** of the incident changes (Screenshots/**Work-Notes**)
                * New
                    * This is the default state for new incidents awaiting triage or ownership
                    * **SLA response** timer begins
                    * Work note: Acknowledged issue, p2 based on impact
                * In Progress
                    * Someone is working on the issue
                    * The **SLA resolution** timer runs while you are working
                    * Work note: tracert stops at ISP edge, opened ticket with ISP, updated Bryan
                * On Hold
                    * The issue is waiting on someone or something else to continue work (for example, ISP issue, waiting on the ISP to resolve problem)
                    * The **SLA resolution** timer pauses
                    * Work note: Placed on hold, awaiting 3rd party ISP, ETR 19:15 UTC. Will resume when update arrives
                * Resolved
                    * The issue is fixed and has been verified
                    * The **SLA resolution** timer stops
                    * You will need to select a **Resolution Code** and put **Resolution notes** in the incident (Screenshots/**Resolution-Information**)
                        * Resolution code: Solution Provided (this will vary depending on your policies)
                        * Resolution notes: ISP cleared a last-mile circuit fault causing complete loss of connectivity at the Cleveland branch. Verified connectivity via ping 10/10, latency 19-23 ms, 0% loss
                * Closed
                    * User confirms issue resolved or automatic based on auto-closure window (for example, 72 hours)
                * Canceled
                    * Incident is not needed for some reason
                        * Duplicate
                        * Opened in error
                        * Wrong ticket type (RFC for example)
                        * False alarm (after verifying)

#### 4. See SLAs in action

* Notice at the bottom of the incident there are different timers for SLAs (Screenshots/**SLAs**)
    * These will vary by agreement with clients and policies
    * They will count down or pause depending on the incident **State**

#### 5. A short write-up of what I learned from this (see **summary.md** in today's folder)

* Please see **summary.md** in the directory for today if you are interested in what I learned creating and running this lab

* If you are reading this, thank you so much and I hope you found something of value in here



