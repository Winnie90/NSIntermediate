---
layout: default
---
# Create Your Own Google Maps Style Callout

![Example of the Google Maps Style Callout][example]

In this tutorial we will be showing you how to create a Google Maps style callout for your pins in native iOS maps. Showing location based information to users can be a challenge. We really liked the style and functionality of the Google maps application and we thought we would show you how to replicate it in your own application. 

* In [NSBeginner][LinkNSBeginner] we will show you how to show a map in your application and then pin data to it.
* In [NSIntemediate][LinkNSIntermediate] we will show you how to create the GMS Callout and populate it with your data.
* In [NSExtras][LinkNSExtras] we will show you how to animate the callout on screen, show the user's location 

## NSBeginner
* In [NSBeginner][LinkNSBeginner] we will show you how to show a map in your application and then pin data to it.
* If you want to skip to the GMS callout part then check out [NSIntermediate][LinkNSIntermediate].

### Setup
This tutorial was made using XCode7 let us know if there are any problems with older versions. 

Open XCode7 and go to:

`File->New project->Application->SingleViewApplication`
![Create a new single view application][SingleViewApplication]

You can then name your project:
![Create a new single view application][ProjectName]

XCode will setup a basic project for you with a single view controller. 

### Storyboard
Click on your main storyboard file -> **Main.storyboard**

![Select Main.storyboard from the project navigator][ProjectNavigator]

Drag a **Map Kit View** from the **Objects Library** in the right hand pane onto the main view controller. Click on the map view and drag it into the corners of the view controller.

![Drag map kit view onto view controller][MapViewSetup]

To have the map view appear full size on the users' screen you will need to set the constraints. Select the pin tool in the bottom right of the storyboard editor's main pane. You can do this by selecting the four constraints around the view on the pin tool and selecting the **Add Constraints** button.

![Setup the map view constraints][MapViewConstraints]


[example]: /images/2015-10-04/00-Example.png "Example of the Google Maps Style Callout"
[SingleViewApplication]: /images/2015-10-04/01-SingleViewApplication.png "Create a new single view application"
[ProjectName]: /images/2015-10-04/02-ProjectName.png "Name your project"
[ProjectNavigator]: /images/2015-10-04/03-ProjectNavigator.png "Select Main.storyboard from the project navigator"
[MapViewSetup]: /images/2015-10-04/04-MapViewSetup.png "Drag map kit view onto view controller"
[MapViewConstraints]: /images/2015-10-04/05-MapViewConstraints.png "Setup the map view constraints"

[LinkNSBeginner]: /2015/10/04/GMCallout-NSBeginner
[LinkNSIntermediate]: /2015/10/04/GMCallout-NSIntermediate
[LinkNSExtras]: /2015/10/04/GMCallout-NSExtras