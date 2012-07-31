# Your First iOS App Using UICukes

Apple provide a simple tutorial for introducing the tools, technologies and techniques (the three T's) of iOS app development. You can find the [tutorial online at the Apple developer website](https://developer.apple.com/library/ios/#referencelibrary/GettingStarted/RoadMapiOS/chapters/RM_YourFirstApp_iOS/Articles/00_Introduction.html) or as a [PDF](http://developer.apple.com/library/ios/DOCUMENTATION/iPhone/Conceptual/iPhone101/iPhone101.pdf) (links work at the time of writing), also find it in Xcode developer documentation under iOS, General, Your First iOS App. Apple call it iOS __101__, a basic introduction. This guide aims to supplement Apple's 101 documentation by following the same tutorial but with Cucumber as our driver: a 101 exercise in behaviour-driven design.

[Prerequisites](https://github.com/OCCukes/OCCukes/wiki/Prerequisites) include: Xcode and Apple's command line tools; Cucumber and Ruby using RVM. Apple's command line tools bundle Git. You will need this for cloning the [UICukes](https://github.com/OCCukes/UICukes) repository and its dependent submodules.

## App Requirements

The application has a very simple requirement: say hello to the user! First, it finds out the user's name. Then, when the user presses the Hello button, the app says hello. How sweet! User interface design reproduced below.

![AppSimulator](UICukes101/raw/master/Images/appSimulator.png)

Start with nothing, except for your project's README. No Xcode project exists as yet. It's always good to start out with your project's README describing your project's aims and objectives. Describing helps to crystallise your ideas and focus on the goals.

See the [list of commits](UICukes101/commits/master) for this project to follow the steps precisely. The following description walks you through the process, blow by blow.

Start by thinking about what your project's goals are. What features give the project its business value. Think up and type up your initial list of features. Write feature files, one for each feature under `features` within your project's root folder.

## Cucumber Folder Convention

Case is not normally significant for OS X machines. The file system is case-insensitive by default. Call the folder `features` or `Features`, it does not matter. By Ruby convention however, the directory has an all-lower-case name as do all files and folders below it, and use underscore delimiters. Recommend that you follow this convention for `features` in your iOS projects also. Use the normal camel-casing for everything else as per Apple conventions. Just stick to underscores for the `features` components.

The standard Cucumber folder structure lays out something like this.

- `UICukes101`: your project's root folder
	- `features`: Cucumber features live here
		* `my_first.feature`: features named for their title
		* `my_second.feature`: another one
		* `my_third.feature`: and another
		* `support`: Cucumber looks for this folder
			- `env.rb`: describes [things about the Cucumber execution environment](https://raw.github.com/OCCukes/OCCukes/master/features/support/env.rb)
		* `step_definitions`: defines steps for Ruby projects
			- `localhost.wire`: no Ruby steps here, redirect to Objective-C using the Cucumber wire protocol

This is how your Cucumber-based iOS app project folder will accommodate Cucumber features. It contains only a tiny amount of Ruby for integrating Cucumber and Xcode.

Back to our application development.

## First Feature

If you have [TextMate](http://macromates.com/) or [RubyMine](http://www.jetbrains.com/ruby/), writing features becomes easier. They understand the [Gherkin](https://github.com/cucumber/gherkin)-formatted feature files and have shortcuts for stubbing out templates for features and scenarios. Create `personal_greeting.feature` in `features`. Make its contents equal to:

	Feature: Personal greeting
	  In order to see my personal greeting
	  As an Apple iOS user
	  I want to enter my name and see a friendly hello

The above text describes the new feature's value or benefit, the user's role and the feature details—all in a human-readable form. This is the Connextra format for expressing user stories. Each line describes the _why_, the _who_ and the _what_ of the feature in question.

Not obvious at first, but the feature is executable. That is, Cucumber inputs the feature with a view to testing it and driving the development process itself by focusing on behaviour required to meet the feature, as opposed to writing software that we _think_ or _imagine_ that might meet the requirements.

Next, we need to run the feature. That requires an iOS app and Cucumber integration. Start with an Xcode template.

## Single View Application

Create an Xcode project for iOS. Use the Single View Application template. Please reference Apple's guide, [Getting Started](https://developer.apple.com/library/ios/#referencelibrary/GettingStarted/RoadMapiOS/chapters/RM_YourFirstApp_iOS/Articles/01_CreatingProject.html#//apple_ref/doc/uid/TP40011343-TP40012323-CH3-SW3). I called the app `UICukes101`, but you can call it anything. Use storyboards, ARC and unit tests. Make it Universal, for iPhone _and_ iPad.

You can launch the template-generated application straight away. It does nothing very interesting, although it does much in terms of launching and running software on your iOS device. From the user's point of view though, it displays a white screen.

![SingleViewAppTemplate](UICukes101/raw/master/Images/SingleViewAppTemplate.png)

Boring. Very tempting to throw on some controls and wire up the view controller at this point. But no. Let Cucumber guide our steps! For simple applications like this one, behaviour-driven design and development seems overkill. But for more complex and realistic projects, it proves golden.

## Install `UICukes`

Use Git to add `UICukes` to your project folder as a Git submodule. There are various useful Mac GUI tools for operating Git. Here are the Terminal commands. Run them from your project's root folder.

	git submodule add git://github.com/OCCukes/UICukes.git
	git submodule update --init --recursive

The `git://` reference is the read-only link to the primary `UICukes` repo at GitHub. Most developers will use this one. The first command clones the repo but does __not__ automatically clone the submodule's addition sub-submodule dependencies. Hence the second command.

Add the `UICukes/UICukes.xcodeproj` to your project; Option+Cmd+A is the keyboard shortcut. Do not copy any items; they are already there. Do not add to any targets. You need to add to your test target only.

![AddToYourProject](UICukes101/raw/master/Images/AddToYourProject.png)

Open your test target's Build Phases. Add the `UICukes` target as a Target Dependency. Also add `libUICukes.a` as a library to link against; the linker automatically picks up the other dependencies. You can now press Cmd+U to compile, build, link and launch the test target. Tests will run but fail. The example test fails by design. Delete the boilerplate test code by removing the `-setUp`, `-tearDown` and `-testExample` methods. Keep the source files. You might find them useful for 'SenTesting' later.

### Environment Support

Copy `UICukes/OCCukes/features/support/env.rb` to the `features/support` folder. You can also find `env.rb` [online](https://github.com/OCCukes/OCCukes/blob/master/features/support/env.rb). This file contains just a little piece of Ruby for synchronising Cucumber with Xcode.

Similarly, copy `UICukes/OCCukes/features/step_definitions/54321.wire` to the `features/step_definitions` folder; also available [online](https://github.com/OCCukes/OCCukes/blob/master/features/step_definitions/54321.wire). The name does not matter. The file configures Cucumber to look for the wire-protocol server at port 54321 on `localhost`. This configuration stands in place of step definitions; they exist on the _other_ side of the wire.

At this point, Cucumber is ready to roll. But Xcode is not. Not yet.

### Test Pre- and Post-Actions

Copy the test pre- and post-actions from the OCCukes scheme to your project's test phase. Test pre-actions should look like this. See screenshot below.

![TestPreActions](UICukes101/raw/master/Images/TestPreActions.png)

Note, you provide build settings from your test target.

Also note, the above `1.9.3` request for Ruby 1.9.3 using RVM. This assumes of course that you have Ruby 1.9.3 installed along with the Cucumber gem within that Ruby. If not, you will need to install Ruby 1.9.3 using `rvm install 1.9.3` and then install Cucumber using `gem install cucumber`. If some of these assumptions fail, you will need to adjust the pre-action accordingly. You may need to adjust the Ruby version or use a Ruby installed by other means, rather than by [Ruby Version Manager](https://rvm.io/).

Similarly for your test post-actions, as follows.

![TestPostActions](UICukes101/raw/master/Images/TestPostActions.png)

So far so good. One more piece to the connection puzzle. Your tests need to launch the wire protocol server.

### Cucumber Runtime

Copy `OCCukesTests.h` and `OCCukesTests.m` from `UICukes/OCCukes/OCCukesTests` to `UICukes101Tests` and add the source to your test target. These tests launch the Cucumber wire server, terminating automatically when all wire connections shut down.

With that in place, you can launch Cucumber using Cmd+U. Cucumber launches in the background, interacts with your application and outputs:

![CucumberFeatures](UICukes101/raw/master/Images/CucumberFeatures.png)

Cucumber wired up! It does not do anything, yet. Cucumber merely parses the feature and displays it. Our project is ready for some scenarios.

### All Load

One final step: navigate to your test target's Build Settings and look for Other Linker Flags. Add `-all_load` to the flags, i.e. `OTHER_LDFLAGS = "-all_load"`. Objective-C does not automatically load Objective-C categories when linking against static libraries. This flag fixes that issue in advance.

## Scenarios

What are scenarios? Features comprise one or more 'scenarios' which exemplify how the software should behave. Scenarios themselves comprise one or more steps.

For this simple app, the basic scenario follows the steps, given that the user launches the personal greeter app:

1. when the user enters his name "Roy,"
2. and presses the "Hello" button;
3. then the greeter app says a friendly "Hello, Roy!"

Add this scenario with steps to your feature as follows:

	  Scenario: Says hello to Roy
	    Given that the user launches the personal greeter app
	    When the user enters his name "Roy"
	    And presses the "Hello" button
	    Then the greeter app says a friendly "Hello, Roy!"

Add then re-test. When you re-test by pressing Cmd+U, you see some additional information. Cucumber provides some snippets for steps which now appear but Cucumber has no idea how they translate to success or failure. The behaviour-driven development process aims to make these yellow steps turn to green.

![Snippets](UICukes101/raw/master/Images/Snippets.png)

You might notice that each step has two alternative 'snippets' or stubs, one for Ruby and one for Objective-C. You can define steps in different languages. Cucumber does not care how you define the steps. Implementation language does not matter.

## Pending Step Definitions

Create a module for defining your greeter steps. Add a new source file, `PersonalGreetingSteps.m`, to your test target. Let it define a static constructor function. The code looks like this. See below. Two things to note: the `constructor` attribute and the `static` storage classifier. The `StepDefinitions()` function remains anonymous outside the scope of the module, and it runs automatically at run-time when the test bundle executes. This is where we will insert our step definitions.

```objc
#import <UICukes/UICukes.h>

__attribute__((constructor))
static void StepDefinitions()
{
	
}
```

Copy the snippets to your step definitions function. You can copy directly from Safari. Your new pending step definitions will appear as:

```objc
#import <UICukes/UICukes.h>

__attribute__((constructor))
static void StepDefinitions()
{
	[OCCucumber given:@"^that the user launches the personal greeter app$" step:^(NSArray *arguments) {
		// express the regular expression above with the code you wish you had
		[OCCucumber pending:@"TODO"];
	} file:__FILE__ line:__LINE__];
	
	[OCCucumber when:@"^the user enters his name \"(.*?)\"$" step:^(NSArray *arguments) {
		// express the regular expression above with the code you wish you had
		[OCCucumber pending:@"TODO"];
	} file:__FILE__ line:__LINE__];
	
	[OCCucumber when:@"^presses the \"(.*?)\" button$" step:^(NSArray *arguments) {
		// express the regular expression above with the code you wish you had
		[OCCucumber pending:@"TODO"];
	} file:__FILE__ line:__LINE__];
	
	[OCCucumber then:@"^the greeter app says a friendly \"(.*?)\"$" step:^(NSArray *arguments) {
		// express the regular expression above with the code you wish you had
		[OCCucumber pending:@"TODO"];
	} file:__FILE__ line:__LINE__];
}
```

And when you re-test, the new steps become pending. Output looks like this:

![Pending](UICukes101/raw/master/Images/Pending.png)

Cucumber invites us to resolve the pending steps one by one.

## Step: Given the User Launches the App

This is a simple step. Xcode handles the automatic launching on our behalf. But what we can do is to express an expectation that the front-most application's name matches our app's name, `UICukes101`. The expectation for this using `UIAutomation` and `OCExpectations` reads:

```objc
	[OCCucumber given:@"^that the user launches the personal greeter app$" step:^(NSArray *arguments) {
		[[[[UIAutomation localTarget] frontMostApp] name] should:be(@"UICukes101")];
	} file:__FILE__ line:__LINE__];
```

This gives you an idea of how to access your application's user interface using [Apple's UIAutomation framework](http://developer.apple.com/library/ios/#documentation/DeveloperTools/Reference/UIAutomationRef/_index.html) ([reference PDF](http://developer.apple.com/library/ios/documentation/DeveloperTools/Reference/UIAutomationRef/UIAutomationRef.pdf)) using Objective-C. The semantics mimic the Instrument's JavaScript interface closely. Best way to learn it might be to play with the UIAutomation instrument using JavaScript. Record something and see what JavaScript code it produces. From there, you can work out relatively easily what Objective-C code gives you the same results.

Switching on the iOS Simulator's Accessibility Inspector is another useful way to access the underlying objects and their attributes. Navigate to Settings within the Simulator, to General, Accessibility. Slide the Accessibility Inspector to ON. From then on, clicking interface components tells you their label, value, traits and frame coordinates. Very useful. See below.

![AccessibilityInspectorOn](UICukes101/raw/master/Images/AccessibilityInspectorOn.png)

And now when we re-test, the first step goes __green__:

![UserLaunchesAppGoesGreen](UICukes101/raw/master/Images/UserLaunchesAppGoesGreen.png)

Progress! We can however eliminate this step definition altogether. UICukes provides a pre-defined step called "the front-most app has the name" where you supply the name of the application is double quotes, as an argument. Delete the step definition and rename the step from

	    Given that the user launches the personal greeter app

to

	    Given the front-most app has the name "UICukes101"

Change the string in quotes to whatever you named your application. Re-test and see the _new_ Given step, still green:

![FrontMostAppHasName](UICukes101/raw/master/Images/FrontMostAppHasName.png)

### Scenario Background

At the moment, our app-name step exists within the scenario and we have to duplicate this for all scenarios, assuming it applies to all scenarios (and it does). Instead, let us move the Given step to the Background so that it applies to this scenario and to all scenarios for this feature that we might add in the future. Our feature file now looks like this:

	Feature: Personal greeting
	  In order to see my personal greeting
	  As an Apple iOS user
	  I want to enter my name and see a friendly hello
	
	  Background:
	    Given the front-most app has the name "UICukes101"
	
	  Scenario: Says hello to Roy
	    When the user enters his name "Roy"
	    And presses the "Hello" button
	    Then the greeter app says a friendly "Hello, Roy!"

## Step: User Enters Name

Three more steps to go. Time for a red-green-refactor development cycle. We start by making the next pending step go __red__, fail. For this step to succeed, there should be a text field where the user enters their name.

We can use Apple's UI automation framework to test our expectation. It works like this: you pick up the "local target," the starting point for navigating the UI hierarchy. Then you walk to the front-most app, then its main window, then ask for all the text fields. There _should_ be one labelled "Name." You normally do this using JavaScript in Instruments of course, but not with UICukes; you can walk the iOS user interface and visit its various interesting elements using Objective-C. The expectation looks like this:

```objc
	[OCCucumber when:@"^the user enters his name \"(.*?)\"$" step:^(NSArray *arguments) {
		UIATarget *localTarget = [UIAutomation localTarget];
		id textFields = [localTarget valueForKeyPath:@"frontMostApp.mainWindow.textFields"];
		id textField = [textFields firstWithName:@"Name"];
		[textField shouldNot:be_a(@"UIAElementNil")];
	} file:__FILE__ line:__LINE__];
```

There are some important things to note about this step definition. First, notice that it uses Key-Value Coding to walk the elements. You can use Objective-C messages as well, of course. But sometimes, KVC can make things easier to read and has some special operators which can help parse out the relevant facts. Also note the `id` types. We are not really interested in the precise types. At this test-level of coding, type checking adds little. If any object fails to receive and handle the messages sent to it, the run-time will throw an exception and our step will fail explaining why to Cucumber. That is as good a test as any. Finally, note the `be_an` expectation. The expectation is that the request for the first text field named "Name" _should not be_ a kind of nil element. UIAutomation uses a special class to represent nil answers: `UIAElementNil`.

On re-test, Cucumber outputs:

![UserEntersNameRed](UICukes101/raw/master/Images/UserEntersNameRed.png)

Red means failure. Time to turn red to green.

