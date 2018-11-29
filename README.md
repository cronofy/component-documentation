# Cronofy Components

* version: 0.4.0

> Note: this is a pre-alpha prototype. Breaking-changes may occur on minor version updates. The current components are provided for demoing and experimentation only. This document will be updated when new versions are released.

## Components covered by this document

* [CronofyComponents.Agenda](#agenda-component)
* [CronofyComponents.SlotPicker](#slotpicker-component)
* [CronofyComponents.AvailabilityViewer](#availabilityviewer-component)

---

## Installation

All components accept some global options, and each have additional component-specific options. For all components, load into your app by including the source `.js` file into your page.

    <script src="https://components.cronofy.com/js/CronofyComponents.v0.4.0.js"></script>

Once you've included the `js` file in your project, you can initialize the desired component using the corresponding method and passing in an options as an object. For example, to load the "Agenda" component, your script would look like this:

    CronofyComponents.Agenda({token: "YOUR_TOKEN",target: "cronofy-calendar"});

### Authenticated mode vs. Demo mode

CronofyComponents all require two things to run: an authentication token, and a target DOM element (to load the component into). The "token" value should be the authorization token from the API as a string, and the target DOM element is indicated by a unique `ID` string.

Using the above `Agenda()` example, CronofyComponents will look for a DOM element with the ID `cronofy-calendar`, and inject the CronofyComponents component inside that element.

A simple integration into an `index.html` file would look something like this:

    <div id="cronofy-agenda"></div>
    <script src="https://components.cronofy.com/js/CronofyComponents.v0.4.0.js"></script>
    <script>
        CronofyComponents.Agenda({
            token: "YOUR_TOKEN",
            target: "cronofy-agenda"
        });
    </script>

For demo purposes, it is possible to bypass the authentication step. If the `demo` option is set to `true`, the component will not make any API calls and will display mock content.

    <div id="cronofy-agenda"></div>
    <script src="https://components.cronofy.com/js/CronofyComponents.v0.4.0.js"></script>
    <script>
        CronofyComponents.Agenda({
            target: "cronofy-agenda",
            demo: true
        });
    </script>

### Generating a token

    POST /v1/component_tokens

    Authorization: Bearer {API_KEY}
    Content-Type: application/json

    {
        "version": "1",
        "component": "COMPONENT_TYPE",
        "subs": ["acc_1234"],
        "origin": "http://localhost"
    }

* `component` [required]:  use `agenda` for the Agenda component, and `availability` for the Slot Picker and Availability Viewer components.
* `subs` [required]: pass a single sub ID for the Agenda component (still within an array: `["acc_1234"]`), and an array of all the participants' subs for the Slot Picker and Availability Viewer (`["acc_1234", "acc_4321", "acc_6789"]`).
* `origin` [required]: the URL of the app where the component will be used.

Expected response:

    {
        "component_token": {
            "component": "agenda",
        "origin": "http://api.cronofy.com",
        "token": "YOUR_NEW_COMPONENT_TOKEN",
        "expires_in": 64800
        }
    }

---

## Agenda Component

The Agenda component displays events for a single-day period. The primary view is a column of event summaries, with the ability to navigate to the next or previous day (up to a maximum of 14 days into the past and 90 days into the future). Clicking an event reveals the full details for that event. 

The Agenda component will fill the width of its parent DOM element, and has a set height of 400px. The default colors for different calendars can be set by passing an array of hex codes to the `colors.calendars` option.

### Agenda options

* `token` [required]: auth token for API connection. *Not required if the component is activated in demo mode.*
* `demo`: boolean to activate demo-mode. Defaults to `false`. If `demo` is set to `true` the component will return mock data (and not make any API calls). 
* `target` [required]: ID of mounting element (e.g. "cronofy-agenda").
* `api_domain` [optional]: Override the default Default value is `"https://api.cronofy.com"`.
* `colors` [optional]: The Agenda component can be assigned a custom color palette for the calendars by passing in an array of HEX values to the colors.calendars option. For example: `colors: { calendars: [ "#0074d9", "#ffdc00" ] }`

### Example Agenda init:

    <div id="cronofy-agenda"></div>
    <script src="https://components.cronofy.com/js/CronofyComponents.v0.4.0.js"></script>
    <script>
        CronofyComponents.Agenda({
            token: "YOUR_TOKEN",
            target: "cronofy-agenda",
            colors: { calendars: [ "#bada55", "#c0ffee", "#133337" ] }
        });
    </script>

---

## SlotPicker component

The Slot Picker component is an embeddable version of the (Real Time Scheduling)[https://www.cronofy.com/real-time-scheduling] interface. It converts an [availability query](https://www.cronofy.com/developers/api/#availability) into a list of bookable slots. When a user confirms a slot, the component passes that slot's details into a callback function (provided when the component is initialised).

The Slot Picker component will fill the width of its parent DOM element. The height defaults to 300px with internal padding of 1em, but these can be overridden through the `styles` option.

### SlotPicker options

* `token` [required]: auth token for API connection. *Not required if the component is activated in demo mode.*
* `demo`: boolean to activate demo-mode. Defaults to `false`. If `demo` is set to `true` the component will return mock data (and not make any API calls). 
* `target` [required]: ID of mounting element (e.g. "cronofy-slot-picker").
* `api_domain` [optional]: Override the default Default value is `"https://api.cronofy.com"`.
* `query` [required]: object that matches a valid Cronofy [Availability request](https://www.cronofy.com/developers/api/#availability).
* `callback` [required]: the function to be called when a slot has been selected by the user. Receives an object for that slot in this format: 
    
```
{
    "start": "2018-11-13T09:00:00Z",
    "end": "2018-11-13T11:00:00Z",
    "participants": [
        { "sub": "acc_12345678" },
        { "sub": "acc_87654321" }
    ]
}
```

* `confirm`[optional]: Boolean that defines if an extra "confirmation" step is used after a user selects a slot from the list. Defaults to `true`.
* `styles` [optional]: an object containing you custom style values See [Custom Styles](#custom-styles) for details of valid options.
    * `useStyles`: set to `false` to disable in-built CSS for customizable elements. Most inbuilt styles are locked down (for example the transitions and basic layout), but some are optional (for example, the slot button styles in the Slot Picker component). If `useStyles` is set to `false` the optional styles will not be applied.
    * `prefix`: customizable elements are given a prefixed class name using this value. For example, if the `prefix` was set as "Foo", the class name on a slot element would be `Foo__slot`. Defaults to the name of the component (e.g. `"SlotPicker"`).
    * `height`: the height of the component in pixels. Defaults to `"300px"`.
    * `padding`: the size of the padding around the edge of the component. Defaults to `"1em"`.

Available classes:

* `.{PREFIX}__slot`
* `.{PREFIX}__slots-header`
* `.{PREFIX}__confirmation-wrapper`
* `.{PREFIX}__confirmation-summary`


### Example Slot Picker init:

    <div id="cronofy-slot-picker"></div>
    <script src="https://components.cronofy.com/js/CronofyComponents.v0.4.0.js"></script>
    <script>
        CronofyComponents.SlotPicker({
            token: "TOKEN_GOES_HERE",
            target: "cronofy-slot-picker",
            query: {
                participants: [
                    {
                        required: "all",
                        members: [
                            { sub: "acc_12345678" },
                            { sub: "acc_87654321" }
                        ]
                    }
                ],
                required_duration: { minutes: 30 },
                available_periods: [
                    { start: "2018-11-09T09:00:00Z", end: "2018-11-09T17:00:00Z" },
                    { start: "2018-11-10T09:00:00Z", end: "2018-11-10T17:00:00Z" }
                ]
            },
            styles: {
                prefix: "custom-name",
                height: "400px"
            },
            callback: slot => console.log("callback",slot)
        });
    </script>

---

## AvailabilityViewer component

The Availability Viewer component is an interactive week-based display of availability. It converts an [availability query](https://www.cronofy.com/developers/api/#availability) into a grid of days with free/busy times displayed. If the requested available periods span more than one week, the user can browse those weeks with the provided navigation buttons. Users can pre-select an available slot for any available block of time, and then confirm that slot with a button-click. When a user confirms a slot, the component passes that slot's details into a callback function (provided when the component is initialised).

The Availability Viewer component will fill the width of its parent DOM element. The height of the component depends on the range of the provided available options (i.e. showing available slots between 9am and 5pm will take up more space that the display for 9am to 2pm).

### AvailabilityViewer options

* `token` [required]: auth token for API connection. *Not required if the component is activated in demo mode.*
* `demo`: boolean to activate demo-mode. Defaults to `false`. If `demo` is set to `true` the component will return mock data (and not make any API calls). 
* `target` [required]: ID of mounting element (e.g. "cronofy-availability-viewer").
* `api_domain` [optional]: Override the default Default value is `"https://api.cronofy.com"`.
* `query` [required]: object that matches a valid Cronofy [Availability request](https://www.cronofy.com/developers/api/#availability).
* `extras` [optional]: use this object to add further limits to the availability display:
    * `start_time` [optional]: hide any available slots before this time. Defaults to "09:00".
    * `end_time` [optional]: hide any available slots `after` this time. Defaults to "17:30".
    * `interval` [optional]: When selecting a slot, the available options are staggered by this amount. Defaults to `15`.
* `locale` [optional]: The Availability Viewer supports localization (e.g. `locale: "fr"` to load in French). Defaults to browser language setting.
* `callback` [required]: the function to be called when a slot has been selected by the user. Receives an object for that slot in this format: 
    
```
{
    "start": "2018-11-13T09:00:00Z",
    "end": "2018-11-13T11:00:00Z",
    "participants": [
        { "sub": "acc_12345678" },
        { "sub": "acc_87654321" }
    ]
}
```

* `styles` [optional]: an object containing you custom style values See [Custom Styles](#custom-styles) for details of valid options.
    * `useStyles`: set to `false` to disable in-built CSS for customizable elements. Most inbuilt styles are locked down (for example the transitions and basic layout), but some are optional (for example, the slot hover styles in the Availability Viewer component). If `useStyles` is set to `false` the optional styles will not be applied.
    * `prefix`: customizable elements are given a prefixed class name using this value. For example, if the `prefix` was set as "Foo", the class name on a slot element would be `Foo__slot`. Defaults to the name of the component (e.g. `"AvailabilityViewer"`).

Available classes:

* `.{PREFIX}__slot-label`
* `.{PREFIX}__slot--unavailable`
* `.{PREFIX}__hover-slot`
* `.{PREFIX}__hover-slot-times`
* `.{PREFIX}__hover-slot-tooltip`

### Example AvailabilityViewer init:

    <div id="cronofy-availability-viewer"></div>
    <script src="https://components.cronofy.com/js/CronofyComponents.v0.4.0.js"></script>
    <script>
        CronofyComponents.AvailabilityViewer({
            token: "TOKEN_GOES_HERE",
            target: 'cronofy-availability-viewer',
            query: {
                participants: [
                    {
                        required: "all",
                        members: [
                            { sub: "acc_12345678" },
                            { sub: "acc_87654321" }
                        ]
                    }
                ],
                required_duration: { minutes: 60 },
                available_periods: [
                    { start: "2018-12-11T09:00:00Z", end: "2018-12-11T17:00:00Z" },
                    { start: "2018-12-12T09:00:00Z", end: "2018-12-12T17:00:00Z" },
                    { start: "2018-12-13T09:00:00Z", end: "2018-12-13T17:00:00Z" },
                    { start: "2018-12-17T09:00:00Z", end: "2018-12-17T17:00:00Z" }
                ],
            },
            extras: {
                start_time:"09:00",
                end_time: "15:30",
                interval: 15
            },
            styles: {
                useStyles: true,
                prefix: "custom-name"
            },
            callback: slot => console.log('callback',slot),
        });
    </script>