# smartthings-iotfuse-workshop

## About

This is the repository for the example SmartThings application we'll build at IoT Fuse 2018. 

We will build a Slack [slash command](https://api.slack.com/slash-commands) to allow you to control your home from Slack:

- `/smartthings turn on the kitchen lights`
- `/smartthings lock the doors`

> NOTE: This application uses a preview of a new beta feature to allow installations via OAuth. As this feature is not generally available it is subject to change.

## Workshop Format

We will build our app incrementally, and use [Git tags](https://git-scm.com/book/en/v2/Git-Basics-Tagging) for the different steps.
This will allow us to start from a common code base for each step.

We've provided a lot of the boilerplate and basic code, including leveraging [Botkit](https://www.botkit.ai), so we can focus on the fun stuff ;)

(Botkit includes a [middleware pipeline](https://github.com/howdyai/botkit/blob/master/docs/readme-middlewares.md) that allows for [plugins](https://botkit.ai/docs/readme-middlewares.html).)


## Step 0 - Bootstrapping

### Install required software and create accounts

This workshop requires the following software installed:

1. [Node.js](https://nodejs.org) and [npm](https://npmjs.com)
2. [ngrok](https://ngrok.com/) installed to create a secure tunnel to create a globally available URL for fast testing.

You also need to have a Samsung account and a Slack account, if you don't already have one:

1.  [Create a SmartThings account](https://account.samsung.com/membership/signUp.do) if you don't already have one.
2. Create a [Slack](https://slack.com/) account if you don't already have one, and create a new workspace for testing purposes.

If you don't already have it, you should also download the SmartThings mobile app, and sign in to it with your Samsung account.

### Get a SmartThings token

To create a SmartThings application, we'll need a SmartThings personal access token. Create a token with all "apps" scope selected, and store the token somewhere so you can access later - it will only be shown once!

Visit the [SmartThings personal access token page](https://account.smartthings.com/tokens) to create a token.

## Step 1 - Run the skeleton app

> NOTE: All commands are executed from the root project directory.

1. Clone this repository

    `git clone https://github.com/jimmyjames/st-iot-fuse.git`

2. Checkout the `step-1` tag

    `git checkout step-1`    

3.  Install dependencies

    ```
    npm i
    ```

4.  Checkout the SlackBot.js file

    ```
    git checkout node_modules/botkit/lib/SlackBot.js
    ```

5.  Copy `.env.example` --> `.env`

6.  Run the app

    ```
    npm start
    ```

7.  Or, Debug the app

    If using Visual Studio Code text editor:

    *   Press F5

8.  Open your browser to http://localhost:3000
9.  Follow the directions to complete setup (we'll do this together)

### Verifying our work so far

Once we have a Slack app installed to our test workspace, we can verify that we can link it to our SmartThings account and execute some commands (they aren't implemented yet, of course):

Type `/smartthings help` for help. Notice it prompts you to authorize, follow the steps to authorize with SmartThings.

Now that we've linked to our SmartThings account, we can try out the commands we'll build next:

- `/smartthings turn off the reading lights`
- `/smartthings turn on the kitchen lights`
- `/smartthings lock the doors`
- `/smartthings unlock the doors`

There are some other utility commands already implemented:

- `/smartthings (uptime|alive|debug)` - health check the app
- `/smartthings help` - basic command help
- `/smartthings (logout|gdpr|forget me)` - delete the SmartThings account details, will force you to reauthorize.

## Step 2 - Get the requested device

Before we can execute a command on a device, we need to get the device itself.
In this step, we'll call the SmartThings API to get the device object for the name the user requested.

Before proceeding, checkout the step-2 tag:

`git checkout step-2`

### Create some test devices

We need some devices to test with - we'll create some "simulated" devices for this purpose. 
We will create three devices - one light and two locks. 

We'll do this together so you can follow along, but the steps are:

1. Go to https://graph.api.smartthings.com and login if necessary.
2. Click the "My Locations" link, then click your Location or create a new Location if you don't have one yet.
3. Click the "My Devices" link.
4. Click the "New Device" button.
5. Enter something meaningful for the Name (e.g., "Kitchen lights", "Front door", "Back door").
6. Enter random characters in the "Device Network Id" field (just needs to be unique).
7. For the light devices, select "Simulated Switch" in the "Type" dropdown. For lock devices, select "Simulated Lock".
8. Choose your location in the "Location" dropdown.
9. Repeat steps 4-8 for each device (one switch and two locks).

### Call the devices API to get the device

For this step, we'll be working in the files `./skills/lights.js` and `./lib/devices.js`.

Take a look at `lights.js` and notice there is some basic functionality already implemented.

Your task is to:
1. Implement the `getDevices(options, devicesAccum)` function in `devices.js` (the one toward the bottom of the file).
2. Update `lights.js` to pass in the correct arguments to `getDevices`.

When testing your changes, don't forget to stop/start your app!

## Step 3 - Let there be light!

In this step, we'll make a request to turn on or off the switch.

First, checkout the step-3 tag:

`git checkout step-3`

We'll be updating the same files as before.
You'll now notice that `lights.js` has some new code to call `devicesApi.actuateDevice`. 
Your task is to:
1. Update the options argument to pass in the correct data.
2. Finish the implementation of `actuateDevice` in `devices.js` by filling in the command body.

Don't forget to stop/start your app when testing changes.

You'll know you're successful when you get a success message back, and more importantly when you see the light go on/off in your SmartThings mobile app!

## Step 4 - Working with multiple devices

Now that we're able to control single switch devices, wouldn't it be cool if we could control multiple devices with one command?
What we're going to do now is start to add that feature, so that we can lock and unlock all our doors with a single command.

Before starting to work, checkout the step-4 tag:

`git checkout step-4`

Your task in this step will be to get all the lock devices for the user (we'll send the commands in the next step).
This will be similar to what we did in step 2, when we made the request to get a single device.
We'll be able to reuse some code we've already written.
Your task is to:

1. Update `./skills/locks.js` to pass the right capability argument to `devicesApi.getDevices`.
2. Update `./skills/locks.js` to return a message to the user if any lock devices are found, or not.

Don't forget to stop/start your app when testing changes.

You'll know you're successful when you get a success message back, and see the devices returned logged to the application's console output.

## Step 5 - Controlling multiple devices

Now that we are able to get all the locks for a user's location, we need to execute the requested command (lock/unlock) on all of them.

First, checkout the step-5 tag:

`git checkout step-5`

Your task in this step will be to:

1. Update `./lib/devices.js` to finish the implementation of `actuateDevices`
2. Update `./skills/locks.js` to call `actuateDevices` and notify the user of success or failure.

As usual, don't forget to stop/restart your app when attempting to test your changes!

## Complete code and next steps

To get the full, completed source code, checkout the step-final tag:

`git checkout step-final`

We encourage you to expand on this example if you wish.
There are many more things we could do - here are just a few ideas:

- Get the status of a device (e.g., switch on or off, temperature of a thermostat, etc.).
- Set the mode of your house.
- Group multiple devices and control them through a single command (e.g., turn on all switches downstairs).
- Send a message to Slack when someone arrives home.

## Links

Here are links to the various tools and documentation that are used in this workshop:

### API/SDK Docs

- [SmartThings Developer Portal](https://smartthings.developer.samsung.com/)
- [SmartThings API Reference](https://smartthings.developer.samsung.com/develop/api-ref/st-api.html)
- [SmartThings Capabilities Reference](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html)
- [Slack API Docs](https://api.slack.com/)
- [Botkit Docs](https://botkit.ai/docs/)

### Tools

- [SmartThings Groovy IDE](https://graph.api.smartthings.com/) (used only to create simulated devices)

### Other examples

- [SmartThings CLI](https://github.com/SmartThingsCommunity/cli-example-nodejs)
- [Weather Color Light SmartApp](https://github.com/SmartThingsCommunity/weather-color-light-smartapp-nodejs)
- [LIFX cloud-to-cloud Connector](https://github.com/SmartThingsCommunity/example-lifx-nodejs-web-connector)