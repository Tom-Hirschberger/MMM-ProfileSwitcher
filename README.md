<!-- All the links I use multiple times in this readme file, this way I won't have to copy paste so often -->
[MMM-Facial-Recognition]: https://github.com/paviro/MMM-Facial-Recognition
[MMM-ProfileSwitcher]: (https://github.com/tosti007/MMM-ProfileSwitcher)

# MMM-ProfileSwitcher

This an extension for the [MagicMirror²](https://magicmirror.builders/).
This Module adds the ability to have different layouts for different profiles.

Special thanks goes to [Paviro](https://github.com/paviro) as this module's classes idea is based on his [MMM-Facial-Recognition] module's classes idea.

## Installation

In your terminal, go to your MagicMirror's Module folder:
````
cd ~/MagicMirror/modules
````

Clone this repository:
````
git clone https://github.com/tosti007/MMM-ProfileSwitcher.git
````

## Using the module

To use this module, add it to the modules array in the `config/config.js` file:
````javascript
modules: [
    {
        module: 'MMM-ProfileSwitcher',
        config: {
            // See 'Configuration options' for more information.
        }
    }
]
````

For this module to have an effect you have to assign custom classes to your modules. The class `nobody` (if you don't change it) is shown on startup or when switched to. The class `everyone` (if you don't change it) is shown for all profiles, except for the `nobody` profile (can be configured). To specify if a module should be for a certain profile add this profile to the classes data. For multiple profiles separate the names with a space. **Note:** the class names are case sensitive.

This can be done like so:
````javascript
{
    module: 'example_module',
    // Set your classes here separated by a space.
    // Shown for all profiles and for the default profile
    classes: 'nobody everyone'
},
{
    module: 'example_module2',
    // Only shown for me
    classes: 'Brian'
}
````

## Configuration options

The following properties can be configured:

| Option                     | Description
| -------------------------- | -----------
| `nobodyClass`              | The name of the class which should be shown on startup and when there is no current profile. <br><br> **Possible values:** `string` <br> **Default value:** `"nobody"`
| `everyoneClass`            | The name of the class which should be shown for every profile. <br><br> **Possible values:** `string` <br> **Default value:** `"everyone"`
| `includeEveryoneToDefault` | Determines if the default class includes the classes that everyone has. <br><br> **Possible values:** `true` or `false` <br> **Default value:** `false`
| `alwaysShowLeave`          | Determines if a leaveMessage should be shown when switching between two custom profiles (excluding nobodyClass). <br><br> **Possible values:** `true` or `false` <br> **Default value:** `false`
| `animationDuration`        | The duration (in milliseconds) of the show and hide animation. <br><br> **Possible values:** `int` <br> **Default value:** `1000`
| `ignoreModules`            | The module names and classes to ignore when switching profiles. Can be one string with multiple classes splitted with spaces or a string array.<br><br> **Note:** It's wise to add the two default values to the ignoreModules array, else you won't be able to view incomming alerts/notifications and updates. `alert` can be omitted if you want different profiles to have different notifications. <br> **Possible values:** `string` or `string array` <br> **Default value:** `["alert", "updatenotification"]`
| `title`                    | Determines if the title in the notifications should be present. If this value is set to a string it will replace the default title.<br><br> **Possible values:** `true`, `false` or `string` <br> **Default value:** `true`
| `enterMessages`            | The notification message that will be shown when we switch to a different profile. See [Changing Profile Messages](#Changing-Profile-Messages) for more information. <br><br> **Possible values:** `Dictionary of profiles` or `false` <br> **Default value:** `{}`
| `leaveMessages`            | The notification message that will be shown when we switch to the `nobodyClass`. See [Changing Profile Messages](#Changing-Profile-Messages) for more information. <br><br> **Possible values:** `Dictionary of profiles` or `false` <br> **Default value:** `{}`


## Changing Profile Messages
We can set a custom message for each of the profiles in a number of ways. This is done by setting a value for the `enterMessages` or `leaveMessages` config. In these custom messages the substring `%profile%` will be replaced with the current profile.

The enter messages will be shown upon changing to a different profile. This can either be from `nobodyClass` or from a custom profile.
The leave message will be shown when we change profile to the `nobodyClass` or a custom profile if `alwaysShowLeave` is `true`. Here `%profile%` will be the profile that is leaving.

#### Disabling Messages
In order to disable a leave or enter message entirely you can, instead of using a dictionary, set this value to false. Example:
````javascript
config: {
    // Disable the leave messages
    leaveMessages: false
}
````
If you only want to disable a single profile you will have to put it in a dictionary. Example:

````javascript
config: {
    // Disable the leave messages for me
    leaveMessages: {
        "Brian": false
    }
}
````

#### Setting Message For A Single Profile
Setting a custom message for a single profile can be done like so:
````javascript
config: {
    // Customize the enter messages for me
    enterMessages: {
        "Brian": "You again?!?!"
    }
}
````

#### Changing Messages For Multiple profiles
Setting or disabling the message for multiple profiles can be done in the same way with the profile names separated with a space. Example:
````javascript
config: {
    // Customize the enter messages for me and Kevin
    enterMessages: {
        "Brian Kevin": "What's up %profile%?" // %profile% will be replaced with the correct name
    }
}
````

#### Changing Messages For Everyone
To change the message for everyone you will have to do the same thing, but as key use the value of `everyoneClass`. To use the default message for a single or multiple profiles, set the value to `true`. **Note:** Using `true` for everyone is the same as not assigning it. Example:

````javascript
config: {
    everyoneClass: "everyone" //same as default, for illustration only
    // Disable the enter messages for everyone but me and Kevin
    enterMessages: {
        "Brian": true, // I have the default message
        "Kevin": "Hello :D", // Kevin has a custom message
        "everyone": false
    },
    // Everyone has a custom message
    leaveMessages: {
        "everyone": "Hey %person%, already leaving?" // %profile% will be replaced with the correct name
    }
}
````

## Switching Profiles
Switching Profiles can be done by sending a notification with the payload being the desired profile.
Like so (replace `'DESIRED_PROFILE_NAME_HERE'` with your profile name):
````javascript
this.sendNotification('CURRENT_PROFILE', 'DESIRED_PROFILE_NAME_HERE');
````

## Using With Other Modules
Since this module uses notifications, as described in [Switching profiles](#Switching-Profiles), it can easily be used in conjunction with other modules.

### [MMM-ModuleScheduler](https://github.com/ianperrin/MMM-ModuleScheduler/) by Ian Perrin
You can switch to a profile on a certain given time by scheduling a notification in the MMM-ModulesScheduler's config. For example like so:
````javascript
{
    module: 'MMM-ModuleScheduler',
    config: {
        notification_schedule: [
            // SWITCH TO THE DAY PROFILE AT 07:30 EVERY DAY
            {notification: 'CURRENT_PROFILE', schedule: '30 7 * * *', payload: 'Day'},
            // SWITCH TO THE NIGHT PROFILE AT 23:30 EVERY DAY
            {notification: 'CURRENT_PROFILE', schedule: '30 23 * * *', payload: 'Night'},
        ]
    }
},
````

### [MMM-Facial-Recognition] by paviro
Using the [MMM-Facial-Recognition] module and [MMM-ProfileSwitcher] together does not work straight out of the box.
In order for [MMM-Facial-Recognition] to use the [MMM-ProfileSwitcher] module we will have to change a few lines of the code in the [MMM-Facial-Recognition] module's javascript file.
This file can be found in (after installing his module):
````
~/MagicMirror/modules/MMM-Facial-Recognition
````
Here we will have to remove the lines. Since his translations are not being used anymore we can also delete these lines (the `getTranslations` function).
Since we will be removing all his code inside the `notificationReceived` function, we might remove that one aswell.

The lines are (all inclusive):
````
32-36, 49-61, 65-77, 97, 104-113 and 38-46 (translations)
````
If you removed the getTranslations method as mentioned above you can also safely delete the translation folder.
````
~/MagicMirror/modules/MMM-Facial-Recognition/translations
````
Lastly you will have to edit two of his calls to the `sendNotification` function. We will have to change `CURRENT_USER` into `CURRENT_PROFILE` and `"None"` into your `nobodyClass`.
At the end the logout and login functions should look like this:
````javascript
// Code from paviro's MMM-Facial-Recognition
login_user: function () {
    this.sendNotification("CURRENT_PROFILE", this.current_user);
},
logout_user: function () {
    this.sendNotification("CURRENT_PROFILE", "nobody"); // nobody is the default nobodyClass
},
````
And then you should be done!


## Current Supported Languages
* English
* German
* Dutch

## Notes
* All the profile names are case sensitive.
* If no class is set then it will never show, unless it is added to the `ignoreModules` array.
* It's wise to add `alert` and `updatenotification` to the ignoreModules array.
* Using `true` for everyone is the same as not assigning it.
* I do not use lockstrings for hiding the layout, this way you can still unhide a module at any given moment.


## The MIT License (MIT)

Copyright (c) 2017 Brian Janssen

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

**THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.**