# How to flush CC data when app crashes

Objective-C allows developer to use `NSSetUncaughtExceptionHandler(&myHandler)` to set an handler to do something when app crashes. So we can flush data in this handler.

Handler is a function. You can define it outside `@implementation xxx` block.

e.g.

	void myHandler()
	{
		statement
	}
	@implementation xxx
	…
	
GCOV has a function `__gcov_flush()` can be used to flush data. To use this function, you need to import `<objc/runtime.h>` in your file. The whole usage should like this:

	#import <objc/runtime.h>
	
	extern void __gcov_flush();
	void myHandler();
	void myHandler()
	{
		if(__gcov_flush) 
		{
			__gcov_flush();
		}
		…… 
	}
	
	@implementation Example
	
	-(BOOL) application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
	{
		NSSetUncaughtExceptionHandler(&myHandler); // set the handler
		…… //other statements
	}
	
	@end