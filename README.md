# Cronofy Calendar View: component prototype

* version: 0.1.0

> Note: this is a pre-alpha prototype. Breaking-changes may occur on minor version updates. The current components are provided for demoing and experimentation only. This document will be updated when new versions are released.

## Components covered by this document

* CronofyComponents.Agenda

## Installation

For all components, load CronofyComponents into your app by including the source `.js` file into your page.

    <script src="https://s3.eu-west-2.amazonaws.com/component-demo/CronofyComponents.v0.1.0.js"></script>

### Authenticated mode

CronofyComponents requires two things to run: an authentication token, and a target DOM element (to load the component into). The "token" value should be the authorization token from the API as a string, and the target DOM element is indicated by a unique `ID` string. Once you've included the `js` file in your project, you can initialize the Agenda component using the `Agenda()` method, and passing in an options object with `token` and `target` values. For example:

    CronofyComponents.Agenda({token: "YOUR_TOKEN",target: "cronofy-calendar"});

Using the above `Agenda()`, CronofyComponents will look for a DOM element with the ID `cronofy-calendar`, and inject the CronofyComponents component inside that element. 

A simple integration into an `index.html` file would look something like this:

    <div id="cronofy-calendar"></div>
    <script src="https://s3.eu-west-2.amazonaws.com/component-demo/CronofyComponents.v0.1.0.js"></script>
    <script>
        CronofyComponents.Agenda({token: "YOUR_TOKEN",target: "cronofy-calendar"});
    </script>

### Demo mode

For demo purposes, it is possible to bypass the authentication step. If the `demo` option is set to `true`, the component will not make any API calls and will display mock content.

    <div id="cronofy-calendar"></div>
    <script src="https://s3.eu-west-2.amazonaws.com/component-demo/CronofyComponents.v0.1.0.js"></script>
    <script>
        CronofyComponents.Agenda({
            target: "cronofy-calendar",
            demo: true
        });
    </script>

## Theming

CronofyComponents will automatically inherit the typeface used in your page. You can assign a custom color palette for the calendars by passing in an array of HEX values to the `colors.calendars` option. For example:

    <div id="cronofy-calendar"></div>
    <script src="https://s3.eu-west-2.amazonaws.com/component-demo/CronofyComponents.v0.1.0.js"></script>
    <script>
        CronofyComponents.Agenda({
            token: "YOUR_TOKEN",
            target: "cronofy-calendar",
            colors: { calendars: ['#0074d9','#ffdc00'] }
        });
    </script>

