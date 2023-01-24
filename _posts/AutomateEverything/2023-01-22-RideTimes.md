---
layout: post
title: Theme Park Alerts
date: 2023-01-22 00:00 +0000
author: Jeff
image: /assets/images/posts/RideAlerts/ride.gif
categories:
    - Home Assistant
    - IoT
    - Disney
    - Node Red
tags:
    - Automate Everything
---

Let's setup some notifications so we can spend more time on rides!
<!--more-->

# Overview
With the [Theme Parks HACS integration](https://github.com/danielsmith-eu/home-assistant-themeparks-integration) for Home Assistant, we can easily integrate theme park data into any Home Assistant instance using the ThemeParks.wiki API. This project takes that data and creates a custom ride wait time notification system that will notify you about which of your favorite rides have a low wait. Each ride can be enabled and
disabled at any point, and the wait time threshold can be configured for each ride individually! The system sends a rollup alert every 30 minutes during park hours and can also be triggered via a chat command for on demand updates!

## Home Assistant

### Setting up the Theme Parks Add On
The first step is to add the theme parks integration into Home Assistant. This integration is a HACS add on, so if you do not have HACS installed yet, check out [Install HACS](https://hacs.xyz/docs/setup/download/). Next, we will need to add the add on as a custom repository. To do this head to http://homeassistant.local/hacs/integrations and click the menu button at the top right to add custom repositories. Paste in the URL from the overview, click add, and restart Home Assistant to finish the installation process.

Finally, after Home Assistant finishes restarting head to the integrations page and add the theme park you are interested in monitoring. We will use Walt Disney World as an example.

<img class="center" width="25%" src="/assets/images/posts/RideAlerts/ra-integration.png"/>

### Setting up Helpers
After adding the Walt Disney World integration, each ride has a sensor entity that displays the current wait time. In order to create ride alerts, we will need two helper objects for each ride we want to track.

Example for Avatar: Flight of Passage

<table class="drac-table">
  <thead>
    <tr>
      <th class="drac-text drac-text-white">Name</th>
      <th class="drac-text drac-text-white">Type</th>
      <th class="drac-text drac-text-white" style="max-width: 200px">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="drac-text drac-text-white">sensor.avatar_flight_of_passage_walt_disney_world_r_resort</td>
      <td class="drac-text drac-text-white">Sensor</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This is how the theme park integration stores ride wait time information and should show up automatically after you select a theme park. 
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">input_boolean.ride_alerts_avatar_flight_of_passage_notification</td>
      <td class="drac-text drac-text-white">Input Boolean</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This helper will be used to determine whether or not we want to receive ride alerts for this ride. This must match the ride sensor name and end with _notification for the flow later.
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">binary_sensor.ride_alerts_avatar_flight_of_passage_wait_time</td>
      <td class="drac-text drac-text-white">Threshold</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This helper will be used to determine how low of a wait we want to alert on for this ride. This must match the ride sensor name and end with _wait_time for the flow later.
      </td>
    </tr>
  </tbody>
</table>

After you have created two helpers for each ride and confirmed they are named based on the above naming standard, you can move on to the next step.

## Other Useful Helpers/Objects

### Alert Group
It may make sense to create a group for the input booleans based on each park to easily turn on and off alerts based on your itinerary. To do that, add the following to your Home Assistant Configuration:

<div>
    <code class="custom-codeblock">
---
animal_kingdom_ride_alerts:
  name: Ride Alerts - Animal Kingdom
- platform: group
  entities:
    - input_boolean.ride_alerts_avatar_flight_of_passage_notification
    - input_boolean.ride_alerts_expedition_everest_legend_of_the_forbidden_mountain_notification
    - input_boolean.ride_alerts_kilimanjaro_safaris_notification
    </code>
</div>

### Average Wait Time
You can also create a helper that takes the average of all the wait times in a park, that way you know which park is busiest.

<img class="center" width="25%" src="/assets/images/posts/RideAlerts/ra-average-wait.png"/>

## Setting up the Dashboard
Next, we will setup the dashboard in Home Assistant that will be used to control what rides we want to alert on while we are in the park. I wanted the ability to be able to configure both which rides are alerting and how long we were willing to wait without needing a laptop to update any code. Below is an example of dashboard that has 3 rides, a group toggle, and the average wait time between all three rides.

<img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-dashboard.png"/>

<div>
    <code class="custom-codeblock">
square: false
columns: 1
type: grid
cards:
  - type: custom:mushroom-title-card
    title: Ride Alerts
    subtitle: Animal Kingdom
  - type: custom:mushroom-entity-card
    entity: sensor.animal_kingdom_average_wait_time
    icon: mdi:account-clock
    layout: vertical
    fill_container: false
  - type: custom:mushroom-entity-card
    entity: group.animal_kingdom_ride_alerts
    layout: vertical
    icon: mdi:toggle-switch
  - type: custom:mushroom-entity-card
    entity: sensor.avatar_flight_of_passage_walt_disney_world_r_resort
    secondary_info: state
    icon_type: none
    fill_container: false
    layout: vertical
  - square: false
    columns: 2
    type: grid
    cards:
      - type: custom:mushroom-entity-card
        entity: input_boolean.ride_alerts_avatar_flight_of_passage_notification
        name: Notifications
        fill_container: false
        layout: vertical
      - type: custom:mushroom-entity-card
        entity: binary_sensor.ride_alerts_avatar_flight_of_passage_wait_time
        name: Short Wait!
        layout: vertical
  - type: custom:mushroom-entity-card
    entity: >-
      sensor.expedition_everest_legend_of_the_forbidden_mountain_walt_disney_world_r_resort
    primary_info: name
    secondary_info: state
    icon_type: none
    fill_container: false
    layout: vertical
  - square: false
    columns: 2
    type: grid
    cards:
      - type: custom:mushroom-entity-card
        entity: >-
          input_boolean.ride_alerts_expedition_everest_legend_of_the_forbidden_mountain_notification
        name: Notifications
        fill_container: false
        layout: vertical
      - type: custom:mushroom-entity-card
        entity: >-
          binary_sensor.ride_alerts_expedition_everest_legend_of_the_forbidden_mountain_wait_time
        name: Short Wait!
        layout: vertical
  - type: custom:mushroom-entity-card
    entity: sensor.kilimanjaro_safaris_walt_disney_world_r_resort
    primary_info: name
    secondary_info: state
    icon_type: none
    fill_container: false
    layout: vertical
  - square: false
    columns: 2
    type: grid
    cards:
      - type: custom:mushroom-entity-card
        entity: input_boolean.ride_alerts_kilimanjaro_safaris_notification
        name: Notifications
        fill_container: false
        layout: vertical
      - type: custom:mushroom-entity-card
        entity: binary_sensor.ride_alerts_kilimanjaro_safaris_wait_time
        name: Short Wait!
        layout: vertical
    </code>
</div>

## Node Red
Below is the Node Red automation that will be used to determine which rides are currently alerting and will roll that information up into a single message with each ride and the current wait time.

<img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow.png"/>

<table class="drac-table">
  <thead>
    <tr>
      <th class="drac-text drac-text-white">Step</th>
      <th class="drac-text drac-text-white">Node Type</th>
      <th class="drac-text drac-text-white" style="max-width: 200px">Description</th>
      <th class="drac-text drac-text-white" style="max-width: 200px">Configuration</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="drac-text drac-text-white">Trigger</td>
      <td class="drac-text drac-text-white">Inject OR Incoming Webhook</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This flow is triggered every 30 minutes via an inject node. It can also be triggered by webhook.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-1.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Reset Alerts</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node resets all of the flow variables.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-2.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Get Active Ride Alerts</td>
      <td class="drac-text drac-text-white">Get Entities</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will get all the rides that have their input boolean helper set to on and store it in a flow array called rides.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-3.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Get Count of Active Ride Alerts</td>
      <td class="drac-text drac-text-white">Get Entities</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will get the number of rides to enumerate.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-4.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Set Number of Rides</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will store the number of rides to enumerate in a flow variable ride_count.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-5.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">For Each Ride</td>
      <td class="drac-text drac-text-white">msg-resend</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will iterate through each ride to check one at a time.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-6.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Set Counter</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will store the current count in the loop called ride_counter.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-7.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Get Ride</td>
      <td class="drac-text drac-text-white">Array Iterator</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will get current ride out of the rides array.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-8.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Extract Entity ID</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will get the entity id of the input boolean helper for the ride.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-9.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Set Current Ride</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will store the entity id in a flow variable called current_ride.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-10.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Normalize</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will store the name of the ride in a readable format in a flow variable called current_ride_normalized.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-11.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Find Wait Time</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will take the value in flow.current_ride and use our naming standard above to convert it to the sensor object for the ride. It will be stored in current_ride_wait_entity.
      </td> 
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-12.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Query HA</td>
      <td class="drac-text drac-text-white">Get Entities</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will query Home Assistant to find the current wait time for the ride and store it in.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-13.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Set Current Wait</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node stores the current wait in current_ride_wait.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-14.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Find Ride Alert Status</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node uses the naming standard to store the name of the threshold helper in current_ride_wait_threshold_entity.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-15.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Query HA</td>
      <td class="drac-text drac-text-white">Get Entities</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will query Home Assistant to get the state of the threshold sensor for the ride.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-16.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Set Current Alert Status</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will store the result of the previous query in current_ride_in_alert.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-17.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">If Over Threshold</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will check to see if the ride has a short wait (if threshold sensor is on).
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-18.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Add to Ride Alerts</td>
      <td class="drac-text drac-text-white">Function</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will append the ride to a flow variable for the notification.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <code class="custom-codeblock">
          var current_alerts = flow.get("ride_alerts");
          var current_ride =  " * " + flow.get("current_ride_normalized");
          var current_wait = flow.get("current_ride_wait");
          var ride_status = current_ride.concat(": ", current_wait + " minutes");
          var new_alerts = current_alerts.concat(" ", ride_status  + "\n");
          flow.set("ride_alerts", new_alerts);
          msg.payload = flow.get("ride_alerts");
          return msg;
        </code>   
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Exit</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will detect the end of the loop.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-19.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">If Alerts</td>
      <td class="drac-text drac-text-white">Change</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will check to see if any rides are in an alert status.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-20.png"/>
      </td>
    </tr>
    <tr>
      <td class="drac-text drac-text-white">Get Ride Alerts</td>
      <td class="drac-text drac-text-white">Template</td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        This node will create the message that will be used for the notification.
      </td>
      <td class="drac-text drac-text-white" style="max-width: 200px">
        <img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-flow-21.png"/>
      </td>
    </tr>
  </tbody>
</table>

## RocketChat
Every 30 minutes the flow is configured to send a notification to a Rocket Chat channel. This can easily be swapped out for Slack, Discord, Mattermost, or Teams. It may also make sense to add a webhook that can be used to trigger the automation on demand. In this example, the automation will be triggered any time that the word "rides" is posted in the channel.

<img class="center" width="75%" src="/assets/images/posts/RideAlerts/ra-alert.png"/> 