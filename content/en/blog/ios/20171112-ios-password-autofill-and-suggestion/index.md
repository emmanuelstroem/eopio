---
date: 2017-11-12
title: "iOS: Password Autofill and Suggestions"
description: |
  
tags: ["iOS", "password", "autofill", "suggestions"]
author: Emmanuel ([@emmanuelstroem](https://twitter.com/emmanuelstroem))
---

### About Password Autofill
iOS 11+ introduces Password Autofill and Suggestions like Safari accessing them from keychain using ```apple-app-site-association```.

You can now enable your apps to suggest the specific credentials at your login screen to reduce login delays and forgotten or mistyped passwords and usernames.

## Pre-requisites:
- iOS 11+
- Xcode 9
- Paid Apple Developer Account
- Write Access to your domain server e.g eopio.com


## Steps:
### Using Code:
usernameTextField.textContentType = .username
passwordTextField.textContentType = .password

### Using Storyboard:
Open your iOS project in Xcode and:
### Username
Set the Username TextField ```Content Type``` to ```Username``` as shown in the below.
![ios_username_content_type_in_xcode](http://eopio.com/content/images/2017/11/ios_username_content_type_in_xcode.png)

### Password
Set the Password TextField ```Content Type``` to ```Password``` as shown.
![ios_password_content_type_in_xcode](http://eopio.com/content/images/2017/11/ios_password_content_type_in_xcode.png)

### Associated Domain

![enable_associated_domains_in_xcode](http://eopio.com/content/images/2017/11/enable_associated_domains_in_xcode.png)

1. Go to Project Navigator and Select your Project Root. 
2. From the Editing Area, Select ```Capabilities``` tab
3. Scroll down to ```Associated Domains``` and 
4. Toggle the ```ON``` Switch
5. Replace example.com to your auth domain e.g eopio.com - you can find your domain name in keychain access.
6. Double-check the steps here to make sure all is configured correctly. Usually the step ```Add the Associated Domains feature to your App ID.``` is a possible issue here.

Possible Build Error Here (1):
![developer_signing_profile_error_in_xcode](http://eopio.com/content/images/2017/11/developer_signing_profile_error_in_xcode.png)
This means you're Xcode Signing is not using a Paid Apple Developer Account.
_Solution:_ Just use your paid Developer Account for Xcode Code Signing.

Possible Error Here (2):
![error_project_associated_domain_in_xcode](http://eopio.com/content/images/2017/11/error_project_associated_domain_in_xcode.png)
This Error Means that ```Associated Domains``` Feature is Disabled for this ```app id``` in the your Developer Portal
_Suolution:_ Follow the steps below to fix it.

### Adding Associated Domains Feature to App ID.
1. Login to your Apple Developer Portal [https://developer.apple.com/account](https://developer.apple.com/account)
2. From the left panel, Select ```Certificates, IDs & Profiles```

![apple_developer_portal_certificates_and_ids](http://eopio.com/content/images/2017/11/apple_developer_portal_certificates_and_ids.png)

3. Select ```App IDs``` from the Left Panel again. 
4. Select your ```app id``` or ```name``` from the list of registered apps. Xcode automatically registers your apps as soon as you configure Signing.

    **If your ```app id``` is not in the list,**
    1 Check Xcode Signing configuration for your project.
    2 If Signing is correct, then Register your App following these steps [Register App ID on Apple Developer Portal](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingProfiles/MaintainingProfiles.html)

![apple_developer_portal_enable_app_services](http://eopio.com/content/images/2017/11/apple_developer_portal_enable_app_services.png) 

Prefix _(2)_: is the ```Team ID```. Take note for we'll need it later.
ID _(3)_: is the ```app id``` also same as ```Bundle Identifier``` set in Xcode. Also note this one.

4. Read throgh the List of Services currently enabled for your ```app id``` If ```Associated Domains``` _(4)_ is **Disabled**, click **Edit** _(5)_ and Check next to Associated Domains to Enable it.

![enable_disable_associated_domains_developer_portal](http://eopio.com/content/images/2017/11/enable_disable_associated_domains_developer_portal.png)


#### Simple Server Side Configuration
Using text-editor of your preference, 
1. Create a file named ```apple-app-site-association``` with the following JSON content:

```
{
    "webcredentials": {
        "apps": [ "apple_team_id.app_bundle_id" ]
    }
}
```

Replace:
```apple_team_id``` with your Apple Developer Team ID.
```app_bundle_id``` with your App's Bundle ID
You can find your Team ID and App's Bundle ID from the section above on _Adding Associated Domains Feature to App ID_.

2. Copy file ```apple-app-site-association``` to your web server root or to ```.well-known``` directory in your web server root.

3. Edit your web server config to return the JSON content.

For NGINX, use the settings below:
```
    # if file is in webroot
    location /apple-app-site-association {
        default_type application/json;
    }
    
    # if file is in .well-known directory
    location /.well-known/apple-app-site-association {	
       default_type application/json;
    }
```

Restart your webserver. For NGINX on Ubuntu: ``` service nginx restart```

Verify that the JSON content is being served on your domain loading either ```yourdomain/apple-app-site-association``` or ```yourdomain/.well-known/apple-app-site-association``` depending on which directory you added the file _apple-app-site-association_.

Build and Run your application.

![password_autofilled_ios_login_screen](http://eopio.com/content/images/2017/11/password_autofilled_ios_login_screen.png)

Good Luck and Let me know :-D

