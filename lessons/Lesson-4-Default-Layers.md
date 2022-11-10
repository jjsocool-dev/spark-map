When you instantiate the map, you can choose to have a default layer loaded. For this map, we want to have the sidewalks that are being mapped added as a default layer.

In the default branch of the app, a layer called **STL Buildings** is added.

This lesson will show you how to add a default layer.

### Step 1
Using the knowledge gained last time, write a query in Overpass Turbo and download sidewalks as a GeoJSON. Then, save that GeoJSON in the data folder. You can check that it downloaded correctly by viewing it in GitHub.

### Step 2 
Add a link to the layer. You can see an example for this if you search for `var buildingsURL = ` in the `index.html` file. Follow the same pattern to add your sidewalks layer. You can name it anything, but it is good practice tobe descriptive, so feel free to call it `sidewalksURL`.

### Step 3
Add the layer to the map. You can see an example by searching for `var buildingsLayer =` in `index.html`. You are going to use the `addGJLayer()` function, which can be found in `spark.js`. Let's quickly break this function down:

```function addGJLayer(GJson) {
// Add buildings layer
var newLayer = L.geoJSON(null, {
	style: addStyle,
	onEachFeature: function(feature, layer) {
		addPopUp(feature,layer);
	}
});
```

This function sets a variable called `newLayer` that adds a GeoJSON to the Leaflet map (that is what `L.geoJSON()` is doing as an "out of the box" function of Leaflet. You will notice that we are giving paramaeters to the function, including `style` and `onEachFeature`. Style is using _another function_ to set the style (we will get to this in a future step). `onEachFeature` is setting the rule for what should happen when each feature in the map is added, and we are using the `addPopup` function to inform that when a user clicks the feature, a pop up should appear. Simple!

### Step 4
Add the layer to the `overlay maps` group. This will add the layer to the table of contents so you can select it "on and off". Find this block of code and add your layer using the same template:
```
		var overlayMaps = {
			"STL Buildings": buildingsLayer
		};
```
But - be sure to add a comma after the first item! So it may look something like this...
```
		var overlayMaps = {
			"STL Buildings": buildingsLayer;
      "STL Sidewalks": sidewalksLayer
		};
```
The semi-colon tells the code that there is another item in the list.

### Step 5
Moving onto style...we dont' have one for sidewalks!

First we need to create the style for the sidewalks. In `spark.js`, look at any of the examples starting around Line 115 (`buildingsStyle`) to add the color (what color will the feature be), the weight (how thick will the line be), and the opacity (how transparent will the feature be).

Now let's make sure anything that is a sidewalk is rendered correctly. In `spark.js`, you will edit the addStyle code. You will need to add the last if statement (we will review next time) to include footways. If you are only adding sidewalk styles and not other footways, then change the last line from `else {return footwayStyle}` to `else {return otherStyle}`

```
function addStyle(feature, layer){
if (feature.properties.building) {
if (feature.properties.building == 'house') {return houseStyle}
else if (feature.properties.building == 'apartments') {return apartmentsStyle}
else if (feature.properties.building == 'school') {return schoolStyle}
else if (feature.properties.building == 'garage') {return garageStyle}
else if (feature.properties.building == 'garages') {return garagesStyle}
else if (feature.properties.building == 'residential') {return residentialStyle}	
else if (feature.properties.building == 'service') {return serviceStyle}
else {return buildingsStyle}
} else {
return otherStyle
}

if (feature.properties.highway){
if (feature.properties.highway == 'railway') {return railwayStyle}
else {return OwaysStyle}
}
else {
return otherStyle
}

if (feature.properties.footway){
if (feature.properties.footway == 'sidewalk') {return sidewalkSyle}
else {return footwayStyle}
}
else {
return otherStyle
}


}
```
