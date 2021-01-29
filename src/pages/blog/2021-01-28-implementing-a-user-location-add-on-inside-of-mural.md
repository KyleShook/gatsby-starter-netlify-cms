---
templateKey: blog-post
title: Implementing a user-location add-on inside of MURAL
date: 2021-01-28T21:46:09.295Z
description: Creating new add-ons for MURAL
featuredpost: false
featuredimage: /img/untitled-mural-•-ks-dev-env-2021-01-19-at-2.21.58-pm.jpg
tags:
  - add-ons
---
> This article assumes you have already set up your [developer environment](https://github.com/tactivos/dev-env) and are able to run MURAL locally.

MURAL add-ons allow developers to enhance MURAL with extra features to suit their individual needs. Examples include user maps, icebreakers, and more.

## Create a User Map add-on

In this lesson, we’ll add a small **user map** to our mural member profile, like this:

![MURAL member profile with small map](/img/screen-shot-2021-01-19-at-2.18.57-pm.png)

###### Note: We’ve taken privacy into consideration. Users can allow or block this feature.

This add-on allows members to view another mural member’s location. (Don’t worry, members have to allow location viewing in their settings.)

### Part 1: Explore the addons folder

1. Go to the [murally repo](https://github.com/tactivos/murally) and look at the `addons` folder. You can find it at `/murally/src/mural/addons`.
2. Inside the `addons` folder, you’ll see previously-created add-ons such as dice, cards, and gallery. This is where your new add-ons and their respective folders and files will live. Feel free to explore the included add-ons, many of which might be experimental.

![Addons direction folder](/img/addonsfolder.jpg)

### Part 2: Set up your JavaScript library

As part of our user map project, you’ll need the help of a JavaScript library for interactive maps. There are many options available. For this project, we’ll use [Leaflet](https://leafletjs.com/index.html), a widely-used open source map library.

![Leaflet library logo](/img/screen-shot-2021-01-22-at-2.16.15-pm.png)

Leaflet supports a variety of ways to use it with your project, including using CDN links, installing Leaflet via NPM, or direct download.

1. Download the package files here: [Leaflet package files](https://leafletjs.com/download.html).
2. Copy/paste the leaflet package folder into your new addon folder, which we will create in the next step.

### Part 3: Create the location folder and JavaScript file

Next, we’ll create a new folder in the `addons` directory.

1. In the add-ons directory, create a folder named **location**.
2. Inside the **location** folder, create a new JavaScript file named **location.js**.

> *If you’d prefer, you can copy the full code snippet for this file. The complete snippet is at the bottom of this post, heavily commented.*

*However, you will still need to add some code to 2 other files. See parts 5 & 6 below.*

### Part 4: Edit the location.js file

Now you can write the actual code for our project inside the **location.js** file. For each numbered step below, copy/type the code snippets verbatim into your **location.js** file.

1. Import the outside files you need into this project:

```javascript
import React from  'react';
// @ts-ignore
import './leaflet/leaflet.js';
import './leaflet/leaflet.css';
import './style.sass'; // If you choose to add some custom styles
```

> Currently, we are writing in JavaScript. Later, we’ll refactor the code using TypeScript, which is more MURAL-friendly. This is why we’ve included the @ts-ignore line.

2. Create the MemberMap function. Inside of MemberMap, add an **if** statement to kickstart our new location feature.

```javascript
function MemberMap() {
	// If geolocation services are available
	if ('geolocation' in navigator') {
		// Next step
}
```

3. Add Location sharing and map coordinates. Inside the **if** statement, prompt the user to allow or block location sharing. Assign variables to both latitude and longitude coordinates. Then set those coordinates as the `coords` array.

```javascript
navigator.geolocation.getCurrentPosition(function(position) {
	const { latitude } = position.coords;
	const { longitude } = position.coords;
	
	const coords = [latitude, longitude];
```

![Browser location permission prompt](/img/locationprompt.png)

4. Create the *memberLocation* variable. Continuing with the **if** statement, create a new `memberLocation` variable. This allows you to add a custom map marker to our map. This isn’t required, but it adds a little more flair to the new feature.

```javascript
let memberLocation = L.icon({
	iconUrl:
		'https://assets-global.website-files.com/5ddd9c3f2186308353fe682d/5e93cf2b73fd9d8622880a1d_favicon.png',
	iconSize: [20,  20],
});
```

5. Create the `map` variable. This links the Leaflet map to the div id `memberMap`. It also disables the zoom buttons on the map and sets the member coordinates and starting zoom level.

```javascript
let map = L.map('memberMap', { zoomControl: false }).setView(coords, 6);
```

6. Choose a map style

When using *most* location libraries, you have the option to choose from many types of [map styles](http://leaflet-extras.github.io/leaflet-providers/preview/), or **tiles**. For this project use a basic OpenStreetMaps tile. Set the max zoom to level 6 and add this choice to our map.

```javascript
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
	maxZoom:  6,
}).addTo(map);
```

7. Add our custom map marker

![An arrow pointing to a map marker](/img/custommarker.jpg)

Add the custom map marker that you set up earlier in step 4.

```javascript
L.marker(coords, {
		icon: memberLocation,
	}).addTo(map);
});
```

8. Return the div and log errors

Return the div in which the map will be loaded. If location services are not available, it will log a basic error to the console.

9. Export the `MemberMap` function to use in other files.

```javascript
		return <div id="memberMap" className="mural-member-map" />
	} else {
		console.log('Sorry this feature is currently unavailable');
	}
}

export default MemberMap;
```

### Part 5: Edit the info-member.tsx file

1. Open the **info-members.tsx** file.

The full path should look like this:

> murally/src/views/mural/ui/member-card/info-member.tsx

2. Import the MemberMap function into the **info-members.tsx** file at the bottom of the other import statements.

*Note the @ts-ignore. As mentioned earlier, we will refactor for TypeScript. For now, we're just trying to get a quick proof of concept up and running.*

```javascript
// @ts-ignore
import MemberMap from  '../../../../mural/addons/location/location.js';
// Your location path may differ
```

3. Add the map container just before the end of the HTML (line 10, below).

```javascript
<div className="info-member">
	<img alt="Member image"  src={url(member)}  width="300px"  />
	<span className="member-name ellipsis">
		<strong>{member.fullName} </strong>
	</span>
	<div className="description">
		<span className={classnames('status-ball', {online,})} />
		<span className="member-type">{getMemberLabel(member,  'mural')}</span>
	</div>
	// Place your map component here
	<MemberMap />
</div>
```

### Part 6: Create a style.sass file

1. Inside the **location** folder, create a **style.sass** file and open it.
2. Inside this new file, set the size of the map and override some image properties.

```javascript
@import "src/ui/style"

.mural-member-map
	width: 100%
	min-height: 100px
	height: 100px
	margin-top: 10px
	
img.leaflet-tile.leaflet-tile-loaded
	border-radius: 0%  !important
	border: none  !important
```

3. Save your edits.

## Part 7: Test your new add-on

1. Refresh your mural, click the online member's icon, and hover over your name, your browser will prompt you to allow or block location services.
2. If you choose to allow it, you will see your new map & coordinate marker after a few moments.

![Opening member profile and map appears](/img/user-map.gif)

## Wrap-Up 🎉

This add-on is a quick proof of concept. There are a plethora of ways we can improve upon it and add even more features. For example: multiple user locations, exporting locations, and so on. Think of all the options!

> Hey! Do you have an idea for a cool MURAL add-on? An icebreaker? A multiplayer game? We can’t wait to see what you come up with! Feel free to share it with us on Twitter [@MURAL](https://twitter.com/MURAL)!

##### Full code snippet of the location.js file below:

```javascript
import React from  'react';
// @ts-ignore
import './leaflet/leaflet.js';
import './leaflet/leaflet.css';
import './style.sass'; // If you choose to add some custom styles

function  MemberMap() {
	// If geolocation services are available
	if ('geolocation' in navigator')
	
		// Prompts user to allow or block location.
		navigator.geolocation.getCurrentPosition(function(position) {
		
			// Assign variables to latitude and longitude coordinates
			const { latitude } = position.coords;
			const { longitude } = position.coords;
			
			// Put those variables into an array
			const coords = [latitude, longitude];
			
			// Custom map marker icon
			let memberLocation = L.icon({
			iconUrl:
				'<https://assets-global.website-files.com/5ddd9c3f2186308353fe682d/5e93cf2b73fd9d8622880a1d_favicon.png',>
			iconSize: [20,  20],
			});
			
			// Create map variable which will include the div id 'memberMap'
			// Remove zoom buttons
			// Set user coordinates & starting zoom level.
			let map = L.map('memberMap', { zoomControl:  false }).setView(coords,  6);
			
			// Maps have many options for tile types. This users the Open Street map tiles
			L.tileLayer('<https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',> {
				maxZoom:  6,
			}).addTo(map);
			
			// Initializes custom map marker to the coordinates
			L.marker(coords, {
				icon: memberLocation,
			}).addTo(map);
			
		});
		return (
			<div id="memberMap"className="mural-member-map" />
		);
	} else {
		console.log('Sorry this feature is currently unavailable');
	}
}

export  default MemberMap;
```