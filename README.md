# Cronofy Calendar View: component prototype

* version: 0.1.0

> Note: this is a pre-alpha prototype. Breaking-changes may occur on minor version updates. The current components are provided for demoing and experimentation only. This document will be updated when new versions are released.

## Components covered by this document

* CronofyComponents.Agenda
* CronofyComponents.SlotPicker

---

## Installation

All components accept some global options, and each have additional component-specific options. For all components, load into your app by including the source `.js` file into your page.

    <script src="https://s3.eu-west-2.amazonaws.com/component-demo/CronofyComponents.v0.1.0.js"></script>

Once you've included the `js` file in your project, you can initialize the desired component using the corresponding method and passing in an options as an object. For example, to load the "Agenda" component, your script would look like this:

    CronofyComponents.Agenda({token: "YOUR_TOKEN",target: "cronofy-calendar"});

## Options

All components require a target DOM ID (to mount the component on the page) and a token (to authorize the API connection), but some require additional options.

### Authenticated mode vs. Demo mode

CronofyComponents all require two things to run: an authentication token, and a target DOM element (to load the component into). The "token" value should be the authorization token from the API as a string, and the target DOM element is indicated by a unique `ID` string.

Using the above `Agenda()` example, CronofyComponents will look for a DOM element with the ID `cronofy-calendar`, and inject the CronofyComponents component inside that element.

A simple integration into an `index.html` file would look something like this:

    <div id="cronofy-calendar"></div>
    <script src="https://s3.eu-west-2.amazonaws.com/component-demo/CronofyComponents.v0.1.0.js"></script>
    <script>
        CronofyComponents.Agenda({
            token: "YOUR_TOKEN",
            target: "cronofy-calendar"
        });
    </script>

For demo purposes, it is possible to bypass the authentication step. If the `demo` option is set to `true`, the component will not make any API calls and will display mock content.

    <div id="cronofy-calendar"></div>
    <script src="https://s3.eu-west-2.amazonaws.com/component-demo/CronofyComponents.v0.1.0.js"></script>
    <script>
        CronofyComponents.Agenda({
            target: "cronofy-calendar",
            demo: true
        });
    </script>

### Global options

These apply to all components.

* `token` [required]: auth token for API connection. *Not required if the component is activated in demo mode.*
* `demo`: boolean to activate demo-mode. Defaults to `false`. If `demo` is set to `true` the component will return mock data (and not make any API calls). 
* `target` [required]: ID of mounting element (e.g. 'cronofy-slot-picker').
* `api_domain` [optional]: Override the default Default value is `"http://api.cronofy.com"`.
* `styles` [optional]: an object containing you custom style values See [Custom Styles](#custom-styles) for details of valid options.


### Agenda options

### SlotPicker options

* `query` [required]: object that matches a valid Cronofy [Availability request](https://www.cronofy.com/developers/api/#availability).
* `callback` [required]: the function to be called when a slot has been selected by the user. Receives an object for that slot is the form: 
    
    {
      "start": "2018-11-13T09:00:00Z",
      "end": "2018-11-13T11:00:00Z",
      "participants": [
        { "sub": "acc_567236000909002" },
        { "sub": "acc_678347111010113" }
      ]
    }

* `confirm`[optional]: Boolean that defines if an extra "confirmation" step is used after a user selects a slot from the list. Defaults to `true`.


Example Slot Picker init call:

    CronofyComponents.SlotPicker({
        token: "TOKEN_GOES_HERE",
        target: 'cronofy-slot-picker',
        api_domain:"http://local.cronofy.com",
        query: {
            participants: [
                {
                    required: "all",
                    members: [
                        { sub: "test_busy_1000-1030_1430-1540_2200-0600" },
                        { sub: "acc_5b97a52a5c92eb0cc0400ffe" },
                        { sub: "test_busy_1130-1200_1445-1500" }
                    ]
                }
            ],
            required_duration: { minutes: 30 },
            available_periods: [
                { start: "2018-11-09T09:00:00Z", end: "2018-11-09T17:00:00Z" },
                { start: "2018-11-10T09:00:00Z", end: "2018-11-10T17:00:00Z" },
                { start: "2018-11-11T09:00:00Z", end: "2018-11-11T17:00:00Z" }
            ]
        },
        styles: {
            prefix: "custom-name",
            height: "400px",
            // padding: 0
        },
        callback: slot => console.log('callback',slot),
        // confirm: false,
        demo: false
    });

## Custom Styles

* `useStyles`: set to `false` to disable in-built CSS for customizable elements. Most inbuilt styles are locked down (for example the transitions and basic layout), but some are optional (for example, the slot button styles in the Slot Picker component). If `useStyles` is set to `false` the optional styles will not be applied.
* `prefix`: customizable elements are given a prefixed class name using this value. For example, if the `prefix` was set as "Foo", the class name on a slot element would be `Foo__slot`. Defaults to the name of the component (e.g. `"SlotPicker"`).
* `height`: the height of the component in pixels. Defaults to `"300px"`.
* `padding`: the size of the padding around the edge of the component. Defaults to `"1em"`.
* `colors`: there are several colors that can be customized here. All options take a HEX color code string (e.g. `"#bada55"`)
    * `text`: the color for text elements in the component
    * `lines`: the color used for borders
    * `highlight`: the primary color used for interactions (hover, focus, etc.)
    * `background`: the background color for the component. By default, components are transparent and therefore inherit the background color of whatever element they are mounted within.
