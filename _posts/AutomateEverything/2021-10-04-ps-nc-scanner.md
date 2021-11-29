---
layout: post
title: PowerShell Port Scanner with Netcat
date: 2021-10-04 21:00 +0000
id: 04
author: Jeff
image: 
categories:
    - PowerShell
    - Cyber
    - Scripts
    - Automation
tags:
    - Automate Everything

---
Yet another netcat port scanner...but with a twist of PowerShell.

<!--more-->

## Overview

The below PowerShell Core script was created using netcat to scan a target host for a list of open ports. I chose to write the script in PowerShell Core because it can be run on any machine (not just Windows). The script works by iterating through a list of ports utilizing the zero I/O mode of netcat. Simply put, it attempts to open a connection on a specified TCP port with no data in the payload. This quickly can be used to determine if the port is open or closed and then is reported back to the user. In order to improve speed, I have chosen to set the timeout to one second using the w parameter.

## How to Use

The script takes in the following parameters:

1.  **Target** \- IP Address or FQDN of computer that will be scanned
2.  **PortRange** \- list of ports to scan (can be input as a single value, range, or list)
3.  **openOnly**  \- will only return the ports that were found to be open

## Examples

For the purposes of the examples we will use google.com as the target. We will assume the target is a web server with ports 80 and 443 open (HTTP/HTTPS).

### Example 1: Single Port

The following example can be used to determine if the server accepts HTTP traffic on port 80.

`Start-PortScan -target google.com  -portRange 80`

### Example 2: Range of Ports

The following example can be used to determine if the server has any open ports between 75 and 85.

`Start-PortScan -target google.com -portRange (75..85)`

<br><img src="/assets/images/posts/ps-nc-scanner/example.png"  width="20%" />


### Example 3: Open Only

The following example can be used to determine if the server has any open ports between 75 and 85. With the addition of the switch parameter, *openOnly*, the results will omit closed ports.

`Start-PortScan -target google.com -portRange (75..85) -openOnly`


## Script:
<script src="https://gist.github.com/ursaMaj0r/ba2b91e022f279fb80bc71b6d01295f3.js"></script>