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
In this NSIntermediate tutorial we will be adding our very own Google Maps style callout to our map view. We will be carrying on with the project from the [NSBeginner][LinkNSBeginner] tutorial. You finished the [NSBeginner][LinkNSBeginner] tutorial with your custom annotations being displayed on the map. Or if you've come straight here and want a refresher on mapviews and custom annotations check out [NSBeginner][LinkNSBeginner].

![Added your custom annotations to the MKMapView][NSBeginnerFinal]

### PersonCalloutCollectionViewCell

First we need to add the code for our very own collection view cell which is going to be populated with our Person's data. 

![Add your own collection view cell to display the person's data][PersonCalloutCollectionViewCell]

You need to create a new class which is a subclass of UICollectionViewCell. Name it **PersonCollectionViewCell**. For a lot of this you could use a standard UICollectionViewCell however we want you to be able to customise it in the future. Once this has been generated select **PersonCollectionViewCell.h** and add the following:

```objective-c
//1
#import <UIKit/UIKit.h>

//2
@interface PersonCollectionViewCell : UICollectionViewCell

//3
@property (weak, nonatomic) IBOutlet UIImageView *avatarImageView;
@property (weak, nonatomic) IBOutlet UILabel *titleLabel;
@property (weak, nonatomic) IBOutlet UILabel *subtitleLabel;

@end
```

1. Make sure UIKit is imported, as we need UIKit's property types such as `UIImageView` and `UILabel`.
2. Make sure your cell subclasses `UICollectionViewCell`.
3. Add properties to set with your person's data.

You can leave the **PersonCollectionViewCell.m** file as it's auto-generated shell.

### Design UICollectionView

We want to start by designing our callout view in **Main.storyboard** which will display our Person's data when an annotation is tapped on. You should see your map view on your single view controller. 

![Add a UICollectionView on top of your view controller][UICollectionView]

Drag a **UICollectionView** on top of your view controller. Set its height to 100px, it's width to the same as that of the view controller. In our case it was 600px.

![Set the UICollectionView attributes to the following][UICollectionViewProperties]

Click on the **UICollectionView** and then the **Attributes Inspector**, set the following attributes:

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

Click on the **Connections Inspector** for the **UICollectionView**. Click on the plus next to `dataSource` and drag it to your `ViewController` either in the **Document Outline** or the main storyboard pane. 

**If you try and Build and Run now your app will crash as we have not made our ViewController conform to UICollectionViewDelegate or UICollectionViewDataSource yet.**

### Design UICollectionViewCell

![Add assets to the UICollectionViewCell][UICollectionViewCell]

Click on the **UICollectionviewCell** and set the reuse identifier to "PersonCollectionViewCell". You need to reference this in code so make sure you copy it directly from your storyboard.

You can now drag assets onto your cell, you can customise these out how you want, we added:

* UIImageView for the persons' avatar. (Contrained to the top and left with a width and height of 84px)
* UILabel for the persons' name. (Contrained 8px to the top, 8px leading to the image view, height at 21px and trailing to the superview >= 8px)
* UILabel for the persons' activity. (Constrained 8px to the label above, 8px leading to the image view, bottom to the superview >= 8px, trailing to the superview >= 8px)
* We left some room on the right for some distance metrics in [NSExtras][LinkNSExtras]

### Hook up PersonCollectionViewCell

We now need to hook up the assets to the outlet properties in your custom `UICollectionViewCell` class `PersonCollectionViewCell`. While clicking on your `UICollectionViewCell` in the storyboard open up the **Identities Inspector**.

![In the identities inspector set the class to PersonCollectionViewCell][CellIdentityInspector]

In the **Identities Inspector** set the class to `PersonCollectionViewCell`. In the **Connections Inspector** you can now hook up the Outlets to the assets in the storyboard. 

![In the Connections Inspector you can now hook up the Outlets to the assets in the storyboard][CellConnectionsInspector]

Click the plus next to the outlet and drag it onto the asset you want to connect it to. Do this for all three of your outlet properties avatarImageView, titleLabel and subtitleLabel.

### MapViewController Code

In your **ViewController.h** file you will need to add some code now to pass your Person's data through to the UICollectionView and UICollectionViewCell.

```objective-c
//1
#import "PersonCalloutCollectionViewCell.h"

//2
@interface ViewController () <UICollectionViewDelegate, UICollectionViewDataSource>
```

1. Import your custom collection view cell.
2. Make `ViewController` conform to the `UICollectionViewDelegate` and `UICollectionViewDataSource` protocols.

```objective-c
...
#pragma mark UICollectionViewDataSource

//1
-(NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section {
    return [self.mapData count];
}

//2
-(UICollectionViewCell*)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    //3
    Person *person = self.mapData[indexPath.row];
    //4
    PersonCollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"PersonCollectionViewCell" forIndexPath:indexPath];
    //5 
    cell.titleLabel.text = person.name;
    cell.subtitleLabel.text = person.activity;
    cell.avatarImageView.image = person.avatar;
    //6
    return cell;
}
```
You can now add the `UICollectionViewDataSource` protocol methods to provide data to your `UICollectionView`.
1. You need to tell the collection view how many items are in the section. Return the number of items in your mapData array.
2. You now need to provide a cell for the index path given.
3. You can retrieve a person object from your `mapData` array using the index paths row parameter.
4. You can create a `PersonCollectionViewCell` by using the **reuse identifier** we set up in the storyboard. Make sure they are the same.  
5. You can now set the cell's outlet properties with the properties of your `Person` object.
6. Finally return the cell.

You will also need to implement the UICollectionViewDelegate method to make the cell fit to the correct bounds.

```objective-c
...
#pragma mark UICollectionViewDelegate

//1
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout sizeForItemAtIndexPath:(NSIndexPath *)indexPath
{
    //2
    return CGSizeMake(CGRectGetWidth(self.view.frame), 100.0);
}
```

1. This delegate method lets you return a custom size for each cell given an `indexPath`.
2. We want to return the same size for every cell. You can use `CGRectGetWidth(self.view.frame)` to get the width of the superviews frame. We set the height to 100 as it's the height of our UICollectionView.

Whew! That was a lot of setup but if you build and run now you should get a working `UICollectionView` with your own `PersonCollectionViewCell` in it. 

You will notice however that when you select an annotation it doesn't show the correct person in the `UICollectionView`, so let's fix that.

##Linking Annotations to CollectionViewCells

Back in your **ViewController.m** file: 

```objective-c
//1
@interface ViewController () <MKMapViewDelegate, UICollectionViewDelegate, UICollectionViewDataSource>

//2
@property (weak, nonatomic) IBOutlet UICollectionView *collectionView;

```
1. Make the ViewController conform to the `MKMapViewDelegate` protocol.
2. Create an outlet propety to your UICollectionView and hook them up in your storyboard.

Now we want to add the code that scrolls to the correct Person Cell when and Annotation is selected.

```objective-c
//1
- (void)mapView:(MKMapView *)mapView didSelectAnnotationView:(MKAnnotationView *)view {
    //2
    PersonAnnotation *annotation = (PersonAnnotation*)view.annotation;
    //3
    [self showPersonAtIndex:annotation.index];
}

- (void)showPersonAtIndex:(NSInteger)index{
    //4
    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
    //5
    [self.collectionView scrollToItemAtIndexPath:indexPath atScrollPosition:UICollectionViewScrollPositionCenteredHorizontally animated:YES];
}
```

1. Implement the `MKMapViewDelegate` protocol method `didSelectAnnotationView`.
2. Cast the received annotation to your own custom annotation class.
3. We can now use the index of that annotation to show the right collection view cell.
4. Create and index path from the passed index.
5. As our collection view uses the same data as our annotations the index will be the same across both so we can scroll to the item at that index path.

If you Build and Run now you will be able to select an annotation and your UICollectionView will scroll to the correct Person.

##Linking CollectionViewCells to Annotations

When we page through our UICollectionView we want the correct annotation to be selected on the map.

To do this we need to conform to some UICollectionViewDelegate methods

If you want to add some polish to your new Google Maps style callout, head to [NSExtras][LinkNSExtras]. Here we will show you how to animate the callout on screen, show the user's location, show the distance to your annotations and open directions to your annotations in Apple Maps. 

[example]: {{site.baseurl}}/images/2015-10-04/00-Example.png "Example of the Google Maps Style Callout"
[NSBeginnerFinal]: {{site.baseurl}}/images/2015-10-04/15-NSBeginnerFinal.png "Added your custom annotations to the MKMapView"
[UICollectionView]: {{site.baseurl}}/images/2015-10-04/16-UICollectionView.png "Add a UICollectionView on top of your view controller"
[UICollectionViewProperties]: {{site.baseurl}}/images/2015-10-04/17-UICollectionViewProperties.png "Change the UICollectionViewProperties"
[UICollectionViewConstraints]: {{site.baseurl}}/images/2015-10-04/18-UICollectionViewConstraints.png "Change the UICollectionView Constraints"
[UICollectionViewSizeInspector]: {{site.baseurl}}/images/2015-10-04/19-UICollectionViewSizeInspector.png "Change the UICollectionView cell size and minimum spacing"
[UICollectionViewCell]: {{site.baseurl}}/images/2015-10-04/20-UICollectionViewCell.png "Add assets to the UICollectionViewCell"
[PersonCollectionViewCell]: {{site.baseurl}}/images/2015-10-04/21-PersonCalloutCollectionViewCell.png "Add your own collection view cell to display the person's data"
[CellIdentityInspector]: {{site.baseurl}}/images/2015-10-04/22-CellIdentityInspector.png "In the identity inspector set the class to PersonCollectionViewCell"
[CellConnectionsInspector]: {{site.baseurl}}/images/2015-10-04/23-CellConnectionsInspector.png "In the Connections Inspector you can now hook up the Outlets to the assets in the storyboard"
[UICollectionViewOutlets]: {{site.baseurl}}/images/2015-10-04/24-UICollectionViewOutlets.png "In the Connections Inspector you can hook up the Outlets to the ViewController"
[WorkingCollectionView]: {{site.baseurl}}/images/2015-10-04/25-WorkingCollectionView.png "You're Collection View should now be working but the pins aren't connected to the collection view."


[LinkNSBeginner]: {{site.baseurl}}/GMCallout-NSBeginner
[LinkNSIntermediate]: {{site.baseurl}}/GMCallout-NSIntermediate
[LinkNSExtras]: {{site.baseurl}}/GMCallout-NSExtras