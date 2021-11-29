---
layout: post
title: 'PS Clockify Part 1: Getting Started'
author: Jeff
image: /assets/images/posts/psclockify/title-image.png
categories:
    - Productivity
    - PowerShell
    - Clockify
    - Automation
tags:
    - Automate Everything

---
Need help managing your time? Are you having trouble balancing everything? Look no further. This is the beginning of a series of blog posts where I show you how to use PowerShell and PowerBI to automate time management!

<!--more-->
<br>

# Background
Time remains one of the few things in life that is impervious to outside influence. Everyday we are all alloted the same 24 hours to do with as we please. Being able to properly manage time can boost productivity and efficiency. About a year ago, I wrote a PowerShell API wrapper for Clockify, a free time management tool and wanted to take the time to show how it can be used to juggle the many day to day challenges we are faced with. We will start with importing and using the module and end up with a Power BI Dashboard that can be used to visually see where you are spending most of your time.

<br>

<h2 class="drac-heading drac-heading-xl drac-text-orange">Setting Up Clockify</h2>
First, we will need to create a Clockify account, which can be done [here](https://clockify.me/signup).
<br><img src="/assets/images/posts/psclockify/signup.png" class="center" width="80%" />

Next let's generate an API key by clicking on the avatar at the top right and then **Profile Settings** or by navigating directly to [https://clockify.me/user/settings](https://clockify.me/user/settings). Scroll to the bottom of the page and click **Generate**.
<br><img src="/assets/images/posts/psclockify/api.png" class="center" width="80%" />

Finally, we will need the workspace name. This can be found on the **General tab of Settings** or to the left of the upgrade button on the top bar. Feel free to edit the name to be whatever you like.
<br><img src="/assets/images/posts/psclockify/workspace.png" class="center" width="80%" />

<h2 class="drac-heading drac-heading-xl drac-text-orange">Installing the Module</h2>
[Source Code](https://github.com/ursaMaj0r/PSClockify) - The module was written with PS Core and works on both Mac and Windows!

Let's download the code and import it as a PowerShell module using the following code:
<script src="https://gist.github.com/ursaMaj0r/33e970b84c9ea884a2f8b1cbdfb752a5.js"></script>

<br><img src="/assets/images/posts/psclockify/install.png" class="center" width="80%" />

<h2 class="drac-heading drac-heading-xl drac-text-orange">Using the Module</h2>

### Connect to Clockify Workspace
Let's get connected to Clockify by running the following command (replacing with your API Key and Workspace name from above). If you want to confirm that you are connected, you can run the *Test-Session* command.
<script src="https://gist.github.com/ursaMaj0r/8676fbb9741615b63992ae360654c43b.js"></script>

<br><img src="/assets/images/posts/psclockify/connection.png" class="center" width="80%"/>

### Creating Stuff
Let's get started by creating a project called chores. We can do that using the module with the following command:

<script src="https://gist.github.com/ursaMaj0r/0722d1e01fee80990d6068060b3fe64b.js"></script>

And we can't forget to add some tasks to the project:
<script src="https://gist.github.com/ursaMaj0r/9c684f21c70ac02023ba3f194043e907.js"></script>

<br><img src="/assets/images/posts/psclockify/create-stuff.png" class="center" width="80%" />

### Event Tracking
Lastly, we need to be able to track the time we spend on each task. Let's track some time doing laundry!

<script src="https://gist.github.com/ursaMaj0r/121ceeb238742e35127188218f12da89.js"></script>

And when we are finished, we can simply run *Stop-Timer*.
<br><img src="/assets/images/posts/psclockify/timer.png" class="center" width="80%" />

### And More
The module can be used to do pretty much anything you can do in the UI. Try playing around with some other commands to create, set, or delete any object (Clients, Projects, Tags, Tasks, and Timers)!

> Examples:
<script src="https://gist.github.com/ursaMaj0r/18d0d25594320b22a60d30aeb496bed6.js"></script>

### Disconnecting a Session
When you are finished working with Clockify, the following command can be used to log out:
<script src="https://gist.github.com/ursaMaj0r/c638b8f4f5b5ccdffc2ee7a8c3250e9c.js"></script>

<br>
<br>
<h1 class="drac-heading drac-text-center">Stay tuned for part two!</h1>