# Developer Journal

> Why this file exists: quick, honest notes while building the course to log my learning and thoughts in a central location.

> Note: This journal contains lab notes only. All examples use non-production data,
> fake domains, private RFC1918 IPs, and some well-known public resolvers to function as generic "internet" endpoints. Any credentials shown are are simply for lab purposes only and not used anywhere else.


## 2025-08-19 — First day of journal

* Spent a long time trying to get Docker containers to function how I wanted in day 3
    * Initial intent was to keep everything UI focused and out of the CLI
        * Thinking back that was silly considering the nature of a NOC environment, but I wanted it to be accessible to those not in IT
* Day 3 basically set up, probably an easier/better way to do it, but this will allow a lot of people to get into syslog.
    * I was considering using Kiwi to do the syslog because I am using it for one of my servers, but then I would need the users to enable syslog servers on other devices somehow?
       * ![alt text](image-1.png)
    * But this seemed like a big challenge because I don't know what devices they are using or have access to
        * Therefore I went with Docker
