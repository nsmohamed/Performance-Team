![Transfer Facebook Leads to Google Sheets with Google Apps Script](https://raw.githubusercontent.com/simmatrix/facebook-leads-google-sheets-integration/master/images/0%20-%20intro.png)

You can find the original walkthrough here: https://github.com/simmatrix/facebook-leads-google-sheets-integration#additional

Th walkthrough was great, but it was a bit outdated, therefore in the following visual walkthrough I'll show you how I transfered Facebook leads to Google Sheets and constantly update the sheet using a trigger in app script.


### Reason for doing so

I'm a media buyer, I create lead ads for my clients regularly. Recently one of our clients has started using Zapier to get FB leads to Google sheets, main issue was the high monthly fees and the limitations.

Therefore I decided to find a way to solve this with efficiently and at little to no cost. 

### Quick introduction

We will be using Google Apps Script to import our leads using a script that I'll link to below, in this walkthrough we're going to:

1. Create a Facebook app
2. Create a Facebook webhook
3. Prepare a blank Google Sheets
4. Connect Webhook to Google apps script
5. Get a Facebook page access token
6. Add importJSON script to Google apps script
7. Add a trigger that reruns the script to get regular updates.
8. Structure our google sheets to prepare for multiple forms.

### Prerequisites

- Be an admin on the FB page (not just the ad account)
- Have a lead form ready, if you already have an existing lead form that worked in the past that will work just fine.

### Useful Tools Being Used

- Facebook Graph API Explorer: https://developers.facebook.com/tools/explorer/
- Facebook Lead Ads Testing Tool: https://developers.facebook.com/tools/lead-ads-testing
- Facebook Access Token Debugger: https://developers.facebook.com/tools/debug/accesstoken
- importJSON code: https://github.com/nsmohamed/ImportJSON/blob/master/ImportJSON.gs

### Additional Useful Reference Materials

- Walkthrough on how to get a long lived token(60 day token): https://www.youtube.com/watch?v=lTJW8o9dFf4&ab_channel=kitchn_io

# Let's begin!

#### Step 1: Create a Facebook App

After first signing up to meta for developers.

Head over to https://developers.facebook.com/apps/ and add a new app.
![step11](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/11%20-%20create%20facebook%20app.png)

Key in a display name for your new app.
![step12](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/12%20-%20create%20facebook%20app.png)

Make sure to choose the business account associated with the page:
<img width="317" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/77b5345f-8b2b-4067-854c-a3ff69022815">


Key in the security check and you are all set with a new Facebook app!
![step13](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/13%20-%20create%20facebook%20app.png)

#### Step 2: Create a Facebook Webhook

Mouse over to the "Webhooks" selection and click on the "Set Up" button
![step14](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/14%20-%20add%20webhook.png)

You will be directed to this page. Now keep this browser tab open. We need to head over to Google Apps Script first.
![step15](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/15%20-%20add%20webhook.png)

In your Google Drive, create a new Google Apps Script
![step16](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/16%20-%20setup%20webhook.png)

This is for Facebook to verify the existence of our webhook. Copy these lines of code into your script panel. You can put any random string as the `hub.verify_token`. I used this free tool: https://www.lastpass.com/features/password-generator to get a 16 letter code (keep this code)

> You can copy the code :
function doGet(request)
{
  if (request.parameter['hub.verify_token'] == 'abcdefghijklmn0123456789') {
    return ContentService.createTextOutput(request.parameter['hub.challenge']);
  }
}


![step17]<img width="798" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/7c28202a-0471-4832-8d79-90d203b411b1">

Then you need to deploy your script to make it live.
![step18]<img width="844" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/16f1fae8-c2cd-42d6-ba99-214a7fc48fbc">

<img width="555" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/97eb4227-2cea-4d94-8ff2-cff2ef5d84ee">

Make sure you are executing your script as `Me (<your_email_address>)` and give `Anyone, even anonymous` the access to your app. And the Project version should be `New`.
![step19]<img width="545" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/55ba179d-435d-4af9-a353-c3990d6fde5e">


After you have made it live, copy your web app URL.
![step20]<img width="587" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/92b08228-157b-473e-ad1d-42d46bed1555">

You may now head back to your Facebook Developers App page.
![step21](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/21%20-%20register%20webhook.png)

Make sure to change the `User` option to `Page` from the drop down list, then proceed to click on the "Subscribe to this object" button.
![step22](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/22%20-%20register%20webhook.png)

Key in the web app URL that you copied from your Google Apps script just now into the Callback URL field, and key is (['hub.verify_token'] == 'abcdefghijklmn0123456789') that you generated and added to the Google apps script. Click `Verify and Save` to finish this off.
![step23](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/23%20-%20register%20webhook.png)

You will now see a list of items below. (You may ignore the warning message as of now, it will go away once you have finished configuring and set your app to live)
![step24](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/24%20-%20make%20subscription.png)

Search for the item `leadgen` and hit on the "Subscribe" button
![step25](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/25%20-%20make%20subscription.png)
![step26](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/26%20-%20make%20subscription.png)



#### Step 3: Get a Facebook long lived page access token

First get the get the app ID and app Secret:
- go to App settings>Basics, click on show beside the app secret to copy it as well as the app ID and save them on a note or text file as we'll need them to generate the long lived token
<img width="714" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/99ff34cc-8c77-45a9-90d5-96de7086a792">

Then head over to: Facebook Graph API Explorer](https://developers.facebook.com/tools/explorer


Generate User token:
- You'll need the following permissions to be able to retrieve leads:
<img width="289" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/cc3d3342-ca79-4735-b98e-90f36556c173">

-Then choose to "Get user access token": 
<img width="920" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/9c064af7-e019-4c91-9009-31a0716f2079">




#### Step 3: Prepare a blank Google Sheets

![step10](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/10%20-%20prepare%20excel%20sheet.png)







### The Forbidden Path

Before proceeding further, I would like to let you know that actually there is a quick way of obtaining a `page access token` but I would not encourage you to do so because the token will expire in about one hour plus. You would need a long-lived token that never expire. Here's a quick way of obtaining a `Page Access Token`, just select your Facebook page from the drop-down list above of the Access Token field, then click `Get Access Token`.

![step27a](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/27a%20-%20shortcut%20of%20getting%20page%20access%20token.png)

You may then click on the blue "info" icon within the Access Token field to view its expiry time. You can see that it will be expiring soon. I created this around 6.45PM and it will expire at 8.00PM.

![step27b](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/27b%20-%20shortcut%20of%20getting%20page%20access%20token.png)

So right now I would take you for a ride of how to obtain a long-lived `page access token`. Here's a big picture:

1. First we would need to get a short-lived `user access token`
2. Then we exchange the short-lived `user access token` with a long-lived one
3. Finally we request for a `page access token` with our long-lived `user access token`

> If our user access token is long-lived, so will our page access token. And vice versa...

### The Right Path

#### 1. Get Short-lived User Access Token

On a fresh page of your Graph API Explorer... (I switched to Facebook's new beta Graph Explorer. If you're still in the classic mode, you can either stay as it is or switch to this new beta and nicer look interface)
![step28](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/28%20-%20get%20short-lived%20user%20access%20token.png)

Make sure you have selected the correct Facebook app at the top-right drop-down list, for mine, it is `Lead Ads Google Sheets Linkage`. After this, from the "Get Token" drop-down list, click on `Get User Access Token`
![step29](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/29%20-%20get%20short-lived%20user%20access%20token.png)

Authorize your Facebook app to read your name and profile picture.

![step29b](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/29b%20-%20get%20short-lived%20user%20access%20token.png)

Now you would need to add permission. Go to the `Add a Permission` selection list, click on the subsection `Events Groups Pages`, then tick on the `manage_pages` checkbox
![step30](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/30%20-%20get%20short-lived%20user%20access%20token.png)

Then click on the blue `Get Access Token` button
![step31](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/31%20-%20get%20short-lived%20user%20access%20token.png)

Continue on with authorizing your Facebook app to read your Facebook page data.
![step32](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/32%20-%20get%20short-lived%20user%20access%20token.png)

Copy the string as shown in the Access Token field. This is your short-lived `user access token`.
![step33](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/33%20-%20get%20short-lived%20user%20access%20token.png)

#### 2. Exchange Your Short-lived User Access Token with a Long-lived one

Open a blank text editor and key in this line of code. Remember to replace the placeholders `INSERT_YOUR_USER_ACCESS_TOKEN`, `INSERT_YOUR_CLIENT_ID`, `INSERT_YOUR_APP_SECRET` with your own data.

```
oauth/access_token?grant_type=fb_exchange_token&fb_exchange_token=INSERT_YOUR_USER_ACCESS_TOKEN&client_id=INSERT_YOUR_CLIENT_ID&client_secret=INSERT_YOUR_APP_SECRET
```

![step34](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/34%20-%20exchange%20for%20long-lived%20user%20access%20token.png)

You can obtain both your App ID and App Secret under `Settings` > `Basic`. Do fill in the rest of the details as well, for example, app icon, category, business use, contact email and privacy policy URL.
![step35](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/35%20-%20exchange%20for%20long-lived%20user%20access%20token.png)

Now you need to copy and paste the text that you have just typed in your text editor into the text field beside of the Submit button, then hit Submit.
![step36](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/36%20-%20exchange%20for%20long-lived%20user%20access%20token%20.png)

The result shown is your long-lived `user access token`.
![step37](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/37%20-%20exchange%20for%20long-lived%20user%20access%20token.png)

#### 3. Get a page access token with your user access token

Key in `me/accounts` to the text field beside the Submit button, and make sure you have pasted your long-lived `user access token` into the Access Token field, then hit the Submit button. The result shown are all of your Facebook pages

> Copy the `access_token` and `id` (Your Facebook Page ID) of your intended Facebook page.

![step39](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/39%20-%20get%20long-lived%20page%20access%20token.png)

Optionally, if you're curious to know whether this `page access token` expires or not, you may head on to [Facebook Access Token Debugger](https://developers.facebook.com/tools/debug/accesstoken) and give it a check! The result is `Never` expires.
![step40](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/40%20-%20double%20confirm%20expiry%20date%20of%20page%20access%20token.png)

#### Step 7: Subscribe your Facebook page to your Facebook webhook

To link together both of your Facebook page and webhook, you would need to make a `POST` request with the endpoint `<YOUR_FACEBOOK_PAGE_ID>/subscribed_apps`. Make sure that it is `POST`, and make sure that you have keyed in your `page access token` to the Access Token field, then hit Submit.
![step41](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/41%20-%20make%20your%20page%20subscribe%20to%20the%20webhook.png)

You should then be getting the following result:

```json
{
  "success": true
}
```

Now you can make a `GET` request with the same endpoint as above to view the list of subscribed apps.
![step42](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/42%20-%20view%20subscribed%20webhook.png)

#### Step 8: Update your webhook script to receive and store your lead data in Google Sheets

Copy the `doPost()` function from [this file](https://github.com/simmatrix/facebook-leads-google-sheets-integration/blob/master/scripts/Code.gs) and paste it into your script panel. Remember to replace the placeholders `INSERT_YOUR_GOOGLE_SHEETS_ID` and `INSERT_YOUR_LONG_LIVED_PAGE_ACCESS_TOKEN` with your own data.

![step43](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/43%20-%20record%20the%20lead%20info.png)

If you're curious of where you can obtain your Google Spreadsheet ID, you can get it from the URL itself.

![extra](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/extra.png)

You can now deploy your web app again.

![step44](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/44%20-%20record%20lead%20info.png)

Make sure you are executing your script as `Me (<your_email_address>)` and give `Anyone, even anonymous` the access to your app. And the Project version should be `New`. If you are prompted with any Authorization pop-up dialog again, just do it like how you have done that before.

![step45](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/45%20-%20record%20lead%20info.png)

Because you have now added the lines of code that will access and write to your Google Sheets, you would need to authorize and grant the necessary permissions for your Google Apps script to write to your Google Sheets on behalf of you.

![step46](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/46%20-%20grant%20permission%20for%20your%20app%20to%20read%20google%20sheets.png)

Sign in with your Google account

![step47](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/47%20-%20granting%20permission.png)

Your browser will prompt a warning that this app is not verified. You may click on the `Advanced` link and proceed. No worries, after all this app is being created by you yourself so it's safe.

![step48](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/48%20-%20granting%20permission.png)

After clicking on the `Advanced` link, you may proceed by clicking the link at the bottom `Go to xxxxx (unsafe)`

![step49](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/49%20-%20granting%20permission.png)

Grant the permission for your Google Apps script to view and manage your spreadsheets in Google Drive.

![step50](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/50%20-%20granting%20permission.png)

Now you would need to head over to the Business Settings page of your Facebook Page > "Leads Access" > "CRMs". Select the `Facebook APP` then click on `Assign access` button.

![step51](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/51%20-%20granting%20access%20to%20your%20app.png)

Then you will see the indicator turns green.

![step52](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/52%20-%20granting%20access%20to%20your%20app.png)

#### Step 9: Test sending a lead

> For this section, you would need to head over to [Facebook Lead Ads Testing Tool](https://developers.facebook.com/tools/lead-ads-testing)
 > ![step53](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/53%20-%20test%20sending%20a%20lead.png)

Hit on the "Create lead" button and wait for a moment, then hit on the button "Track status"
![step54](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/54%20-%20test%20sending%20a%20lead.png)

### TADAA... THE END!

![step61](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/61%20-%20check%20test%20lead.png)

### Additional (Pulling Previous Leads)

So for the above we deal with pulling of Facebook Leads in real-time. How if you just implement it half-way when your campaigns are running? You definitely need to pull all of the other previous leads into your Google Sheets as well right... You can refer to this [alternative script](https://github.com/simmatrix/facebook-leads-google-sheets-integration/blob/master/scripts/CodeAdditional.gs) of which can be called by `<YOUR_GOOGLE_SCRIPTS_WEB_APP_URL>?pull_all_leads=true`. Kindly refer to the `doGet()` function. It handles the pagination as well (as Facebook returns 25 leads per call)

To find the ID of your lead form to be keyed into `var lead_form_id = 'INSERT_YOUR_LEAD_FORM_ID';`, you can go to [Facebook Lead Ads Testing Tool](https://developers.facebook.com/tools/lead-ads-testing) and create a test lead, then look at the `Payload` column at the bottom. `..."form_id":"xxxxxxxxxxx"...`
![step60](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/additional.png)

In this additional script, I have also sorted the data in the order which I have wanted before inserting it into Google Sheets because I realized that for some of my previous leads, sometimes "email" comes first, sometimes "full_name" comes first, so without ordering them in proper format, it would be a mess in Google Sheets later.

### Additional (Saving Leads from Different Forms to Different Sheets)

Sometimes you may have multiple lead forms created for a single Facebook page, if so, you may refer to this [alternative script](https://github.com/simmatrix/facebook-leads-google-sheets-integration/blob/master/scripts/CodeMultipleFormsSheets.gs)

### Additional (Sending Leads to your Email)

If you would like to have the leads be sent to your mailbox instead, you can refer to this [alternative script](https://github.com/simmatrix/facebook-leads-google-sheets-integration/blob/master/scripts/CodeEmailSending.gs). Do remember to replace `"<YOUR_EMAIL_ADDRESS>"` with your own email address.

![sendemail](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/send-email.png)

### Feedback

Should you have any feedback, feel free to send your enquiries via simmatrix100[at]gmail[dot]com.
