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


* Docker commands
    * docker exec opensearch curl -X DELETE "localhost:9200/security-auditlog-2025.08.20"
        * fixed cluster issue
    *
        ```
                for ($i=1; $i -le 20; $i++) { 
            if ($i % 2 -eq 1) { 
                docker run --rm --network graylog-net busybox sh -c "echo '<134>Aug 20 23:21:00 LAB Interface DOWN #$i' | nc -u -w1 graylog 1514"
            } else { 
                docker run --rm --network graylog-net busybox sh -c "echo '<134>Aug 20 23:21:00 LAB Interface UP #$i' | nc -u -w1 graylog 1514"
            }
            Start-Sleep 2
        }
        ```
    * generates noise
    ```
        for ($i=1; $i -le 20; $i++) {
        $status = if ($i % 2) { 'DOWN' } else { 'UP' }
        $now = Get-Date
        $day = $now.Day.ToString().PadLeft(2,' ')
        $ts  = "{0} {1} {2}" -f $now.ToString('MMM'), $day, $now.ToString('HH:mm:ss')
        $msg = "<134>$ts LAB Interface $status #$i"
        docker run --rm --network graylog-net -e MSG="$msg" busybox sh -lc 'printf "%s\n" "$MSG" | nc -u -w1 graylog 1514'
        Start-Sleep 2
        }
    ```
    * shows in search but garbled message
    ```
                <#
        .SYNOPSIS
        Sends test syslog messages to Graylog for testing and validation.

        .DESCRIPTION
        This script sends properly formatted RFC 5424 syslog messages to a Graylog server
        using a Docker container. It includes proper timestamp formatting and structured data.
        #>

        # Function to send syslog message
        function Send-SyslogMessage {
            param(
                [string]$Message,
                [string]$Severity = "Info"
            )
            
            # Create RFC 5424 compliant timestamp
            $timestamp = Get-Date -Format "yyyy-MM-ddTHH:mm:ss.fffZ"
            
            # Map severity levels to syslog severity codes
            $severityMap = @{
                "Emergency" = 0
                "Alert"     = 1
                "Critical"  = 2
                "Error"     = 3
                "Warning"   = 4
                "Notice"    = 5
                "Info"      = 6
                "Debug"     = 7
            }
            
            $severityCode = $severityMap[$Severity]
            $priority = (16 * 8) + $severityCode  # Facility 16 (local0) + severity
            
            # Create structured data
            $structuredData = "[exampleSDID@32473 i=`"$i`" source=`"PSScript`"]"
            
            # Create RFC 5424 compliant message
            $syslogMsg = "<$priority>1 $timestamp $env:COMPUTERNAME - - $structuredData $Message"
            
            # Send via Docker
            try {
                docker run --rm --network graylog-net `
                    -e MSG="$syslogMsg" `
                    busybox sh -c 'echo "$MSG" | nc -w1 -u graylog 1514'
                Write-Host "Sent: $Message" -ForegroundColor Green
            }
            catch {
                Write-Host "Error sending message: $_" -ForegroundColor Red
            }
        }

        # Main execution
        Write-Host "Starting Graylog test message sender..." -ForegroundColor Cyan
        Write-Host "Press Ctrl+C to stop" -ForegroundColor Yellow

        $i = 1
        while ($true) {
            $status = if ($i % 2) { 'DOWN' } else { 'UP' }
            $message = "LAB Interface $status #$i"
            
            # Alternate between Info and Error severity
            $severity = if ($i % 4) { 'Info' } else { 'Error' }
            
            Send-SyslogMessage -Message $message -Severity $severity
            
            $i++
            Start-Sleep 2
        }
    ```
    * This seems to work great