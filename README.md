# googlefitbit
Google scripts for Fitbit step data download (other data may work with modification, but this script does not intend to support other endpoints at this time. You are welcome to submit PRs to add additional functionality. There is some code for other loggables in there but that was copied over from an older version of the script.)

http://sbromberg.com

If you run into trouble, please double check the steps **carefully** and check the error log in the script editor before raising issues or seeking help.

### Note Oct 21 2015
Fitbit has made some changes to its API requiring some minor changes to the scripts. The scripts have been updated already, but if you have a previously authorized script that stopped working, you will need to reset the authorization from the Fitbit menu, and re-authorize with the new permissions selected.

If you run into trouble, please doublecheck the steps **carefully** and check the error log in the script editor before raising issues or seeking help.

### Note Oct 21 2015
Fitbit has made some changes to its API requiring some minor changes to the scripts. The scripts have been updated already, but if you have a previously authorized script that stopped working, you will need to reset the authorization from the Fitbit menu, and re-authorize with the new permissions selected.

>>>>>>> origin/master
## interday.gs
Download step data, one row per day, from a start day to the present. Make sure not to set the start day too far in the past.

VERSION 0.1.1, July 19 2015
You are free to use, modify, copy any of the code in this script for your own purposes, as long as it's not for evil
If you do anything cool with it, let me know!
Note: there are minor improvements/cleanups still to be made in this file, but it should work as is if everything is setup properly

Parts of this script are based on work from the following sources.  The main difference in my version is that I use Fitbit's new OAuth2 and Google's OAuth2 instead of OAuthConfig which is deprecated.

- Original script by loghound@gmail.com
- Original instructional video by Ernesto Ramirez at http://vimeo.com/26338767
- Modifications by Mark Leavitt (PDX Quantified Self organizer) www.markleavitt.com
- https://github.com/qslabs/FitbitDailyData

### Setting up:
1. Create a **new** Google spreadsheet (do not try to re-use one with the old version of the script), click Tools > Script editor... then copy and paste the contents of the interday.gs file (see above) into the script editor. Return to the spreadsheet and refresh the page. A couple seconds after the page reloads you should see a "Fitbit" menu at the top.

2. Add the Oauth2 Google Script library to your project by clicking Resources > Libraries... (menus inside the script editor). Then search for the OAuth2.0 library by typing in the project key "MswhXl8fVhTFUH_Q3UOJbXvxhMjh3Sh48" and hitting Select. More info on that library on [its Github page](https://github.com/googlesamples/apps-script-oauth2 "apps-script-oauth2"). Select the latest version and click Save.

3. Find your project's key by (inside script editor) going to File > Project Properties. Copy the long string next to Project Key in the popup. You will need that in subsequent steps. 

4. Go to https://dev.fitbit.com/apps and log in. If you haven't already, register a new app by clicking at the top right. (For OAuth1.0 application type you want Browser, and OAuth2.0 Server. Default access type Read only. Other fields at top you can put whatever you want, just need to put something.)
Open up the Application Settings for your app and add the following inside the callback url box: 

     https://script.google.com/macros/d/{PROJECT_KEY}/usercallback
     
     (Make sure to replace {PROJECT_KEY} with the project key you got from step 3).
Note your OAuth 2.0 Client ID and your Client (Consumer) Secret.

5. Inside your spreadsheet, refresh if the Fitbit menu isn't visible at the top. Then hit "Setup" and a popup will show. Copy in the OAuth 2.0 Client ID, Secret, and project key, choose loggables, and a start date (if you try to go more than a couple years back, Fitbit's API will not like you). Hit Save.

6. In your spreadsheet, click Authorize from the Fitbit menu and a sidebar will show up. Click the link in the sidebar, log in to Fitbit in the new window, authorize the application, and then close the tab when it says "Success you can close this tab"

7. Back in your spreadhseet, hit Sync, and after a few moments the data should load in.

If I forgot something, or it doesn't work, please let me know.

## intraday.gs
Similar setup to interday, except _**you need to [contact Fitbit](mailto:api@fitbit.com "email Fitbit")**_ to request access to intraday data. Also the  menu names in the spreadsheet will be slightly different.

Based on this post, http://quantifiedself.com/2014/09/download-minute-fitbit-data/

Note: if you want to get heart rate data follow these additional steps, courtesy of [gthm on the Fitbit forum](https://community.fitbit.com/t5/Web-API/Google-apps-script-for-minute-by-minute-data-stopped-working/m-p/890582/highlight/true#M2685 "Fitbit Forum")

1) When requisting Oauth2 the default permission scope does not include heart rate. It pretty much includes everything but heart rate and calories. So for better measure I explicitly granted scope.
 
In function getFitbitService() { // updated the below line
.setScope('activity heartrate location nutrition profile settings sleep social weight')
 
2) Update the activities and intradays variables accordingly
 
var activities = ["activities/heart"];
var intradays = ["activities-heart-intraday"];
 
3) Request the proper URL for heart rate. The API docs are not clear enought, especially near the URL templates. I got the last template of the API docs working for heart rate.
 
It was easier for me to hardcode the URL so I just replaced the featch call with this
 
var result = UrlFetchApp.fetch("https://api.fitbit.com/1/user/-/activities/heart/date/2015-07-07/1d/1sec/time/00:00/23:59.json", options);
 
Once you get this request to work, you can generalize and construct the above request dynamically based on user inputs.
 
PS: I could not find a way to download intra day heart for multiple days through single call. Looks like the call only supports for a single day (too much data to include multiple days I guess). I am thinking about looping the date range and fetching the details multiple times.

I have also uploaded heartrate.gs which is a rough version of a script to download heart rate data.
