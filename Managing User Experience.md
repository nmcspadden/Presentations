# User Experiences & Expectations  

### Managing The User Experience As An Admin



#### Nick McSpadden
#### Schools of the Sacred Heart
      
---
### What do users want to do with their devices?

![inline](http://dilbert.com/dyn/str_strip/000000000/00000000/0000000/000000/20000/3000/700/23794/23794.strip.zoom.gif)

^ They want easy, they want obvious. They want to sit down and go without any obstacles. When there are complicated choices or unnecessary delays, that takes time away from doing things they want.

---

### What users think they're doing:

![inline](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/users_cash.jpg)

^ As admins, our users are usually fellow employees that make our companies succeed. It's important to remember that they may not really care about technology at all other than that it's a tool for doing their job. Remember that someone else evaluated them and determined they were a useful asset to the company, and even though we may not always be on the same wavelength when it comes to using the provided tools, they have jobs that they are good at and we admins must respect them, always.

---

### What admins think users are doing:

![inline](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/illegal_site.gif) ![inline](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/mantis_lololol.gif)

---

### What admins think admins are doing:

![inline](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/startrek.jpg)

---

### Users want things to be easy and straightforward.

![inline](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/Staples_easy.jpg)

---
### How To Annoy Users:
* Present mysterious or ambiguous messages  
![100%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/Annoy1.gif)
* Demand or prompt for information they don't have
![100%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/Annoy2.png)

^ We're all Mac admins. We know how obnoxious it is when software doesn't behave. We know how frustrating it is when things don't just work. If it's annoying for us, as technology specialists, imagine how someone less proficient feels.

---
### How To Annoy Users:
* Make users guess an answer they don't understand
![100%](http://www.barebones.com/images/support/Mavericks-xcrun_dialog.png)
* Deny access because of policy, without explanation
![100%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/Annoy4.jpeg)

^ Every time we have to update an Adobe product, we tend to throw things, use harsh language, rub cats the wrong way, leave the fridge door open... 
^ So how does a user feel when they see messages like these?

---
![200%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/sad_user.jpg)

---
![150%, left](http://static.comicvine.com/uploads/original/11112/111123482/3587232-0225522493-compu.jpg)
###The more times a user has to ask for help, they more they'll resent asking for help.

### And it's always your fault, and your department's fault.

^ It doesn't matter if the user did something you didn't expect.  It doesn't matter if the user's machine got into a weird state, or found an uncommon issue.  It's a problem, and as the IT admin, it's your job to solve it. Otherwise, it's your problem it never got solved.

---
## Give your users more control over their own destiny - or even just the illusion of more control.

^ Let's look at some detailed examples.  Not all of these will apply to every environment, and some of these things may be out of your control - but these are ideas for user empowerment.

---
*Give users the ability to do stuff on their own*

* Let users pick and choose what productivity software they want

![inline 45%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/JAMFSelfService.png)  ![inline 45%](http://wiki.munki.googlecode.com/git/images/managed_software_center.png)

^ Modeled after the successful App Store (iOS).  Document the process for adding software to your central repository

---
*Give users the ability to do stuff on their own*

* Let them pick and choose available printers

![inline](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/msc_printers.png)

---
```bash
#!/bin/sh
printername="business_manager".sacredsf.org
location="Business Manager"
gui_display_name=$location" 4650"
address=$printername
driver_ppd="/Library/Printers/PPDs/Contents/Resources/hp color LaserJet 4650.gz"
option_1="HPOption_Duplexer=False"
currentVersion="3.0"

if [ -e /private/etc/cups/deployment/receipts/$printername.plist ]; then
	storedVersion=`/usr/libexec/PlistBuddy -c "Print :version" /private/etc/cups/deployment/receipts/$printername.plist`
	echo "Stored version: $storedVersion"
else
	storedVersion="0"
fi

versionComparison=`echo "$storedVersion < $currentVersion" | bc -l`

/usr/bin/lpstat -p $printername
if [ $? -eq 0 ]; then
	if [ $versionComparison == 0 ]; then
		exit 1
	fi
    /usr/sbin/lpadmin -x $printername
fi

/usr/sbin/lpadmin -p "$printername" -L "$location" -D "$gui_display_name" \
        -v lpd://"${address}" -P "$driver_ppd" -o "$option_1" -o printer-is-shared=false \
        -o printer-error-policy=abort-job -E

/usr/sbin/cupsenable $(lpstat -p | grep -w "printer" | awk '{print$2}')

mkdir -p /private/etc/cups/deployment/receipts
/usr/libexec/PlistBuddy -c "Add :version string" /private/etc/cups/deployment/receipts/$printername.plist
/usr/libexec/PlistBuddy -c "Set :version $currentVersion" /private/etc/cups/deployment/receipts/$printername.plist

chown -R root:_lp /private/etc/cups/deployment
chmod -R 700 /private/etc/cups/deployment

exit 0
```
[Managing Printers with Munki](https://code.google.com/p/munki/wiki/ManagingPrintersWithMunki#Alternate_Method_Using_nopkg)

^ This is a script I use with Munki to add network printers. We have tons of printers, and 9 buildings, so giving users the ability to choose their devices on their own using Munki's optional installs makes it easier for me as an admin. Everybody just gets the same blanket setup and they decide for themselves what to do with it.

---
*Give users the ability to do stuff on their own*

* Offer multiple browers, with warnings/notices about compatibility with internal sites

![inline, 120%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/browsers.png)
![inline](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/browser_desc.png.png)

^ There's a fine line between giving the users good options, and giving them too many options. The best choices are the ones that are controlled - we only provide users with good outcomes. I don't particularly care what browser a user uses, as long as we have support or policy to handle it.

---
**Don't mandate unnecessary controls on trivial things.**
Don't force users to:

* Use a specific background.
* Use a specific account picture.
* Use your own personal Finder preferences.
* Use a pre-made Dock that can't be changed.
* Manually back up data.
* Wear stripes with plaid.

![](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/rage.jpg)

^ Anybody who has ever used a Mac at home has learned to have certain expectations for the Apple experience.  They expect to change the background, the Dock, customize their little interface quirks they way they like, choose fun pictures, and so forth. The more of those little choices we take away, the less connected users will feel to their technology.

---
###"Why can't I set my background to something other than this?"

![](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/NaturePatterns08.jpg)

---
###"It's IT policy."

---
###"That's a stupid policy. Who can I talk to about changing it?"

---
###"You'll have to go talk to my boss."

---
![90%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/DilbertManager1.jpg)

---
![120%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/DilbertManager1.jpg)

---
![170%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/DilbertManager1.jpg)

---
![original 240%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/DilbertManager2.png)

^ And you know what the outcome of this conversation is?

---
![200%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/sad_user.jpg)

^ This is what happens when you force unnecessarily restrictive policies on users.

---
###Giving users more control isn't the only way to improve the experience.

###We also need to remove unnecessary obstacles, and avoid asking the user to make decisions.

---

* Manage Apple software updates:
```softwareupdate --schedule off```
* Manage third-party software updates. If possible, do them in the background, silently, and unobtrusively. 
* If you can't do them silently in the background in a reasonable way, schedule updates for a consistent time slot and communicate this to users.
* Make heavy use of profiles to manage settings for the OS, applications, and settings.

---

### What admins see when Software Update appears on a user's screen:

![inline](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/softwareupdate.png)

---

### What users see when Software Update appears:

![inline](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/trololol_softwareupdate.png)

---
Remove first run dialogues from Apple apps:

* [Keynote](https://github.com/nmcspadden/Profiles/blob/master/Keynote.mobileconfig):
![inline 150%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/keynote_payload.png)
* [Pages](https://github.com/nmcspadden/Profiles/blob/master/Pages.mobileconfig) and [Numbers](https://github.com/nmcspadden/Profiles/blob/master/Numbers.mobileconfig):
![inline 190%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/pages_payload.png) 

---
Remove first run dialogues from Apple apps:

* [iMovie](https://github.com/nmcspadden/Profiles/blob/master/iMovie.mobileconfig):
![inline 150%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/imovie_payload.png)
* [iPhoto](https://github.com/nmcspadden/Profiles/blob/master/iPhoto.mobileconfig):
![inline 150%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/iphoto_payload.png)

---
Remove first run dialogues from Apple apps:

* [GarageBand](https://github.com/nmcspadden/Profiles/blob/master/GarageBand.mobileconfig):
![inline 150%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/garageband_payload.png)

Remove the new account [setup assistant](https://github.com/nmcspadden/Profiles/blob/master/SetupAssistant.mobileconfig):
![inline 140%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/setupassistant_payload.png)

---

* Remove [menu extras](https://github.com/nmcspadden/Profiles/blob/master/MenuExtra.mobileconfig) you don't expect to use:
![inline 200%](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/menuextras_payload.png)

---
` `
###Depending on their job, users tend to spend a great deal of time in a web browser window.
   

![inline](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/browser_tabs.png)   
###Removing barriers to opening up a web browser and getting access to content will go a long way towards improving the user experience.


---
Common browser improvements:

* Remove all first run dialogs (Firefox & Chrome, I'm looking at you)
* Turn off automatic updates, manage them yourself
* Test updates / versions for compatibility with critical intranet sites
* Don't manage bookmarks. Instead, provide a single site users can go to find content they need.
* If you want users to gravitate towards a specific browser, consider making it the default.

---
###Firefox:

Use Mike Kaply's [Client Customization Kit 2](https://addons.mozilla.org/en-US/firefox/addon/cck2wizard/?src=hp-dl-upandcoming) to remove all the annoying Firefoxisms.

![inline fit](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/firefox.png)

---
###Safari:

Simple to manage with a [profile](https://github.com/nmcspadden/Profiles/blob/master/Safari.mobileconfig).
Avoid over-managing, as users rely on this to sync with iOS devices.

![inline fit](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/safari.png)

---
###Chrome:

There's a [theme](https://github.com/nmcspadden/Profiles/blob/master/Chrome.mobileconfig) here:

![inline fit](https://dl.dropboxusercontent.com/u/56422040/MacTech%20LA%202014/chrome.png)

Sadly, there's more work involved in [removing the first run dialog](https://github.com/nmcspadden/Google-Chrome-Configurations).

---
## How do I figure out what settings to manage for other software?

* Use a VM with snapshots to be able to easily test the same procedure over and over.
* Use a tool like [fseventer](http://www.fernlightning.com/doku.php?id=software:fseventer:start), [opensnoop](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/opensnoop.1m.html), or [fs_usage](http://www.macobserver.com/tmo/article/os_x_using_fs_usage_as_a_troubleshooting_tool) to figure out what files are created / modified when the program launches the first time.
* If it's a plist, use `defaults read` to see what keys exist. Use the `defaults` command to try making modifications to see what happens to the software.

---
## How do I deploy these settings?



* If it's a plist, use Tim Sutton's [Make-Profile-Pkg](https://github.com/timsutton/make-profile-pkg) to create a profile that can be easily deployed in Apple pkg format.
* If it's not a plist, collect the preference files and create your own deployable package out of them.
* Alternatively, use Profile Manager on OS X Server to upload a plist file to create a profile.

---
###To summarize: 

### We admins have a lot of power over the user experience.

### Use it wisely! 