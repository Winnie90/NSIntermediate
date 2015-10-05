---
layout: default
---
# Create Your Own Google Maps Style Callout - NSIntermediate

![Example of the Google Maps Style Callout][example]

In this tutorial we will be showing you how to create a Google Maps style callout for your pins in native iOS maps. Showing location based information to users can be a challenge. We really liked the style and functionality of the Google maps application and we thought we would show you how to replicate it in your own application. 

* In [NSBeginner][LinkNSBeginner] we will show you how to show a map in your application and then pin data to it.
* In [NSIntemediate][LinkNSIntermediate] we will show you how to create the GMS Callout and populate it with your data.
* In [NSExtras][LinkNSExtras] we will show you how to animate the callout on screen, show the user's location, show the distance to your annotations and open directions to your annotations in Apple Maps. 

## NSIntermediate
In this NSIntermediate tutorial we will be adding our very own Google Maps style callout to our map view. We will be carrying on with the project from the [NSBeginner][LinkNSBeginner] tutorial. You finished the [NSBeginner][LinkNSBeginner]tutorial with your custom annotations being displayed on the map. Or if you've come straight here and want a refresher on mapviews and custom annotations check out [NSBeginner][LinkNSBeginner].

![Added your custom annotations to the MKMapView][NSBeginnerFinal]

### Design UICollectionView

We want to start by designing our callout view in **Main.storyboard** which will display our Person's data when an annotation is tapped on. You should see your map view on your single view controller. 

![Add a UICollectionView on top of your view controller][UICollectionView]

Drag a **UICollectionView** on top of your view controller. Set its height to 100px, it's width to the same as that of the view controller. In our case it was 600px.

![Set the UICollectionView attributes to the following][UICollectionViewProperties]

Click on the **UICollectionView** and then the attributes inspector, set the following attributes:

* Set the flow direction to horizontal, as we want to swipe left/right to view more information.
* Set Paging Enabled to true (ticked) as we want the scroll view to stop on each piece of data rather than be a continuous scroll.
* Set Shows Horizontal Indicator to false (unticked), this can be left enabled but we think it looks better when hidden.
* Set Shows Vertical Indicator to false (unticked).
* Set the background to white color (not shown), this can be set to match your colour scheme just be aware the cell's background is clear by default so if left clear you will just see a black bar at the bottom of your screen.

![Change the UICollectionView cell size and minimum spacing][UICollectionViewSizeInspector]

In the size inspector set the **Cell Size** to the same as the **UICollectionView**. Set the **Min Spacing** to 0 to avoid spacing between the cells. We want the cell to take up the whole of the collection view to get that GM callout effect. 

![Add the UICollectionView Constraints][UICollectionViewConstraints]

Set the constraints of the UICollection view to:

* Trailing space to `superview = -20`
* Leading space to `superview = -20`
* Height Equals `100`
* Bottom Space to `Bottom Layout Guide` (0)

### Design UICollectionViewCell

![Add assets to the UICollectionViewCell][UICollectionViewCell]

Click on the **UICollectionviewCell** and set the reuse identifier to "PersonCalloutColletionViewCell". You need to reference this in code so make sure you copy it directly from your storyboard.

You can now drag assets onto your cell, you can chustomise these out how you want, we added:

* UIImageView for the persons' avatar. (Contrained to the top and left with a width and height of 84px)
* UILabel for the persons' name. (Contrained 8px to the top, 8px leading to the image view, height at 21px and trailing to the superview >= 8px)
* UILabel for the persons' activity. (Constrained 8px to the label above, 8px leading to the image view, bottom to the superview >= 8px, trailing to the superview >= 8px)
* We left some room on the right for some distance metrics in [NSExtras][LinkNSExtras]

If you want to add some polish to your new Google Maps style callout, head to [NSExtras][LinkNSExtras]. Here we will show you how to animate the callout on screen, show the user's location, show the distance to your annotations and open directions to your annotations in Apple Maps. 

[example]: /images/2015-10-04/00-Example.png "Example of the Google Maps Style Callout"
[NSBeginnerFinal]: /images/2015-10-04/15-NSBeginnerFinal.png "Added your custom annotations to the MKMapView"
[UICollectionView]: /images/2015-10-04/16-UICollectionView.png "Add a UICollectionView on top of your view controller"
[UICollectionViewProperties]: /images/2015-10-04/17-UICollectionViewProperties.png "Change the UICollectionViewProperties"
[UICollectionViewConstraints]: /images/2015-10-04/18-UICollectionViewConstraints.png "Change the UICollectionView Constraints"
[UICollectionViewSizeInspector]: /images/2015-10-04/19-UICollectionViewSizeInspector.png "Change the UICollectionView cell size and minimum spacing"
[UICollectionViewCell]: /images/2015-10-04/20-UICollectionViewCell.png "Add assets to the UICollectionViewCell"

[LinkNSBeginner]: GMCallout-NSBeginner
[LinkNSIntermediate]: GMCallout-NSIntermediate
[LinkNSExtras]: GMCallout-NSExtras