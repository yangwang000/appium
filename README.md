## TDD Framework
```
BaseTest.java
    initialize driver
    initialize config properties [global variables]
    exit driver
  We initialize driver in @BeforeTest method, so all the test classes can use the driver. We initialize the driver only once assuming all the test cases will be executed on the same device.And we intall the App in the @BeforeTest.
  Selenium initialize driver in @BeforeMethod method.
  
BasePage.java
    explicit wait
    pagefactory initialization
    driver commands [click sendkey etc.]  
    
TestNG Listener
    can be called in class by annotation or in testng.xml, when called in testng.xml listener can be used by all test classes
```
Avoid using class level objects for parallel execution.


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
|Locator Strategy        |Description                           |Example                                                                              |Code                                                                                                        |
|------------------------|--------------------------------------|-------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
|Accessibility ID        |content-desc attribute                |<unique_element_name>                                                                |driver.findElementByAccessibilityId("<unique_element_name>");                                               |
|ID                      |resource-id attribute                 |<app_package>/<resource-id>                                                          |driver.findElementById("<resource-id>");                                                                    |
|Class Name              |Uiautomator2 class name               |android.widget.TextView                                                              |driver.findElementByClassName("android.widget.TextView");                                                   |
|Name                    |text  attribute                       |<static>_text_                                                                       |driver.findElementByName("<static_text>")                                                                   |
|XPath                   |XML path expression                   |//<UiAutomator2 Class Name>[@<attribute name>="attribute value"]/<axes>::<expression>|driver.findElementByXPath();                                                                                |
|Image                   |matches with base64 encode image file |<base64_encode_string>                                                               |driver.findElementByImage("<base64_encode_string>");                                                        |
|UiAutomator2(UiSelector)|UI Automator API, use UiSelector class|"new UiSelector().text(\"Animation\")"                                               |((FindsByAndroidUIAutomator)driver).findElementByAndroidUIAutomator("new UiSelector().text(\"Animation\")");|

### Android: Finding Elements Using Different Locator Strategies
```
AppiumDriver driver = CreateDriverSession.initializeDriver("Android");

MobileElement myElement = (MobileElement) driver.findElementByAccessibilityId("Accessibility");
System.out.println(myElement.getText());

myElement = (MobileElement) driver.findElementsById("android:id/text1").get(1);
System.out.println(myElement.getText());

myElement = (MobileElement) driver.findElementsByClassName("android.widget.TextView").get(2);
System.out.println(myElement.getText());

myElement = (MobileElement) driver.findElementByXPath("//android.widget.TextView[@content-desc=\"Accessibility\"]");
System.out.println(myElement.getText());

myElement = (MobileElement) driver.findElementByXPath("//*[@text=\"Accessibility\"]");
System.out.println(myElement.getText());

myElement = (MobileElement) driver.findElementByTagName("Accessibility");
System.out.println(myElement.getText());

MobileElement myElement = (MobileElement) ((FindsByAndroidUIAutomator) driver).findElementByAndroidUIAutomator("new UiSelector().text(\"Accessibility\")");
System.out.println(myElement.getText());

myElement = (MobileElement) ((FindsByAndroidUIAutomator) driver).findElementsByAndroidUIAutomator("new UiSelector().className(\"android.widget.TextView\")").get(2);
System.out.println(myElement.getText());

myElement = (MobileElement) ((FindsByAndroidUIAutomator) driver).findElementByAndroidUIAutomator("new UiSelector().description(\"Accessibility\")");
System.out.println(myElement.getText());

myElement = (MobileElement) ((FindsByAndroidUIAutomator) driver).findElementsByAndroidUIAutomator("new UiSelector().resourceId(\"android:id/text1\")").get(1);
System.out.println(myElement.getText());
```

### Important note on XPath
For native apps, please try to avoid using XPath. XPath is fragile, slow and bound to change with minor UI updates in the application. A single UI update and many XPaths will get impacted if not optimized.

If you really want to use XPath, please write optimized XPaths. It's difficult to cover XPath in detail in this course because here our focus is on learning Appium and the best practices. XPath is not one of the best practice.

If elements cannot be identified using unique IDs, please request your application development team to add those as much as possible.

Also, iOS Predicate Strings and Class Chains are very good alternatives to XPath. Appium Inspector automatically suggests these in most of the cases. So prefer these over XPath.

For Android , it's still ok to use XPath for elements where resource-id is not available, but for iOS, please avoid as much as possible.

For Hybrid apps, prefer CSS over XPath if elements cannot be identified using unique IDs.

### Different ways of defining native elements and best practices
```
public class DifferentWaysOfDefiningElements {
    //@AndroidFindBy annotation should be used on class level not method level
    //this annotation works with PageFactory
    @AndroidFindBy(xpath = "//*[@text=\"Accessibility\"]")
    @iOSXCUITFindBy(accessibility = "Accessibility")
    private static MobileElement myElement3;

    public DifferentWaysOfDefiningElements(AppiumDriver driver){
        PageFactory.initElements(new AppiumFieldDecorator(driver), this);
    }

    public static void main(String[] args) throws Exception {
        AppiumDriver driver = CreateDriverSession.initializeDriver("Android");
        DifferentWaysOfDefiningElements differentWaysOfDefiningElements = new DifferentWaysOfDefiningElements(driver);
        System.out.println(myElement3.getText());

        By myElement2 = MobileBy.AccessibilityId("Accessibility");
        System.out.println(driver.findElement(myElement2).getText());

        MobileElement myElement = (MobileElement) driver.findElementByAccessibilityId("Accessibility");
        System.out.println(myElement.getText());

        WebElement myElement1 = driver.findElementByAccessibilityId("Accessibility");
        System.out.println(myElement1.getText());
    }
}

// MobileElement, WebElement, By, MobileBy, @FindBy, @AndroidFindBy, @iOSXCUITFindBy
```

### Basic Element Actions
```
AppiumDriver driver = CreateDriverSession.initializeDriver("Android");

By views = MobileBy.AccessibilityId("Views");
By textFields = MobileBy.AccessibilityId("TextFields");
By editText = MobileBy.id("io.appium.android.apis:id/edit");

driver.findElement(views).click();

(new TouchAction(driver))
                .press(new PointOption().withCoordinates(507, 1679))
                .moveTo(new PointOption().withCoordinates(520, 625))
                .release()
                .perform();

(new TouchAction(driver))
                .press(new PointOption().withCoordinates(494, 1713))
                .moveTo(new PointOption().withCoordinates(499, 560))
                .release()
                .perform();

(new TouchAction(driver))
                .press(new PointOption().withCoordinates(503, 1713))
                .moveTo(new PointOption().withCoordinates(494, 525))
                .release()
                .perform();

driver.findElement(textFields).click();
driver.findElement(editText).sendKeys("my text");
Thread.sleep(3000);
driver.findElement(editText).clear();
```

### Fetch Element Attributes
```
AppiumDriver driver = CreateDriverSession.initializeDriver("iOS");

By accessibility = MobileBy.AccessibilityId("Activity Indicators");
System.out.println("label:" + driver.findElement(accessibility).getText());
System.out.println("label:" + driver.findElement(accessibility).getAttribute("label"));
System.out.println("value:" + driver.findElement(accessibility).getAttribute("value"));
System.out.println("enabled:" + driver.findElement(accessibility).getAttribute("enabled"));
System.out.println("selected:" + driver.findElement(accessibility).getAttribute("selected"));
System.out.println("visible:" + driver.findElement(accessibility).getAttribute("visible"));

System.out.println("selected:" + driver.findElement(accessibility).isSelected());
System.out.println("enabled:" + driver.findElement(accessibility).isEnabled());
System.out.println("displayed:" + driver.findElement(accessibility).isDisplayed());
```

### Synchronization using waits and best practices
```
AppiumDriver driver = CreateDriverSession.initializeDriver("iOS");
//driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

By alertViews = MobileBy.AccessibilityId("Alert Views");
By okayCancel = MobileBy.AccessibilityId("Okay / Cancel");

WebDriverWait wait = new WebDriverWait(driver, 10);
wait.until(ExpectedConditions.visibilityOfElementLocated(alertViews)).click();
//driver.findElement(alertViews).click();
wait.until(ExpectedConditions.visibilityOfElementLocated(okayCancel)).click();
//driver.findElement(okayCancel).click();
```
Use either implicit wait or explicit wait, but don`t mix both.

### Tap
```
AppiumDriver driver = CreateDriverSession.initializeDriver("Android");
driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

By accessibility = MobileBy.AccessibilityId("Accessibility");

TouchAction t = new TouchAction(driver);
//t.tap(ElementOption.element(driver.findElement(accessibility))).perform();
 //t.tap(PointOption.point(538, 416)).perform();
t.tap(TapOptions.tapOptions().withElement(ElementOption.element(driver.findElement(accessibility)))).perform();
```

### Press and Long Press
```
AppiumDriver driver = CreateDriverSession.initializeDriver("iOS");
driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

By accessibility = MobileBy.AccessibilityId("Activity Indicators");

TouchAction t = new TouchAction(driver);
t.press(ElementOption.element(driver.findElement(accessibility))).waitAction(WaitOptions.waitOptions(Duration.ofMillis(5000))).release().perform();
t.longPress(ElementOption.element(driver.findElement(accessibility))).waitAction(WaitOptions.waitOptions(Duration.ofMillis(5000))).release().perform();
```

### Swipe
```
AppiumDriver driver = CreateDriverSession.initializeDriver("iOS");
driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

By views = MobileBy.AccessibilityId("Views");
By grid = MobileBy.AccessibilityId("Grid");
By animation = MobileBy.AccessibilityId("Animation");
driver.findElement(views).click();*//*

By steppers = MobileBy.AccessibilityId("Steppers");
By activityIndicators = MobileBy.AccessibilityId("Activity Indicators");

Dimension size = driver.manage().window().getSize();

int startX = size.width / 2;
int endX = startX;
int startY = (int) (size.height * 0.8);
int endY = (int) (size.height * 0.2);

TouchAction t = new TouchAction(driver);
t.press(PointOption.point(startX, startY)).waitAction(WaitOptions.waitOptions(Duration.ofMillis(2000))).moveTo(PointOption.point(endX, endY)).release().perform();

TouchAction t = new TouchAction(driver);
t.press(ElementOption.element(driver.findElement(steppers)))
                .waitAction(WaitOptions.waitOptions(Duration.ofMillis(2000)))
                .moveTo(ElementOption.element(driver.findElement(activityIndicators)))
                .release()
                .perform();
```

### Android: Interacting with Apps
```
AppiumDriver driver = CreateDriverSession.initializeDriver("Android");
driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

By views = MobileBy.AccessibilityId("Views");
driver.findElement(views).click();

Thread.sleep(5000);
System.out.println(driver.queryAppState("io.appium.android.apis"));
Thread.sleep(5000);
driver.terminateApp("io.appium.android.apis");
Thread.sleep(5000);
System.out.println(driver.queryAppState("io.appium.android.apis"));
driver.runAppInBackground(Duration.ofMillis(5000));
driver.terminateApp("io.appium.android.apis");
Thread.sleep(5000);
driver.activateApp("com.android.settings");
Thread.sleep(5000);
driver.activateApp("io.appium.android.apis");*//*
System.out.println(driver.isAppInstalled("io.appium.android.apis"));
driver.terminateApp("io.appium.android.apis");
String andAppUrl = System.getProperty("user.dir") + File.separator + "src" + File.separator + "main"+ File.separator + "resources" + File.separator + "ApiDemos-debug.apk";
driver.installApp(andAppUrl, new AndroidInstallApplicationOptions().withReplaceEnabled());
```

### Android: Lock and Unlock Devices
```
AppiumDriver driver = CreateDriverSession.initializeDriver("Android");
driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

((AndroidDriver) driver).lockDevice();
System.out.println(((AndroidDriver) driver).isDeviceLocked());
((AndroidDriver) driver).unlockDevice();
```

### Android: Working with keyboard
```
System.out.println(((AndroidDriver) driver).isKeyboardShown());

((AndroidDriver) driver).pressKey(new KeyEvent().withKey(AndroidKey.C));
driver.getKeyboard().pressKey(Keys.ARROW_DOWN);
((AndroidDriver) driver).pressKey(new KeyEvent().withKey(AndroidKey.HOME));
((AndroidDriver) driver).pressKey(new KeyEvent().withKey(AndroidKey.CALENDAR));
((AndroidDriver) driver).pressKey(new KeyEvent().withKey(AndroidKey.B));
Thread.sleep(3000);
driver.getKeyboard().pressKey(Keys.HOME);
driver.hideKeyboard();
```