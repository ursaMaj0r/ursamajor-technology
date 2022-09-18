---
layout: post
title: PowerShell Secrets, it's Uber Simple!
date: 2022-09-18 00:00 +0000
id: 00
author: Jeff
image: /assets/images/posts/PSSecrets/security.png
views: 0
categories:
    - PowerShell
    - Cyber
    - Scripts
tags:
    - Automate Everything
---

“Three may keep a secret, if two of them are dead.”  ―Benjamin Franklin

<!--more-->

# Overview
Creating a PowerShell script to automate a task saves us time and helps to reduce repetitive tasks. Scripts often connect multiple resources together, making them an easy pivot point for a bad actor. It may be tempting to include a password, API key, or secret in a PowerShell file, but this can easily turn a good script into a ticking time bomb. Luckily, PowerShell makes it easy to interact with secret vaults or even create one locally on any Windows, Linux or Mac device with the SecretManagement and SecretStore modules.

# Getting Started

### Installing the Modules
In order to setup the modules for the first time, run the following code in an administrative command prompt:

<div>
    <code class="custom-codeblock">Install-Module Microsoft.PowerShell.SecretManagement, Microsoft.PowerShell.SecretStore</code>
</div>

### Create a Vault
Next, we will create a vault to hold our secrets. The easiest way to do this is to use the SecretStore vault, which creates a locally encrypted file. However, the SecretManagement utilities can also be connected to other more common vaults such as KeePass, LastPass, Azure KeyVault and Hashicorp Vault. 

To do this, run the following command:

<div>
    <code class="custom-codeblock">Register-SecretVault -Name KrabbyPattySecretFormula -ModuleName Microsoft.PowerShell.SecretStore -DefaultVault</code>
</div>

### Store a Secret
Now it is time to store our secret in the vault. 

<div>
    <code class="custom-codeblock">Set-Secret -Name Ingredients -Secret "Bun,Sea Cheese, Sea Lettuce, Sea Tomatoes, Pickles, Ketchup, Mustard, Mayonnaise, Sea Onions, Patty, Secret Sauce,Bun"</code>
</div>

Enter and confirm a password to encrypt your vault. Try to use a long passphase and store it in a password manager for safe keeping!

### Retrieve a Secret
After the secret is placed into the vault, we can very easily retrieve it later in a script. Let's list out all the ingredients in the Krabby Patty secret formula:

<div>
    <code class="custom-codeblock">(Get-Secret -Name Ingredients -Vault KrabbyPattySecretFormula -AsPlainText).Split(',')</code>
</div>

<br><img src="/assets/images/posts/PSSecrets/secret-formula.png"  width="40%"/>

# Putting it all Together
In order to unlock the vault we must provide the password interactively. This is obviously not possible during an automated process. The module provices a way to unlock the vault for a one hour period using the below command. It is important to securely store this password using an encrpted file, or ideally an environment variable in a CI/CD pipeline.

<div>
    <code class="custom-codeblock">Unlock-SecretStore -Password $env:vaultPassword</code>
</div>

### Learn More
- [SecretManagement and SecretStore are Generally Available](https://devblogs.microsoft.com/powershell/secretmanagement-and-secretstore-are-generally-available/)
- [Overview of the SecretManagement and SecretStore modules](https://learn.microsoft.com/en-us/powershell/utility-modules/secretmanagement/overview?view=ps-modules)
- [XKCD](https://xkcd.com/538/)