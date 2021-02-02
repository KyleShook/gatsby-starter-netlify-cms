---
templateKey: blog-post
title: Code snippet test2
date: 2021-02-02T21:31:11.066Z
description: testing code snippets
featuredpost: false
featuredimage: /img/addonsfolder.jpg
tags:
  - code
---
MURAL enables innovative teams to think and collaborate visually to solve important problems. People benefit from MURAL’s speed and ease of use in creating diagrams, which are popular in design thinking and agile methodologies, as well as tools to facilitate more impactful meetings and workshops.

```javascript
function MemberMap() {
	// If geolocation services are available
	if ('geolocation' in navigator') {
		// Next step
}
```

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