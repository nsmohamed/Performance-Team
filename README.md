<img width="608" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/bba276d6-429c-4155-99df-3946b9b44863">![Transfer Facebook Leads to Google Sheets with Google Apps Script](https://raw.githubusercontent.com/simmatrix/facebook-leads-google-sheets-integration/master/images/0%20-%20intro.png)

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
- importJSON code: [https://github.com/nsmohamed/ImportJSON/blob/master/ImportJSON.gs](https://github.com/nsmohamed/facebook-leads-google-sheets-integration/blob/master/App%20Scripts%20importJSON.gs)

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

- Then head over to: Facebook Graph API Explorer](https://developers.facebook.com/tools/explorer

Generate User token:
- You'll need the following permissions to be able to retrieve leads:
<img width="289" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/cc3d3342-ca79-4735-b98e-90f36556c173">

-Then choose to "Get user access token": 
<img width="920" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/9c064af7-e019-4c91-9009-31a0716f2079">

- After getting the token:
<img width="364" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/f6a75297-ccb3-4f35-aa25-4614a579268f">

paste your new access token, your appID and your app secret into the following code:

oauth/access_token?grant_type=fb_exchange_token&fb_exchange_token=add_token_here&client_id=add_appID_here&client_secret=add_appSecret_here
<img width="935" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/921e47ad-4a1d-4227-bedf-c56f1a414cb0">

Then press submit, after submitting you'll get:
<img width="340" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/a39873ea-55bf-4b44-b81a-f3887683da02">

The entirety of this string is your long lived token.

#### Step 4: testing our access:

If you already have an existing form with existing leads great!

If not please create a form, create a test lead using this tool: https://developers.facebook.com/tools/lead-ads-testing, then come back to this step.

Then what we will need is:

- Form ID.

After getting the form ID, type the following get: {Form_id}/leads

Then submit, you should get:

<img width="960" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/657fbda0-2b74-413f-8e7f-d57cce8446dd">

If you get this then great! we can now head over to our google sheets!

If not please reach out or try retracing your steps:

- check your permissions.
<img width="83" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/50964ec6-21c9-49e9-bbc8-60e6510994b6">
- Check that you connected your app with the right business account.
- Check that you have the right lead form.
- Check that you subscribed to leadGen on webhook.

  
#### Step 5: Prepare a blank Google Sheets and add importJSON appScript

![step10](https://github.com/simmatrix/facebook-leads-google-sheets-integration/raw/master/images/10%20-%20prepare%20excel%20sheet.png)

Go to app scripts:
<img width="462" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/42dabfd3-8542-489a-9eb3-3dda8d6b292d">

Copy and paste the script in this link: https://github.com/nsmohamed/ImportJSON/blob/master/ImportJSON.gs

<img width="680" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/ce1fc73d-d270-423e-a3d8-19797b5c3f3f">

Then remove the existing empty function:
<img width="943" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/f2003c26-3e9b-4767-b94f-35d72867190e">

Replace it with the new script:
<img width="840" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/9b5ce1df-620d-4d04-9f9f-a1de4ff34b17">

Then save: <img width="619" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/0e943e29-c8a1-4a40-8887-53ee903fe07c">

Try running the code.
It will request your authorization when running:
<img width="321" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/1967c159-8d6c-4587-bb00-1b1f4abeba08">

Review, permissions and sign into your google account:

<img width="488" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/1cbcef02-b37d-4d59-8337-fa3843ff6aaf">

Ignore the following message, press on advanced press on the "Go to Untitled project (unsafe)":
<img width="474" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/792d59e5-e2c7-4c5a-9b4e-2a67d0be24ee">

Then allow:
<img width="422" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/77ced39a-a4ed-47f6-86d1-f87207ae7a43">

The code will give you an error and that's fine, we haven't provided it with the url it will be calling, we'll do this in the next step.


#### Two ways to call the leads into our google sheets:

##### 1 Calling it manually into our google sheets using importJSON:

After saving the script, go back to the google sheets and do the following:

If successful (great job so far super hero, you're soo close!)

call "=importJSON" function, should appear as follows:
<img width="235" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/6c5272a8-4a86-4bf8-9d83-30de71425294">

then paste the following between quotations "https://graph.facebook.com/v19.0/Add_form_id/leads?date_preset=lifetime&limit=1000&access_token=Add_longLivedToken"

**Important:
**
- dont forget to add the quotations ""
- replace the "Add_form_id" with the actual form ID
- replace "Add_longLivedToken" with the long lived token.

You should now get all your leads in a table format in a descending order:
<img width="345" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/6d5cc230-5d8a-441f-ba87-cbc12e13e954">

##### You should use the second way when you want to get regular updates for your leads:

First in your google worksheet, create two sheets:

- name the first one "LeadsSheet", it's very important you name it exactly this.
    - in the first column will contain our form IDs(if we want to get more than one form)
    - in the second colum we should write the name of the sheet we want to export our leads to. (ex. leads_From_formID122)
  
- Create another sheet with the name we typed in the second column(leads_From_formID122)
<img width="313" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/4da68e6a-190e-499e-b0c0-c677a25e65cf">



Head over to the apps script>> script go to the bottom, you'll find the following function importleads():

<img width="684" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/29b56925-e2fe-4e7e-8572-9dcc9ee13470">

in line 460, you should replace "add_longlivedtoken_here" with the long lived access token :

<img width="608" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/1d63a1c5-f7d5-4b6d-95a1-13601f879481">

After adding your token, choose "importLeads" as the function you want to run:
<img width="461" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/52edc10a-24ea-483b-8679-4e92996a477d">

Then press run:
<img width="640" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/8250db27-c176-43cd-a4fd-30d7af0c8ef7">

If there're no errors(hopefully), then you've got only one more step to go!

On the left you'll find a collapsed menu, hover over it, then choose triggers:
<img width="227" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/ea58ecaf-5d62-48b8-a52f-6e16b6f51303">

Add Trigger:
<img width="440" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/22baefb0-1e3b-4cbd-b86d-6b2b82a9df3a">

Choose importLeads as the function to run:
<img width="269" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/164f93cf-9b04-409b-a6d3-7b937eb6b4bf">

Change event source to time-driven:
<img width="231" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/a32108f6-f443-452e-bee7-3b0eed30affd">

choose the time between each trigger, I chose 15 minutes:
<img width="233" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/82c730d4-3273-499e-af5b-2026f48b807c">

Hit save:
<img width="537" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/fca221c5-8bff-44a4-b626-d9e7ceb4c2b0">

Check your trigger for any issues, if it gives you that it completed successfully then you're done!
You can go to Executions to check the success and failure status.
<img width="899" alt="image" src="https://github.com/nsmohamed/facebook-leads-google-sheets-integration/assets/98906258/e7e975f6-1333-4f93-b92a-a78bd295311a">


### Whew, you pulled it off superstar!

Now I'll keep improving on it and I'll be adding any updates here, but for now you will be receiving all your leads into google sheets regularly for free!!

### Massive appreciation for

Massive thanks to the original coders who provided the strong base for this walkthrough:
- https://github.com/simmatrix/facebook-leads-google-sheets-integration#additional
- https://github.com/bradjasper/ImportJSON

### Feedback

If you have any feedback or any suggestions, please reach out, I'd love to improve on this.
