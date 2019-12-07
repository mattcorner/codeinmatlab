---
title: "Tracking user behaviour in Google Analytics"
categories:
  - Google Analytics
tags:
  - user tracking
  - google analytics
  - toolbox
  - file exchange
toc: true
toc_sticky: true
---
Google Analytics allows web and application developers to track user behaviour. The goal is to better focus development time on features that makes a difference for their users.

Recently I released a new toolbox on the [MATLAB File Exchange](https://au.mathworks.com/matlabcentral/fileexchange/73419-google-analytics-toolbox) that opens up this functionality to MATLAB application and toolbox developers. For the first time, the toolbox allows developers to get a better understanding of the systems that their users are running on, and the way in which their users use their tools. 

This article is a quick getting started guide to making use of the toolbox in your application. 

## Signing up for Google Analytics
The first step is to sign up for a free Google Analytics account. Visit the [Google Analytics website](https://analytics.google.com/) and sign in with your Google account (or create one if you don't already have). Once signed in, click Sign up. Fill out your Account Name, Website Name, Website URL, and select an Industry Category.

Since we are re purposing website tracking for use in matlab applications and toolboxes, feel free to make up a url etc as applicable.

Click 'Get Tracking ID' and accept the Terms of Service Agreement. Note down your TrackingID as you will need this unique identifier log data to your account.

## Installing the toolbox
The toolbox can be installed either from the [MATLAB File Exchange](https://au.mathworks.com/matlabcentral/fileexchange/73419-google-analytics-toolbox), or from the [Github Development Page](https://github.com/mattcorner/matlab-google-analytics).

**Note:** Feel free to include this toolbox as a dependency in your project, or copy the codebase into your own.
{: .notice--warning}

## Generating a tracker
The tracker is the first element that needs to be created in order to log data. You only need to generate this once per session and retain a handle to it. It contains your tracking ID, and 'website URL'.

``` matlab
tid = "U-XXXXXXXX-XX";
url = "http://testing.com";
tracker = mga.Tracker(tid, url);
```

## Gathering user information
Next, we want to gather information on the user, and their system. Much of this is automated and again, need only be generated once per session. By default, the following is identified:

* ClientID: This is a unique identifier that persists across matlab sessions. It identified the machine.
* Screen Resolution: Screen resolution for the primary monitor.
* Screen Depth: Screen colour depth.
* Operating System
* Matlab version
* User language

```matlab
visitor = mga.Visitor();
```

Optionally, we can provide some extra information should you wish to include it. If your toolbox or application has a version number, it can be useful to log this to see what your users are currently running. 

Similarly, a userID can be provided. This is intended for applications that for example have user authentication and where a user may work across multiple machines. The userID should be something that uniquely identifies a user, but contains no personal identifiable information.

```matlab
userID = "somethingunique";
appVersion = "1.2.3";
visitor = mga.Visitor('UserID', userID, ...
    'AppVersion', appVersion);
```

## Logging a hit
Google Analytics supports several different hit types. At initial release, the Matlab toolbox supports two of these; pageview and event. In most cases these should cover the types of activities that would be logged in a Matlab environment.

### Logging an event
An event is to log interactions that the user has made with an application. For instance, the user may have set the axis limits for a plot, started playing a video, or resized the window.

Logging an event requires an Category and an Action.

```matlab
ev = mga.hit.Event("Video", "Play");
tracker.track(visitor, ev);
```

Optionally, an Label and an Value can be attributed to the event.

```matlab
ev = mga.hit.Event("Screen", "Resized", ...
   'Label', 'User resized screen', ...
   'Value', 1024);
tracker.track(visitor, ev);
``` 

## Logging a page view
A pageview hit type is used for a user entering a specific section of an application. Since Google Analytics is set up for websites, consider how you will map screens of your application to web page urls. e.g. '/login', '/splash', 'plotter'

Logging a pageview is as simple as creating a Pageview object, and tracking it using the Tracker object.

```matlab
pv = mga.hit.Pageview("/gettingStarted.mlx");
tracker.track(visitor, pv);
```

Optionally, you can include a page title along with the page name.
```matlab
pv = mga.hit.Pageview("/gettingStarted.mlx", ...
    'Title', 'This is a page title');
tracker.track(visitor, pv);
```

## Seeing hits
### Real-time
You will be able to see basic detail of pageviews and events in real-time as they are logged. These can be access from the real-time tab, or in the real time widget.

![Real-time pageviews](/assets/post/google-analytics/real-time.png)

### Processed
After approx. 24 hours, Google processes hits and you can begin to delve down into the detail. For instance, by now you will be able to look at system information such as browser and OS.