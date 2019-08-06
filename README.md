# slangjs
slang sdk for the web

# integrating slang on to your webapp/pwa/website

## prerequisites

Before starting with integrating slang with your website using the web-sdk, you must create an application in our console and configure it.

console - https://console.slanglabs.in/home
console guide - https://docs.slanglabs.in/slang/developer-guide/slang-developer-guide

## step 1 - Installation

### npm packages
If you are using npm packages to manage dependencies then you have to add `slang-web-sdk` to your project.
`npm install slang-web-sdk`
or
`yarn add slang-web-sdk`

Then import slang where you want to initialize it
`import Slang from "slang-web-sdk";`


### Script tags
If however you have static html files where you want to add slang to then drop a script tag pointing to slang-web-sdk in ALL your html files.
`<script type="text/javascript" src="https://slang-public.s3.ap-south-1.amazonaws.com/index-iife.js"></script>`

This should bring slang in scope, and you can refer to it.

## step 2 - initialize slang

Once you have Slang in scope, by following either of the above two methods, you have to initialize Slang, which will add a mic button as an overlay to every page.

```Slang.initialize({

      buddyId: “BUDDY_ID_HERE”,

      apiKey: “API_KEY_HERE”,

      env: "stage", // one of ['stage','prod']

      locale: "en-IN", // one of ['en-IN','hi-IN']

      onSuccess: () => {

        console.log("Slang initialized successfully"); // anything you want to do once slang gets init successfully

      },

      onFailure: () => {

        console.log("Slang Failed to initialize");  // anything you want to do once slang fails to init

      }

    });
```

## step 3 - set intent action handler

Slang has processed whatever the user has spoken and has extracted intent and entities from it.
Now you can do whatever action you want to take based on the intent and entities.
So you have to provide slang with your function that handles the intent.

the signature of the action handler function is

`const intentHandler = (intent) => {}`

eg:

```const intentHandler = (intent) => {
        switch(intent.name){
            case 'show_balance':
                showBalance()
                return true
            default:
                return false
        }
    };
```

Once you have defined your intent handler, you can register your intent handler with slang by:

```
Slang.setIntentActionHandler(intentHandler);
```

Few things to note:

The return value lets slang know wether the action was successfull or not.
If successful Slang will speak out the affirmative for that intent, else slang will speak out the negetive.

Slang only considers a explicit return value of `false` to be failure event. Everything else is considered a success event.

If your action requires a async event (eg. a network call) then you must return a Promise.
If the promise rejects then slang considers it a failure event.
If the promise resolves then slang checks the return value and proceeds as mentioned above.

## Other stuff

`Slang.cancel()` - programmaticaly cancel the current interaction
`Slang.changeLocale( locale )` - programmaticaly change the current locale. Supported locales - [`en-IN`,`hi-IN`]
`Slang.triggerSlang()` - programmatically trigger slang. Or attach to a elem to make it a trigger. eg `elem.onclick = () => {Slang.triggerSlang()}`
`Slang.toggleMute()` - toggle mute for slang responses. Starts off unmuted
`Slang.setASRHints(hints)` - hints tells slang there is a higher probability of certain words occuring in this application.
Hints are a map of locale to an array of strings. eg:

```
const hints = {
      "en-IN": ["this","is","a","hint"]
    }
Slang.setASRHints(hints);
```

`Slang.startConversation( startStatement, isSpoken )` - programmaticaly start a conversation with a given statement instead of the welcome statement configured in the schema. `isSpoken` is a boolean flag as to wether slang will speak out the statement or not.
You can even start a new conversaion from inside a action handler (in which case slang queues the new conversation and starts it as soon as the current cycle is completed)

