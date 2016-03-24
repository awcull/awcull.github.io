---
layout: post
title:  "Tracking Websites - Chrome Extension"
date:   2015-08-03
categories: [JavaScript, Chrome]
comments: true
---

## Introduction

Writing Chrome Extensions is surprisingly easy, it just takes some time to learn the API if you are familiar with basic JavaScript, HTML, and CSS.  As part of trying to find out where I spend my time, I decided to learn how to write a Chrome Extension to track the time I spend on websites.  This will be a quick introduction to my first attempt at a Chrome Extension and leaves a lot of room for improvement as will be highlighted later.

## Design

The design of the extension has a [persistent background](https://developer.chrome.com/extensions/background_pages) that will count and record the time on a website and a popup HTML and JavaScript used to display the time spent on websites.  This is tied together with a [manifest file](https://developer.chrome.com/extensions/manifest) that points to the required files and permissions is all we need to create a basic extension.

## Manifest

The manifest below is basic and the parts described below:

{% highlight json %}
{
  "manifest_version": 2,

  "name": "Web Tracker",
  "description": "This extension attempts to track time on websites",
  "version": "1.0",

  "background" :{
	"scripts": ["background.js"]
  },
  
  "browser_action": {
	"default_title": "Tracker",
    "default_icon": "icon.png",
    "default_popup": "popup.html"
  },
  "permissions": [
    "tabs"
  ]
}
{% endhighlight %}


* [manifest_version](https://developer.chrome.com/extensions/manifest/manifest_version) as of Chrome 18 should be specified as 2, as 1 is depreciated at time of writing.
* [name](https://developer.chrome.com/extensions/manifest/name#name), specifies the name as will be displayed in install, extension management UI, and Chrome Web Store.
* [description](https://developer.chrome.com/extensions/manifest/description), describes the extension.
* [version](https://developer.chrome.com/extensions/manifest/version), specifies the version of the extension.
* [background](https://developer.chrome.com/extensions/background_pages), points to the background script that will manage the time spent on each site.
* [browser_action](https://developer.chrome.com/extensions/browserAction):
    - default_title, string shown in tool tip.
	- default_icon, the icon that will be shown next to the search bar.  The image was taken from the [Google's Getting Started: Building a Chrome Extension page](https://developer.chrome.com/extensions/getstarted).
	- default_popup, the HTML file that will be used to display information when the icon clicked.
* [permissions](https://developer.chrome.com/extensions/declare_permissions), for this example the only permission set is _tabs_ which grants access to several APIs such as chrome.tabs and chrome.windows.  This is used to get tab identification information on the active tab.

## background.js

The first step in setting up the background script is to initialize the storage.  Checking if the local storage has a value siteList, this will store all the websites and times as a JSON file.  If it does not exist create it.

{% highlight javascript %}

// Need to check local storage if it exists
if(!localStorage.siteList) {
	localStorage.siteList = JSON.stringify({});
}

{% endhighlight %}	

Once the storage is checked, we need data to put into it.  This will involve three possibilities:

* A new website is visited in the tab
* A new tab is opened or closed
* The window closes or opens, this creates a new tab.

To keep track of this, the Chrome API will be used to get a Tab object which holds the url and unique to the browser session tab id.  Using this tab id, the websites can be tracked with the following variables:

{% highlight javascript %}

var startTime = null; // when site was first visited
var curTabId = null; // current tab id
var curSite = null; // the site base url

{% endhighlight %}	

A function _updateInfo_ will be used to properly update the data.  As of now, we will leave it blank and fill it in later.

{% highlight javascript %}

// Updates the variables based on changes to tabs/windows
function updateInfo() {

}

{% endhighlight %}

## Change Website in a Tab

The first case to consider is when the user goes to a new website, the old website needs to be recorded and the script needs to know that a new website is visited.  To check for this, the Chrome API [_chrome.tabs.onUpdated.addListener_](https://developer.chrome.com/extensions/tabs#event-onUpdated).  This will be fired every time a tab is updated.

{% highlight javascript %}

// Check for tabs updating
chrome.tabs.onUpdated.addListener(
function(tabId, changeInfo, tab) {
	if (tabId == curTabId) {
		console.log("Tabs updated - " + tabId);
		updateInfo();
	}
});

{% endhighlight %}

## Change Tab

The next case is when a tab is changed.  To do this, the [_chrome.tabs.onActivated.addListener_](https://developer.chrome.com/extensions/tabs#event-onActivated) is called.  The return is a Tabs object that the tab id can be separated from and properly updated.

{% highlight javascript %}

// Check for changing tab
chrome.tabs.onActivated.addListener(
function(tabs) {
	curTabId = tabs.tabId;
	console.log("Tab changed - " + tabs.tabId);
	updateInfo();
});

{% endhighlight %}

## Window Focus

When the window focus is changed, [_chrome.windows.onFocusChanged.addListener_](https://developer.chrome.com/extensions/windows#event-onFocusChanged) is fired.  Then need to get tab information using [_chrome.tabs.query_](https://developer.chrome.com/extensions/tabs#method-query).  According to the comments regarding chrome.tabs.query, a window can only have one active tab, tab[0], at a time, so it much be the active one.  Checking to make sure there is at least one tab is checked as the debug tools register a window with no tabs, throwing an error.  Using this, the current tab index can be updated.

{% highlight javascript %}

// Query info for tabs.query
// As per https://developer.chrome.com/extensions/tabs#method-query
var queryInfo = {
	active: true,
	currentWindow: true
};

// check for window focus changing (different window or closing)
chrome.windows.onFocusChanged.addListener(
function(windowId) {
   console.log("Detected window focus changed");
   chrome.tabs.query(queryInfo,
   function(tabs) {
	console.log("Window or Tab changed");
	// Make sure tabs is defined
	if (typeof tabs[0] != 'undefined') {
		var tab = tabs[0]; // only one active tab at a time
		curTabId = tab.id;
		updateInfo();
	}
  });
});	

{% endhighlight %}

## Get URL

Now that the tab info is taken care of, the next step is to verify the website and from the tab object.  First we will write a function to check a string URL if it is valid:

{% highlight javascript %}

function getSite(url) {
	/* Generate a reg ex for verifying website
	* ^ -> start of string
	* \w+ -> accept all A-Z, a-z, and 0-9 in any order and amount
	* \/ -> explicitly check for '/'
	* \/ -> explicitly check for '/'
	* [^\/]+ -> don't match anything after a 3rd '/' is encountered
	*/
	var regex = /^(\w+:\/\/[^\/]+)/;
	// See if the given url matches, return matched group or return null
	var matches = url.match(regex);
	// If matches
	if (matches) {	
		return matches[0];
	} else {
		return null;
	}
}

{% endhighlight %}

## Updating updateInfo

Now that we can get the URL from Tabs, lets update updateInfo function to do something now.  The plan for updateInfo is:

1.  Check if current tab id is valid
*  If not valid, return out
2.  Get the current tab object using the current tab id
3.  Get the URL from the current tab
*  If invalid return out
4.  Check if a current site has been set
*  If not, set curSite and startTime to the current site and time
5.  If the current site stored does not equal the site from the active tab URL, update time from the now previous site and set curSite and startTime to current site and time

# Getting Tab URL

In order to do this the [_chrome.tabs.get_](https://developer.chrome.com/extensions/tabs#method-get) function is called to retrieve data about the specified tab.  The second parameter is a Tab object that the URL can be extracted from as tab.url.  This value can be passed onto the getSite in order to try and get the base URL.

# updateInfo()

The full function is below:

{% highlight javascript %}

function updateInfo() {
	console.log("Running update");
	// Make sure current tab exists
	if (curTabId == null) {
	  return;
	} else {
	  // Get data from the current tab
	  console.debug("Update - " + curTabId);
	  chrome.tabs.get(curTabId, 
	  function(tab) { 
	  	console.debug(tab);
		// Get base URL
		var theSite = getSite(tab.url);
		// Check if its valid
		if (theSite == null) {
			console.log("URL issue");
			return;
		}
		// Set site if doesnt exist yet
		if (curSite == null) {
			console.log("Setting new site - " + curSite);
			startTime = new Date();
			curSite = theSite;
			return;
		}
		// If new site, update time spent
		if (curSite != theSite && curSite != null) {
			var diffTimeSec = ((new Date()).getTime() - startTime.getTime()) / 1000;
			updateTime(curSite, diffTimeSec);
			curSite = theSite;
			startTime = new Date();
		}
	  });
    }
}

{% endhighlight %}

## Update Time on Site

We have on more function to write in order for this to be complete, the _updateTime_ function.  This will update the time spent on a site and write it to local storage.  It is pretty straight forward:

{% highlight javascript %}

function updateTime(theSite, timeSeconds) {
	console.log("Updating time " + theSite);
	// get JSON file
	var sites = JSON.parse(localStorage.siteList);
	// Check to see if site exists already, if not save site and set time to 0.
	if (!sites[theSite]) {
		sites[theSite] = 0;
	} 
	// Update site by adding the number of seconds spent
	sites[theSite] = sites[theSite] + timeSeconds;
	console.log("Updating time - " + timeSeconds);
	// Write file as JSON
	console.log("Updating time - writing file");
	localStorage.siteList = JSON.stringify(sites);
}

{% endhighlight %}

## Finishing up background.js

To finish up, the part about checking if localStorage exists and checking tabs/windows should be formed into their own functions as below:

{% highlight javascript %}

// Initialize storage variables and check for activity of tabs/windows
function init() {
	console.log("Initialize");
	// Need to check local storage if it exists
	if(!localStorage.siteList) {
		localStorage.siteList = JSON.stringify({});
	}
	
	// Check for activity changes
	checkActivity();
}

// Check for activity of tabs/windows and updates variables correctly
function checkActivity() {
	console.log("checkActivity initialized");
	// Check for changing tab
	chrome.tabs.onActivated.addListener(
	function(tabs) {
		curTabId = tabs.tabId;
		console.log("Tab changed - " + tabs.tabId);
		updateInfo();
	});
	
	// Check for tabs updating
	chrome.tabs.onUpdated.addListener(
	function(tabId, changeInfo, tab) {
		if (tabId == curTabId) {
			console.log("Tabs updated - " + tabId);
			updateInfo();
		}
	});
	
	// Query info for tabs.query
	// As per https://developer.chrome.com/extensions/tabs#method-query
	var queryInfo = {
		active: true,
		currentWindow: true
	};
	
	// check for window focus changing (different window or closing)
	chrome.windows.onFocusChanged.addListener(
	function(windowId) {
		console.log("Detected window focus changed");
		chrome.tabs.query(queryInfo,
		function(tabs) {
			console.log("Window or Tab changed");
			// Make sure tabs is defined
			if (typeof tabs[0] != 'undefined') {
				var tab = tabs[0]; // only one active tab at a time
				curTabId = tab.id;
				updateInfo();
			}
		});
	});	
}

{% endhighlight %}

This should provide the required information to keep track of websites.

## Popup JavaScript and HTML

The final part is displaying the time spent on sites when the user clicks on the icon.  This combines an HTML template and JavaScript to build it.  If you have experience working with HTML this should be a cake walk.

# HTML

The HTML portion is heavily borrowed from the [Show Tabs in Process](https://developer.chrome.com/extensions/samples) Google Extension example.

{% highlight html %}

<!doctype html>
<html>
    <head>
    <title>Popup</title>
    <link href="popup.css" rel="stylesheet" type="text/css">
    <script type="text/javascript" src="popup.js" async></script>
  </head>
  <body>
    <div id="title"></div>
    <div id="tab-list"></div>
  </body>
</html>

{% endhighlight %}

Originally, the extension was failing due to NULL issue regarding the DOM.  This was fixed thanks to this [Stack Overflow](http://stackoverflow.com/questions/436411/where-is-the-best-place-to-put-script-tags-in-html-markup) by changing the popup.js line to _<script type="text/javascript" src="popup.js" async></script>_.

# css

Again the css was almost identical to [Show Tabs in Process](https://developer.chrome.com/extensions/samples) Google Extension example.  The header and row sections have padding added to format output in a more pleasing manner.

{% highlight css %}

/**
 * Copyright (c) 2011 The Chromium Authors. All rights reserved.
 * Use of this source code is governed by a BSD-style license that can be
 * found in the LICENSE file.
 */

body {
  overflow: hidden;
  margin: 0px;
  padding: 0px;
  background: white;
  width: 400px;
}

div:first-child {
  margin-top: 0px;
}

div {
  padding: 1px 3px;
  font-family: sans-serif;
  font-size: 10pt;
  width: 400px;
  margin-top: 1px;
}

table {
  width: 100%;
}

th {
  padding: 5px;
}

td {
  padding: 5px;
}


{% endhighlight %}

# popup.js

In order to process the data into a HTML format, we need to:

* Read data in
* Sort data based on time
* Create headers
* Loop over sorted sites and add site URL and time spent to a table

I hope the code explains it self with the comments added.

{% highlight javascript %}

// Based on Show Tabs in Process, https://developer.chrome.com/extensions/samples
function init() {
  // Read in JSON data generated from background.js
  var siteList = JSON.parse(localStorage.siteList);
  
  // Create sorted list
  var sortedSites = new Array();
  // Generate sorted list
  for (theSite in siteList) {
   sortedSites.push([theSite, siteList[theSite]]);
  }
  // Sort sites by time
  sortedSites.sort(function(a, b) {return b[1] - a[1];});

  // Set up html
  var outputDiv = document.getElementById("tab-list");
  var titleDiv = document.getElementById("title");
  
  // create title
  titleDiv.innerHTML = "<b>List of sites and time</b>";
  
  // Create rows/cols to store info
  var row = document.createElement("tr");
  var col = document.createElement("td");
  
  // Create headers
  row = document.createElement("tr"); // define row
  col = document.createElement("th"); // define header
  col.appendChild(document.createTextNode("Site"));
  row.appendChild(col);
  col = document.createElement("th"); // define header
  col.appendChild(document.createTextNode("Minutes"));
  row.appendChild(col);
  outputDiv.appendChild(row);
  
  // Loop over sorted sites and create html, site name and time in minutes
  for (var j = 0; j < sortedSites.length; j++) {
   var site = sortedSites[j][0];
   var secToMin = siteList[site] / 60; // Convert seconds to minutes
	// make sure site is not null, had some issue originally with this but should no longer be an issue but better to check
	if (site != null) {
		// create table information
		// Create site name column
		row = document.createElement("tr"); // deinfe row
		col = document.createElement("td"); // define col/cell
		col.appendChild(document.createTextNode(site)); // add site name
		row.appendChild(col); // append to row
		// Create time column
		col = document.createElement("td"); // define col/cell
		col.appendChild(document.createTextNode(secToMin));  // add minutes
		row.appendChild(col); // append to row
		outputDiv.appendChild(row);
   } // end of null check
  } // end of for loop over sorted sites
} // end of init function

document.addEventListener("DOMContentLoaded", init());

{% endhighlight %}

## Conclusion

This will provide very basic web time tracking capabilities.  In the future, adding ability to clear, download data, and adding dates to the time may be implemented and if it is will make a post about it.  To get the final data, it can be downloaded [here](http://www.awcull.com/assets/2015-08-03-WebTracker/website-Tracker.zip).  Loading it, go to chrome://extensions and enable Developer mode.  Then click Load unpacked extension... and navigate to the folder, click okay and it should be working!

To further extend this, sites that I know I visit a lot, such as [Reddit](http://www.reddit.com/), the update site name could be updated to include the proper sub-Reddit.