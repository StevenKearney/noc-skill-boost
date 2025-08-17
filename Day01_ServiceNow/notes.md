## 🚧 WIP

Last Updated: AUG-16-2025

# Day 1 - ServiceNow

## Goals for the day
    
1. Spin up a free ServiceNow Personal Developer Instance (PDI)
2. View simulated incidents and create a new one
3. Attach an SLA
4. Work through the incident like a NOC technician

## What we'll accomplish

1. Set up a ServiceNow developer account
2. Create a **Network Operations** assignment group, a test "**caller**", and a **CI** (Configuration Item) will will call **Router-1**
3. Open an **Incident**, set **Impact/Urgency**, decide on a **Priority Level**, and attach an **SLA**
4. Work the ticket from start to finish: **New -> In Progress -> On Hold -> Resolved -> Closed**
5. We will also capture screenshots and write a short **incident report**

## Why this matters in a NOC

* Demonstrates ticket **workflow** and **documentation**
* Gets us familiar with **SLAs** and how incident response works
* Familiarizes us with **ServiceNow**

## Lab steps

#### 1. Obtain a free PDI from ServiceNow

* Sign up using this link
    - https://developer.servicenow.com/dev.do
* Once signed up, click **Start Building**, this may take a few minutes to initialize
* In the new tab that opens, navigate to: **All** -> **Service Desk** -> **Incidents**
* You should now see the **Incidents** list. It should look similar to our screenshot "Incidents.png" in the screenshots/ folder
    * For easier access, we can add this page to our "**Favorites**" by selecting the **star icon** at the top of the page.
    * Here we can sort, filter, and view all incidents. Feel free to click on things to see how it behaves

