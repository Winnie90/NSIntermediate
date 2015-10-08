---
layout: default
---
# NSBeginner - Create Your Own Google Maps Style Callout

![Example of the Google Maps Style Callout][example]

In this tutorial we will be showing you how to create a Google Maps style callout for your pins in native iOS maps. Showing location based information to users can be a challenge. We really liked the style and functionality of the Google maps application and we thought we would show you how to replicate it in your own application.  

* In [NSBeginner][LinkNSBeginner] we will show you how to show a map in your application and then pin data to it.
* In [NSIntemediate][LinkNSIntermediate] we will show you how to create the GMS Callout and populate it with your data. We will also show you how to link the annotation data to the callout data.
* In [NSExtras][LinkNSExtras] we will show you how to animate the callout on screen, show the user's location, show the distance to your annotations and open directions to your annotations in Apple Maps. 

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

We now need to set up a connection to the view controller. Click the Assistant Editor button which should pull up the side by side view with the storyboard in the left pane and your view controller code on the right. Hold down Ctrl and click-drag from the map view into the @interface of the view controller. You will then want to name the outlet mapView. You will get a few issues but don't worry we will be fixing that soon.

![Setup the map view connections][MapViewConnection]

We also need to set the delegate to the map view so that the view controller responds to the users' actions with the map view. Hold down Ctrl and click-drag from the map view this time to the **ViewController** in the Document Outline

![Setup the delegate connections][DelegateConnection]

### Map Kit Framework

To fix those pesky errors go to the project pane, go to **Linked Frameworks and Binaries** and click the Add button. Search for the MapKit Framework and click Add. Now that the MapKit framework has been imported all of the native map's classes will be available to you.

![Add the MapKit Framework][AddMapKitFramework]

You will now be able to import the MapKit into your **ViewController**. Switch to **ViewController** in the Navigator. Add the MapKit header above the "ViewController.h" header import. Most style guides advise importing frameworks above the other header files.

![Switch to the main view controller][MapViewControllerProjectNavigator]

```objective-c
//1
#import <MapKit/MapKit.h>

#import "ViewController.h"
```

If you **Build and Run** you will be able to see your very own map view in the simulator. 

![Working Map View][WorkingMapView]

### Adding Pins

A map view is useless without it showing any data, so lets add some. We are going to add the location of your friends on the map. Most map view tutorials include the data in the view controller, we wanted to abstract it out properly so that you can change the data without it affecting the MapView implementation. This should give you more flexibility if you want to expand on this tutorial later.

### Person Class

Each friend will be handled using a Person class. You can adjust the naming to match your data. To create a new class right click on your main group name and select **New File**. Then select **Cocoa Touch Class** you will want to name it Person.

![Name your class person][NamePerson]

You want to set up the following code in your newly generated **Person.h** file.

```objective-c
//1
#import <MapKit/MapKit.h>
//2
#import <UIKit/UIKit.h>

@interface Person : NSObject

//3
@property (strong, nonatomic) NSString *name;
@property (strong, nonatomic) NSString *activity;
@property (strong, nonatomic) UIImage *avatar;
@property (assign, nonatomic) CLLocationCoordinate2D location;

//4
- (instancetype)initWithName:(NSString*)name activity:(NSString*)activity avatar:(UIImage*)avatar location:(CLLocationCoordinate2D)location;

@end
```
1. You need to import the MapKit file to store a location with the Person. 
2. Change the Foundation #import to UIKit so we can use UIImage. 
3. Create some properties in the interface file to store details about your friends. You need to store a name and an activity as NSStrings. An avatar as a UIImage and location as a CLLocationCoordinate2D. 
4. We will also create a custom initializer so you can create a Person with a name, activity, avatar and location.

```objective-c
#import "Person.h"

@implementation Person

//1
- (instancetype)initWithName:(NSString*)name activity:(NSString*)activity avatar:(UIImage*)avatar location:(CLLocationCoordinate2D)location
{
    self = [super init];
    if (self) {
        self.name = name;
        self.activity = activity;
        self.avatar = avatar;
        self.location = location;
    }
    return self;
}

@end
```

In your main file, you need to initialize a Person with the properties you pass it.

### MapViewModel

We will now use a model class to create and handle the Person data for our map view. In a real application this would be used to fire off requests to a network handler and handle the responses to pass to the map view. 

Create a new class in the same way you created the Person class and name it MapViewModel. In the header file add the following public method.

```objective-c
#import <Foundation/Foundation.h>

@interface MapViewModel : NSObject

- (NSArray*)retrieveMapData;

@end
```

You will need to import some images into the project to use as your friends' avatars. Click on **Assets.xcassets** and then drag and drop your images into the assets collection. You will need to rename them for each of your friends. 

![Import images into the project][Images]

You can then create the main file to MapViewModel.

```objective-c
#import "MapViewModel.h"
//1
#import "Person.h"

@implementation MapViewModel

- (NSArray*)retrieveMapData{
    //2,3,4
    Person *steve = [[Person alloc] initWithName:@"Steve" activity:@"Dog Walking" avatar:[UIImage imageNamed:@"steveAvatar"] location:CLLocationCoordinate2DMake(54, -2)];
    Person *woz = [[Person alloc] initWithName:@"Woz" activity:@"Getting Coffee" avatar:[UIImage imageNamed:@"wozAvatar"] location:CLLocationCoordinate2DMake(54.02, -2)];
    Person *tim = [[Person alloc] initWithName:@"Tim" activity:@"On the Train" avatar:[UIImage imageNamed:@"timAvatar"] location:CLLocationCoordinate2DMake(54.04, -2)];

    //5
    return [[NSArray alloc] initWithObjects:steve, woz, tim, nil];
    
}

@end
```
1. Import the Person header into your main file, so you can use your custom initializer.
2. Create people to display on the map using your custom initializer. We can give it the name and activity using string literals. 
3. Use `UIImages'` method `imageNamed` to retrieve your image from the assets folder, just make sure the names match.
4. Create locations using `CLLocationCoordinate2DMake`. This is represented by two floats one for latitude and one for longitude.
5. Return an array of your People objects.

### Import your data

Switch to your **ViewController.m** file.

```objective-c
#import "ViewController.h"
//1
#import "MapViewModel.h"

@interface ViewController ()

@property (weak, nonatomic) IBOutlet MKMapView *mapView;
//2
@property (strong, nonatomic) NSArray *mapData;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    //3
    self.mapData = [[MapViewModel new] retrieveMapData];
}
```
1. Import your map view model.
2. Add an array to store your friends' data.
3. Create a new **MapViewModel** and then retrieve the map data from it. This can then be stored in your array property.

### Custom Annotations

You can either use a standard MKPinAnnotationView or for more options going forward you can customise your own annotation. Create a new class called PersonAnnotation and make it a subclass of NSObject.

```objective-c
//1
#import <MapKit/MapKit.h>
#import "Person.h"

//2
@interface PersonAnnotation : NSObject <MKAnnotation>

//3
@property (nonatomic, assign) CLLocationCoordinate2D coordinate;
@property (nonatomic, copy) NSString *title;
@property (nonatomic, copy) NSString *subtitle;
@property (nonatomic, assign) NSInteger index;

//4
- (id)initWithPerson:(Person*)person;

@end
```
1. To subclass an annotation you will need to import Mapkit and we want create an annotation with a Person so this neeeds to be imported.
2. The annotation needs to conform to the `MKAnnotation` protocol.
3. You need to subclass the annotations' properties.
4. We want to store the `mapData` index with the annotation for `CollectionView` functionality in [NSIntemediate][LinkNSIntermediate]. 
5. You can then create your own initialization method.

Switch to the **PersonAnnotation.m** file and star implementing your new annotation class.
```objective-c
#import "PersonAnnotation.h"

@implementation PersonAnnotation
//1
- (id)initWithPerson:(Person *)person atIndex:(NSInteger)index{
    if ((self = [super init])) {
        _title = person.name;
        _subtitle = person.activity;
        _coordinate = person.location;
        _index = index;
    }
    return self;
}

//2
- (NSString *)title {
    return _title;
}

- (NSString *)subtitle {
    return _subtitle;
}

- (CLLocationCoordinate2D)coordinate {
    return _coordinate;
}

@end
```
1. Your custom initializer will allow you to pass a Person object to the annotation and initilaise it.
2. To conform to the `MKAnnotation` protocol you will need to provide get methods for the title, subtitle and coordinate.

##Finishing off

You can now add your own custom annotations to the map. Switch to **ViewController.m** and add the following code.


```objective-c
//1
#import "Person.h"
#import "PersonAnnotation.h"

- (void)viewDidLoad {
    ...
    //2
    [self addMarkersToMap];
}

#pragma mark Private

-(void)addMarkersToMap {
    NSInteger index = 0;
    //3
    for(Person *person in self.mapData){
    	//4
        PersonAnnotation *annotation = [[PersonAnnotation alloc] initWithPerson:person atIndex:index];
        [self.mapView addAnnotation:annotation];
        index++;
    }
    [self.mapView showAnnotations:self.mapView.annotations animated:YES];
    
}
```
1. You need to import the `Person.h` and `PersonAnnotation.h` header files.
2. On `viewDidLoad` you can call `addMarkersToMap`.
3. In the `addMarkersToMap` method you need to loop through your map data. Cast the data to `Person`. 
4. You can then initialize your custom annotation with the person. We want to store the `mapData` index with the annotation for CollectionView functionality in [NSIntemediate][LinkNSIntermediate]. 
5. Add the annotation to the map view.
6. Once all of the annotations have been added you can move maps viewport to show them.

![Added your custom annotations to the MKMapView][NSBeginnerFinal]

Your project is now set up to follow along with the [NSIntemediate][LinkNSIntermediate] tutorial.

[example]: {{site.baseurl}}/images/2015-10-04/00-Example.png "Example of the Google Maps Style Callout"
[SingleViewApplication]: {{site.baseurl}}/images/2015-10-04/01-SingleViewApplication.png "Create a new single view application"
[ProjectName]: {{site.baseurl}}/images/2015-10-04/02-ProjectName.png "Name your project"
[ProjectNavigator]: {{site.baseurl}}/images/2015-10-04/03-ProjectNavigator.png "Select Main.storyboard from the project navigator"
[MapViewSetup]: {{site.baseurl}}/images/2015-10-04/04-MapViewSetup.png "Drag map kit view onto view controller"
[MapViewConstraints]: {{site.baseurl}}/images/2015-10-04/05-MapViewConstraints.png "Setup the map view constraints"
[MapViewConnection]: {{site.baseurl}}/images/2015-10-04/06-MapViewConnection.png "Setup the map view connections"
[DelegateConnection]: {{site.baseurl}}/images/2015-10-04/07-DelegateConnection.png "Setup the delegate connections"
[AddMapKitFramework]: {{site.baseurl}}/images/2015-10-04/08-AddMapKitFramework.png "Add the MapKit Framework"
[MapViewControllerProjectNavigator]: {{site.baseurl}}/images/2015-10-04/09-ProjectNavigator.png "Switch to the main view controller"
[WorkingMapView]: {{site.baseurl}}/images/2015-10-04/10-WorkingMapView.png "Working Map View"
[NewFile]: {{site.baseurl}}/images/2015-10-04/11-NewFile.png "New File"
[CocoaTouchClass]: {{site.baseurl}}/images/2015-10-04/12-CocoaTouchClass.png "New Cooca Touch Class"
[NamePerson]: {{site.baseurl}}/images/2015-10-04/13-NamePerson.png "Name Person"
[Images]: {{site.baseurl}}/images/2015-10-04/14-Images.png "Import images into the project"
[NSBeginnerFinal]: {{site.baseurl}}/images/2015-10-04/15-NSBeginnerFinal.png "Added your custom annotations to the MKMapView"

[LinkNSBeginner]: {{site.baseurl}}/GMCallout-NSBeginner
[LinkNSIntermediate]: {{site.baseurl}}/GMCallout-NSIntermediate
[LinkNSExtras]: {{site.baseurl}}/GMCallout-NSExtras