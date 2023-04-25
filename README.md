<img src="https://devblogs.microsoft.com/microsoft365dev/wp-content/uploads/sites/73/2021/08/teams.svg" width="100" height="100">

osTicket Teams User Notifier
==============
An plugin for [osTicket](https://osticket.com) which posts ticket creation and update notifications to users in a [Microsoft Teams](https://products.office.com/en-us/microsoft-teams/group-chat-software) channel. 

Will also mention users if enabled!

Originally forked from [Data Tech's Awesome plugin](https://github.com/Data-Tech-International/osTicket-Microsoft-Teams-plugin).

Info
------
This plugin uses CURL and was designed/tested with osTicket-1.10.1 and updated for use with osTicket-1.17.2
**Note** This has also been updated to fix the first message being repeated for each notification. It should now show new messages as they come in.

## Requirements
- php_curl
- A Office 365 account
- If using mentioning, you will need a Webhook forwarding service.
- **User's emails MUST be their MicrosoftAD emails for mentioning to work.** Use [this](https://github.com/cbasolutions/osTicket-Plugins/tree/master/auth-openid-MS) plugin if you want this to be seamless and foolproof.

## Install
--------
1. Clone this repo or download the zip file and place the contents into your `include/plugins` folder.
2. Now the plugin needs to be enabled & configured, so login to osTicket, select "Admin Panel" then "Manage -> Plugins" you should be seeing the list of currently installed plugins.
3. Click on `MS Teams Notifier` and paste your Teams Endpoint/Webhook Relay URL into the box (MS Teams and Relay setup instructions below). 
5. Click `Save Changes`! (If you get an error about curl, you will need to install the Curl module for PHP). 
6. After that, go back to the list of plugins and tick the checkbox next to "MS Teams Notifier" and select the "Enable" button.

## MS Teams Setup:
- Open MS Teams, navigate to channel and open COnnectors from elipsoids (...) menu
- Select Incoming Webhook and configure
- Choose webhook name and optionally change associated image
- Click Create
- Scroll down and copy the Webhook URL entirely.
- paste this into the `osTicket -> Admin -> Plugin -> Teams` config admin screen. **IF USING MENTIONING** skip this step, but keep the webhook URL handy.

The channel you select will receive an event notice, like:
```
GraphicHealer has set up a connection to Incoming Webhook so group members will be notified for this configuration with name osTicket
```

## User Mentioning Setup:
The way PHP handles HTML tags in json breaks mentioning in teams. In order for it to work, you must use some sort of translation method. I use a webhook forwarder, Specifically this one: [https://webhookrelay.com/](https://webhookrelay.com/)

1. Go to the `osTicket -> Admin -> Plugin -> Teams` config admin screen, and enable mentioning.
2. Go to [https://webhookrelay.com/](https://webhookrelay.com/) and create a free account. You only get one bucket, but that is all you need.
3. Create a forwarding bucket. The output webhook URL needs to be set to the Teams webhook that you copied earlier.
4. Copy the incoming webhook URL from WebhookRelay and paste that into the `osTicket -> Admin -> Plugin -> Teams` config admin screen.
5. Use the following function in WebhookRelay to properly modify the Mentioning values:
    ```
    local out = string.gsub(r.RequestBody, "##user.mention##", "<at>user.mention</at>")
    
    r:SetRequestBody(out)
    ```
If you are using a different webhook service, the basic idea of what you are doing is replacing `##user.mention##` with `<at>user.mention</at>`. As long as you do that, the message will go through.

## Test!
Create or update a ticket!

Notes and System messages shouldn't appear, and the button at the bottom
is the link to the ticket on the user side of osTicket.
The avatar will also update based on what is set in osTicket.
