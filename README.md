Technologies/Tools used in building the framework
============================================
- Appium - Mobile Automation library
- Maven - Build automation tool
- Java - Programming language
- TestNG - Test Management library
- Log4J - Logging framework
- Extent Reports - Reporting framework
- JSON/Excel - Test Data
- XML - Static text
- GitHub - Version control
- Jenkins - CI/CD

Framework implements below best practices
=========================================
- Code reusability
- Code readability
- Scalable automation (demonstrated using multiple test classes)
- Uses explicit waits
- Abstraction layer for UI commands like click, sendkeys, etc.
- Parameterization using TestNG XML and config.properties
- Alternate Design approach [Without using inheritance]
- Exception handling [using Try/Catch and TestNG Listener]
- Abstraction layer for test data
- Abstraction layer for static text
- Supports iOS and Android
- Demonstrates how to define UI elements that are common across pages (e.g. menu bar, side bar, etc.)
- How to recover from test failure/ how to write fail safe test cases
- Scrolling for both Android and iOS (using touchaction, uiScrollable, mobile:scroll)
- Demonstrates how to effectively capture Screenshots/Videos
- Supports parallel execution on multiple real Android and iOS devices
- Integrated with Log4J2 Logging framework (supports basic as well as parallel logging)
- Integrated with Extent Reporting framework (supports parallel, screenshots, logging test steps)


## Misc
**Desired Capabilities**: Keys and values encoded in a JSON object, sent by Appium clients to server. Carries information like device info and app info.

**Android how to get appPackage and appActivity:**
```
adb shell dumpsys window | grep -E mCurrentFocus
adb shell "dumpsys activity activities | grep mResumedActivity" (Android 10 or 11)
download APK info 
```

**Android launch emulator automatically:**
```
desiredCapabilities.setCapability("avd", deviceName);
desiredCapabilities.setCapability("avdLaunchTimeout", 120000);
```

**How to check device id of iPhone simulator?**
```
1. instruments -s devices
2. xcrun simctl list
3. From Xcode: Window -> Devices and Simulators -> Simulators. The Identifier value is the UDID.
```

## Android Locator Strategies
|Locator Strategy|Description|Example|Code|
|----------------|-----------|---------|--------|
|Accessibility ID|content-desc attribute|<unique_element_name>|driver.findElementByAccessibilityId("<unique_element_name>");|
|ID|resource-id attribute|<app_package>/<resource-id>|driver.findElementById("<resource-id>");|
|Class Name|Uiautomator2 class name|android.widget.TextView|driver.findElementByClassName("android.widget.TextView");|
|XPath|XML path expression|//<UiAutomator2 Class Name>[@<attribute name>="attribute value"]/<axes>::<expression>|driver.findElementByXPath();|
|Image|matches with base64 encode image file|<base64_encode_string>|driver.findElementByImage("<base64_encode_string>");|
|UiAutomator2(UiSelector)|UI Automator API, use UiSelector class|"new UiSelector().text(\"Animation\")"|((FindsByAndroidUIAutomator)driver).findElementByAndroidUIAutomator("new UiSelector().text(\"Animation\")");|