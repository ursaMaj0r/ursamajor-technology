---
layout: post
title: Did you feed the dog?
date: 2021-09-14 21:00 +0000
id: 01
author: Jeff
image: /assets/images/posts/did-you-feed-the-dog/title.png
categories:
    - Smart Home
    - Doggo
    - Home Assistant
    - NFC Tags
tags:
    - Automate Everything


---
Have you ever forgotten if you fed the dog breakfast? Or maybe your dog is like mine and is always ready for another meal... Look no further!
<!--more-->

# Background
Calli the dog has always been motivated by one thing in life: food. In fact, it motivates her so much she has been know to ask for breakfast *one human at a time.* Like most dogs as soon as someone steps into the kitchen in the morning, Calli knows its time for breakfast. However, she also knows that after a delicious meal, there's a chance for more. She waits patiently for the next person to make their way down to the kitchen, and you guessed it, "Breakfast Time!?!?!", she exclaims. We needed another way to keep track of this, so I created a system to sort out this conundrum using Home Assistant, our phones, and some NFC Tags!
<img src="/assets/images/posts/did-you-feed-the-dog/calli-food.png" style="display: block;margin-left: auto;margin-right: auto;width:30%;"/>
<br>

<h2 class="drac-heading drac-heading-xl drac-text-orange">Getting Started</h2>
### Before beginning this tutorial, please make sure you have the following:
* Home Assistant
* NFC Tags --> [Purchase Here! (paid link)](https://amzn.to/3tIsnGh)
<br>

<h2 class="drac-heading drac-heading-xl drac-text-orange">Creating the Helpers</h2>
<div>
  <div
    variant="subtle"
    class="drac-box drac-card drac-card-subtle drac-border-cyan drac-p-md drac-m-md"
  >
    <span class="drac-text drac-line-height drac-text-red">Info: <br> Helpers are used like environment variables and their state can be read or modified by an automation. Some examples include: Input Boolean, Input Datetime, Input Number, Input Select, Input Text and Counter. For detailed info, please review - <a href="https://www.home-assistant.io/integrations/#automation">Home Assistant Docs</a></span>
    
  </div>
</div>
<br>

Let's start by opening up the menu to create helpers:
[![Open your Home Assistant instance and show your helper entities.](https://my.home-assistant.io/badges/helpers.svg)](https://my.home-assistant.io/redirect/helpers/)

To create a helper, select *Add helper* at the bottom right of the page.

## Helper 1 - Counter - Dog Meal Counter
The first helper that we create will be a counter. It will be used to keep track of the number of times that we fed our dog and will reset at midnight. It needs to have a maximum value of **2** (or more depending on Fido) and a step size of  **1**.

<img width="50% + 1vw" src="/assets/images/posts/did-you-feed-the-dog/counter.png"/>

## Helper 2 & 3 - Input Boolean - Dog Breakfast & Dog Dinner
The next two helpers that we need are toggles. They will be used to keep track of which meals our dog has ate. I have named one **Dog Breakfast** and the other **Dog Dinner** (*how creative, ik!*).

<img width="50% + 1vw" src="/assets/images/posts/did-you-feed-the-dog/toggle.png"/>

<h2 id="setting-up-the-tag" class="drac-heading drac-heading-xl drac-text-orange">Setting up the Tag</h2>
To create a tag, open the configuration menu and select tags:
[![Open your Home Assistant instance and show your tags.](https://my.home-assistant.io/badges/tags.svg)](https://my.home-assistant.io/redirect/tags/)

Next, click **Add tag** at the bottom right of the page.

## Tag 1 - Counter - Dog Meal Counter
<img  width="50% + 1vw" src="/assets/images/posts/did-you-feed-the-dog/tag.png"/>

### From your phone:
Go ahead and grab the tag that you plan on using, name it, and select **create and write**. Finally, to write the information to the tag, simply place your phone on top of it.

<h2 class="drac-heading drac-heading-xl drac-text-orange">Creating the Automations</h2>
I am going to break up each automation into a set of triggers, conditions and actions.
* **Trigger:** Define what will set the rule off
* **Condition:** Define what needs to be in place in order for the action to complete
* **Action:** A list of things the rule will actually do

Let's start by opening up the menu to create automations:
[![Open your Home Assistant instance and show your automations.](https://my.home-assistant.io/badges/automations.svg)](https://my.home-assistant.io/redirect/automations/)

To create an automation, select *Add automation* at the bottom right of the page.

## Automation 1 - Counter - Dog Meal Counter

### GUI
(If you prefer to use YAML, skip to [code](#code-1))

#### Triggers
We will need to select the tag we setup [in the previous step](#setting-up-the-tag). Anytime this tag is scanned, it will kick off the automation.

<img src="/assets/images/posts/did-you-feed-the-dog/trigger1.png"/>

#### Conditions
Since the dog only eats two meals, we will set a *numeric state* condition on our counter to **below 2**. This will prevent the automation from running if the dog has already eaten twice.

<img width="50% + 1vw" src="/assets/images/posts/did-you-feed-the-dog/condition1.png"/>

#### Actions
For the first action we want the automation to increment the counter (add one to it). This can be done with the *call service* action type and selecting the **counter.increment** service. Finally, we will want to make sure we target the meal counter.

<img width="50% + 1vw" src="/assets/images/posts/did-you-feed-the-dog/action1.png"/>

Next, we will use a *choose* action, which acts like a simple if/else statement. If the counter's value is 1, turn on **Dog Breakfast**, otherwise (else) turn on **Dog Dinner**.

<img width="50% + 1vw" src="/assets/images/posts/did-you-feed-the-dog/action1-2.png"/>

### Code 1
Here's the full code of what we just built:
<script src="https://gist.github.com/ursaMaj0r/dcf5c623bbd9860f9182fe26bebcff6f.js"></script>

Thats it! Now we can move onto the last automation.

## Automation 2 - Counter - Dog Meal Counter Reset

### GUI
(If you prefer to use YAML, skip to [code](#code-2))

#### Triggers
The trigger for this automation will be of the type *time* and will be set to **00:00:00**. This means the automation will start everyday at midnight.

<img width="50% + 1vw" src="/assets/images/posts/did-you-feed-the-dog/trigger2.png"/>

#### Conditions
We don't need to set anything here!

#### Actions

We will need to do three things:
* Reset the counter
* Turn off Dog Breakfast
* Turn off Dog Dinner

In order to reset the counter we will need to call the **counter.reset** service with the meal counter as the selected target. Finally, in order to turn off the meal switches, you will need to call the **input_boolean.turn_off** service targeted at each switch respectively.

<img width="50% + 1vw" src="/assets/images/posts/did-you-feed-the-dog/action2.png"/>

### Code 2
Voilà, we have working code!

<script src="https://gist.github.com/ursaMaj0r/cfd12763872bd591322f298e39f4ddec.js"></script>

<h2 class="drac-heading drac-text-center drac-heading-xl drac-text-orange">Wrapping up</h2>

<p class="drac-text drac-text-center">To finish this one off, I created a card on the dashboard so the info is always visible!</p>

<img  width="50% + 1vw" style="display: block;margin-left: auto;margin-right: auto;" src="/assets/images/posts/did-you-feed-the-dog/card.png"/>

<script src="https://gist.github.com/ursaMaj0r/bf31b13f0b8ab4dd9d0b1b87e12a90aa.js"></script>

