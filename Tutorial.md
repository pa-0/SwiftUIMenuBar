# Create a macOS Menu Bar Application Using SwiftUI

>Learn how to create a menu bar application using SwiftUI

Written by: [Aaron Wright](https://medium.com/@acwrightdesign?source=post_page---byline--54572a5d5f87--------------------------------)
6 min read
Dec 18, 2019

![](https://miro.medium.com/v2/resize:fit:1400/0*ikJCRGppb3H5LZJo)

<sup>Photo by [Adam Wilson](https://unsplash.com/@fourcolourblack?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)</sup>

Menu bar applications are among some of the more useful applications you can install for macOS. The ability of menu bar apps to hover above other open applications make them great for quick tasks or for quick access to data. There are even websites dedicated to cataloging [menu bar apps](https://macmenubar.com/). In this tutorial we will create the skeleton for a menu bar application in SwiftUI.

![](https://miro.medium.com/v2/resize:fit:1400/1*S_R0X5I2Dy6aGucoSbIoJw.png)

The macOS menu bar

## **Getting Started**

To start, open Xcode and choose “Create a new Xcode project” from the main menu or choose File -> New Project from the File menu. In the templates window select the macOS tab and choose “App”. Next, give your project a name. Make sure you choose Swift for the language and SwiftUI in the dropdown for “User Interface” if it is not already selected. Uncheck any of the checkboxes at the bottom as we will not be using any of these items. Finally, choose a location for your project, optionally create a Git repository, and then choose “Create”.

![](https://miro.medium.com/v2/resize:fit:1400/1*ySj0bm78c7SRYd8l-AQsYQ.png)

Creating a new Xcode project

### **A Little Housekeeping**

The Xcode project template we are using needs a bit of cleanup before we can get started. Navigate to the `AppDelegate.swift` file in the Project navigator and you should see something similar to this:

Remove all the code in `applicationDidFinishLaunching` after the initialization of the `contentView` as we will not be needing it. You should end up with something like this:

We are now ready to start adding code to set up a menu bar application!

## **Creating a Popover**

We will be creating a `NSPopover` instance to contain our UI for our application. This could also be done using a `NSWindow` but for this application we will focus on using `NSPopover` to display our content.

Create a property in `AppDelegate` to store our popover instance:
    
    var popover: NSPopover!
    
Next, we will initialize our popover. Add the following to the bottom of applicationDidFinishLaunching after the initialization of the ContentView:
     
    _// Create the popover_let popover = NSPopover()popover.contentSize = NSSize(width: 400, height: 400)  
    popover.behavior = .transient  
    popover.contentViewController = NSHostingController(rootView: contentView)self.popover = popover
    
Your AppDelegate class should now look something like this:
    
We have now created the popover and assigned our SwiftUI ContentView to the popover’s contentViewController. This is a good start, but we still need to create a status item for the menu bar so we can show our popover when the menu item is activated…
    
## **Creating the Status Item**
    
In order to show our popover, we need two things: A NSStatusItem to add a menu item to the menu bar, and a method to handle the activation of the status item.
    
Add the following method after applicationDidFinishLaunching in your AppDelegate file:
    
    _// Create the status item_@objc func togglePopover(_ sender: AnyObject?) {  
         if let button = self.statusBarItem.button {  
              if self.popover.isShown {  
                   self.popover.performClose(sender)  
              } else {  
                   self.popover.show(relativeTo: button.bounds, of: button, preferredEdge: NSRectEdge.minY)  
              }  
         }  
    }
    
This method will toggle the popover open or closed when the status item is activated (clicked). However, we still need to create the NSStatusItem and assign this method as the handler for it’s action. Add the following at the bottom of the applicationDidFinishLaunching method:
    
    self.statusBarItem = NSStatusBar.system.statusItem(withLength: CGFloat(NSStatusItem.variableLength))if let button = self.statusBarItem.button {  
         button.image = NSImage(named: "Icon")  
         button.action = #selector(togglePopover(_:))  
    }
    
And also add the following property to the top of theAppDelegate class after the popover declaration:
      
    var statusBarItem: NSStatusItem!
    
Your AppDelegate class should now look something like this:
    
We have now successfully created the NSStatusItem we needed and assigned it’s action to our togglePopover handler method. This is all the code we need for a working menu bar application! But we aren’t quite done yet… You may have noticed above that we have also assigned a NSImage to our status item button. Let’s see how we create that icon now…
    
## **Creating a Menu Bar Icon**
    
In order for our icon to appear correctly in the menu bar it needs to be a specific size. The recommended size for the icon is 22px square. However I have found that while the overall size of the icon image file should be 22pxthe _actual_ icon size should be 16px centered in the 22px transparent image area. I would also recommend using a vector image such as a PDF image for the icon.
    
![](https://miro.medium.com/v2/resize:fit:512/1*QE4lwG1850HURKYFcEvsuQ.png)
    
Your icon should fit in the 16px area. Your overall image size should be 22px square.
    
Once you have created your icon masterpiece in your vector graphics application of choice, navigate to Assets.xcassets and choose “New Image Set” by clicking the + symbol near the lower left of the window.   
Rename the image set to “Icon”.
    
With the image set selected, in the Attributes inspector tab on the right side, change “Render As” to “Template Image” and uncheck “Universal” and check “Mac” in “Devices”. In “Scales” choose “Single Scale”. Drag and drop your exported PDF icon file to “All” area.
    
You should have something like this:
    
![](https://miro.medium.com/v2/resize:fit:1400/1*rHSMWGHXqfugVjELFBvuUw.png)
    
Attributes inspector settings for icon file
    
That’s it for the icon setup! Congratulations for making it this far! Now let’s test our application…
    
## **Running the Application**
    
With all of the initial setup out of the way we have arrived at the big moment! Go ahead and build and run the application by clicking the run button near the top left of the main Xcode window, or with Command-R.
    
You should see your application’s icon appear in the status bar! You should also be able to click the icon and see our popover appear! Congrats!
    
![](https://miro.medium.com/v2/resize:fit:1400/1*wRYpVVz71LfkAGKqk22cuw.png)
    
The fruits of our labor
    
But wait… why does my application also appear in the dock? Isn’t this supposed to be a menu bar application?
    
Let’s fix this next…
    
## **Final Steps**
    
In order to have our application appear only in the menu bar we need to make a quick settings change to the Info.plist file. Navigate to the Info.plist file in Xcode and add the following setting:
    
    Application is agent (UIElement) -> YES
    
![](https://miro.medium.com/v2/resize:fit:1400/1*-pVeNP5ufGDh1psQUAPkZQ.png)
    
    Application is agent (UIElement) setting
    
This mysterious (and vague) setting will allow the application to run only in the menu bar. Try it out now. Build and run your application again and voila! You should now have a fully functioning menu bar only application!
    
## **Bonus Tip (Two click problem)**
    
This tutorial was focused only on making a functioning skeleton application so we did not add any UI to our popover. However, you will find that if you add an intractable element to your popover’s UI (such as a Button), it will not be intially clickable when the popover first opens and will require two clicks before the corresponding action is called. This is due to our menu bar application not being “key” when the popover opens. To fix this issue add the following line to the AppDelegate file in the togglePopover method after the call to show the popover:
    
    self.popover.contentViewController?.view.window?.becomeKey()
    
As a note, in the past I have built non-SwiftUI menu bar applications and using the following method would also solve this problem:
    
    NSApp.activate(ignoringOtherApps: true)
    
However, this does not appear to work at least for me as of my writing this tutorial. Your mileage may vary or this may change in the future so I’m including this for reference.
    
That’s it! Thanks for following along and happy menu bar app coding!
