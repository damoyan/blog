# How to integrate memory leak check into ABA

### Step 1. preparation

#### 1. download command line tool of Xcode

Since we need to use command in Terminal, so you need to have this command line tool ready. Open Xcode -> Preferences -> Downloads, choose command line tool.

#### 2. make sure you can run ABA on your Mac

#### 3. authorize `instruments` 

This step is to avoid `instruments` prompt for Mac user name and password to get the access to minitor other process.

Open Terminal and run the command below:

	security authorizationdb write system.privilege.taskport is-developer
	
You will see a pop-up window which request your user name and password. Input Your password and click 'OK', after the window dismisses, you can see `YES (0)` in Terminal. 

### Step 2. build app for `instruments`

We still use xcodebuild to build the app. But we need to add one more setting for `instruments`.

	xcodebuild -project MicroStrategyMobile.xcodeproj -sdk iphonesimulator -configuration Debug ARCHS="i386" VAVID_ARCHS="i386" -target MicroStrategyMobileIPad_Dev GCC_INSTRUMENT_PROGRAM_FLOW_ARCS=YES

The last parameter `GCC_INSTRUMENT_PROGRAM_FLOW_ARCS=YES` will generate information for `instruments`.

### Step 3. start Simulator using `instruments`

You can check the detail usage of instruments on [apple](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/instruments.1.html). Here I will instroduce some parameter used.

#### -w

	instruments -w help
	
this will list all the known device can be used. This parameter should be the first one. The output may be like this:

	Known Devices:
	PYU-MAC (com.apple.instruments.devices.local)
	iPhone - Simulator - iOS 7.1
	iPhone Retina (3.5-inch) - Simulator - iOS 7.1
	iPhone Retina (4-inch) - Simulator - iOS 7.1
	iPhone Retina (4-inch 64-bit) - Simulator - iOS 7.1
	iPad - Simulator - iOS 7.1
	iPad Retina - Simulator - iOS 7.1
	iPad Retina (64-bit) - Simulator - iOS 7.1
	
#### -s

This will list all the templates can be used by `instruments`

	[mstr@PYU-MAC ~]$ instruments -s
	Known Templates:
	/Applications/Xcode.app/Contents/Applications/Instruments.app/Contents/Resources/templates/Activity Monitor.tracetemplate
	/Applications/Xcode.app/Contents/Applications/Instruments.app/Contents/Resources/templates/Allocations.tracetemplate
	/Applications/Xcode.app/Contents/Applications/Instruments.app/Contents/Resources/templates/Blank.tracetemplate
	/Applications/Xcode.app/Contents/Applications/Instruments.app/Contents/Resources/templates/Counters.tracetemplate
	/Applications/Xcode.app/Contents/Applications/Instruments.app/Contents/Resources/templates/Event Profiler.tracetemplate
	/Applications/Xcode.app/Contents/Applications/Instruments.app/Contents/Resources/templates/Leaks.tracetemplate
	…
	
#### -D

Set the output file of one run.

The final command should be like this:

	instruments -w "iPad - Simulator - iOS 7.1" -t /Applications/Xcode.app/Contents/Applications/Instruments.app/Contents/Resources/templates/Leaks.tracetemplate -D 111.trace /Users/mstr/pyu_view_main_switch/Kernel/IPhone/MicroStrategyMobile/build/Debug-iphonesimulator/MicroStrategyIPad_Dev.app

###  Crash Handle and Stop

`instruments` won't exit after started. If the app crash during the testing, `instruments` will also quit and it will write the data into output file. We can call it again to restart it.

After the test finishes, we need to sent SIGINT signal to this process to force it flush data to output file(in the example, the output file is 111.trace). We can use `kill -2 pid` to achieve this.

Anytime when simulator is closed, `instruments` will exit with data flushed.