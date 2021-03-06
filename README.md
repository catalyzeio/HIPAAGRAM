HIPAAGRAM
=========
HIPAA compliant WhatsApp

Prerequisites
-------------
HIPAAGRAM uses cocoapods to manage the libraries it needs. You will need cocoapods installed to use this app. Check out their [docs](http://cocoapods.org/) or simply run `sudo gem install cocoapods` in a terminal window.

Getting Started
---------------
HIPAAGRAM is an example app written to show the capabilities of the Catalyze HIPAA compliant APIs. An explanation of the following instructions can be found in both the [API Guide](https://docs.catalyze.io/guides/api/latest/) and the [blog post](https://blog.catalyze.io) explaining HIPAAGRAM. In order to run this application you will have to follow these steps

* Head over to the [dashboard](https://dashboard.catalyze.io) and sign up for an account
* Create an Organization
* Create an Application to run this app (call it HIPAAGRAM!)
* Create an API Key
* Insert the API Key (<id>) in the `Constants.h` file
* Insert the application ID from the dashboard in the `Constants.h` file
* If you want to use Amazon SNS for push notifications, follow their documentation and enter the appropriate values in the `Constants.h` file

Now that we have our org and app setup, we need to create a few custom classes and assign appropriate permissions. There are two ways to accomplish this, run a script or do it manually with cURL commands.

Run a Script
------------
From the root of the project you will see a folder called `Setup`. Open up terminal and navigate to this folder

```
cd Setup
```

This is a simple python script that will take care of everything for us, creating the appropriate custom classes and setting the correct read/write permissions on those classes. There is one dependency for this script, the python `Requests` library. To install this run

```
sudo pip install requests
```

Feel free to make this folder a [Virtual Env](https://virtualenv.pypa.io/en/latest/) and run the install that way as well. Now that we have the dependency installed let's run the script!

```
python HipaaGramSetup.py --username MY_EMAIL --password MY_PASSWORD --appId MY_APP_ID --apiKey MY_API_KEY
```

Be sure to replace the values of `MY_EMAIL`, `MY_PASSWORD`, `MY_APP_ID`, and `MY_API_KEY` with your specific values. The script will tell you once everything finishes and you're all set! There is no need to perform the manual commands if you run this script, skip on down to `iOS Project Setup`.

Manual Commands
---------------
Create a custom class named `conversations` with the following schema. Be sure to mark PHI as `true`.

```
{
"name":"conversations",
"schema":{
	"sender":"string",
	"recipient":"string",
	"sender_id":"string",
	"recipient_id":"string",
	"sender_deviceToken": "string",
	"recipient_deviceToken": "string"
	}
}
```

Create a custom class named `contacts` with the following schema. Be sure to mark PHI as `false`.

```
{
"name":"contacts",
"schema":{
	"user_deviceToken": "string",
	"user_username":"string",
	"user_usersId":"string"
	}
}
```

Lastly, create a custom class named `messages` with the following schema. Be sure to mark PHI as `true`.

```
{
"name":"messages",
"schema":{
	"conversationsId":"string",
	"msgContent":"string",
	"toPhone":"string",
	"fromPhone":"string",
	"timestamp":"string",
	"isPhi":"boolean",
	"fileId":"string"
	}
}
```

Now we need to set the appropriate permissions for these three classes. To learn about setting permissions, check out [this guide](https://resources.catalyze.io/baas/guides/permissions-and-acls/#granting-full-permissions-on-a-custom-class). You can run cURL commands or use the REST client of your choice to run these commands. For the `contacts` class we need to assign the following permissions

```
['create', 'retrieve']
```

For the `conversations` class assign the following permissions

```
['create']
```

For the `messages` class assign the following permissions

```
['create']
```

iOS Project Setup
-----------------
There is very little setup for the iOS project itself. Simply run

```
pod install
```

in the root of the project from a terminal window to install all the necessary dependencies. Open up `HipaaGram.xcworkspace` (do not open `HipaaGram.xcodeproj`) and you're all set to go!

Amazon SNS
----------
HIPAAgram uses push notifications to alert recipients that they have a new message. No details about the message are sent in the notification. This is so the app remains HIPAA compliant. The notification is simply an alert to tell the recipient that they should query for new messages. In order to use this feature you will need to sign up for an AWS account and setup Amazon SNS or replace it with your own push notification provider. You will then need to fill in the appropriate keys in the `Constants.h` file.

If you do not wish to use this feature, simply don't fill in any SNS values in the `Constants.h` file. You may see errors in the logs, but it will not degrade the UI of the application.

License
--------

    Copyright 2015 Catalyze, Inc.

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
