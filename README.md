## POL - B&DE - Test Automation Framework

#### 1. Purpose and Contents

The aim of this test automation framework is providing a one-stop solution for test automation requirements across scrum teams for both In-sprint and Regression test automation. Targeting to create a hybrid framework with highly reusable modules to enable browser/mobile app/api testing using Cucumber BDD.

As a first step, this framework is now having options for UI automation(browser based/mobile simulation UI automation). <Br>
More features will be added in the upcoming sprints.
****

#### 2. Parameters to run tests

**mvn clean test** - **Mandatory** - To clean the target folder, build and run tests configured in maven project

**-Denv="dev"** - **Optional** - Name of the properties file contains environment specific parameters. E.g.: dev.properties or test1.properties, which is available under src\test\resources\config\environments\ <Br>
Note: If the environment is not set from commandline/terminal, the framework will refer the 'defaultEnvironment' parameter from defaultSettings.properties

**-DdriverStack="chrome"** - **Optional for UI Automation** - Name of the json file that contains configuration details to initiate a browser. The files are available in the path src\test\resources\config\driver\. Other files in the same location are grid.json, multibrowser.json.
Note: If the environment is not set from commandline/terminal, the framework will refer the 'driverStack' parameter from defaultSettings.properties

**-DserverType="grid"** - **Optional** - Optional parameter to intiate grid driver. Can be used for parallel/distributed execution using grid. The other parameter can be -DserverType="local".

**Other Optional Parameters:** <BR>
-DgridURL - string <BR>
-DreportPath - string <BR>
-DtextReportPath - string <BR>
-DPROJECT_NAME - string <BR>
-DBUILD_NUMBER - string <BR>
-DchromeDriver - string <BR>

If below parameters are not passed through commandline/terminal, then framework will refer the default values configured in src\test\resources\config\defaultSettings.properties <Br>

-DwebDriverManager - boolean in string format <BR>
-DscreenshotOnFailure - string

#### 3. Base Classes and Run Configs

| Package  | Class                  | Purpose                                                      |
| -------- | -----------------------| ------------------------------------------------------------ |
| pol.bde.steps | BaseSteps              | Base class providing access to the driver and wait objects. This class will be extended by each 'step definition' class in the test project. |
| pol.bde.steps | CommonSteps            | Small set of common step definitions used for browser/app testing |
| pol.bde.hooks | Hooks                  | Teardown cucumber hooks that ensure screenshot capture for failed tests.  Screenshots are written to the report directory within the test project. The screenshot file path is also written to the test parameters (Common Library object) and if the Reporting Library is used by the test project then the screenshot will be automatically embedded in the run report. |
| pol.bde.runners | SequentialBaseRunner   | Base runner class to run scenarios one by one that sets the initial @CucumberOptions and the @Test method that invokes cucumber via TestNG (with data provider). This class will be extended by each 'runner' class in the test project used for browser / app testing. |
| pol.bde.runners | ParallelBaseRunner   | Base runner class to run scenarios in parallel that sets the initial @CucumberOptions and the @Test method that invokes cucumber via TestNG (with data provider). This class will be extended the 'runner' class in the test project to run scenarios in parallel. |

The runner files need to be configured in the testng file as below
E.g. for sequential run:
```
<suite name="suite1" parallel="classes" thread-count="10" data-provider-thread-count="10">
    <test name="e2e-1">
        <classes>
            <class name="pol.bde.runners.e2e.e2e"/>
        </classes>
    </test>
</suite>
```
E.g. for parallel run: <BR> 
- The parallel threads can be increased or decreased based on the requirement
- More classes or tests can be added to increase the coverage based on the requirement
```
<suite name="suite1" parallel="methods" thread-count="10" data-provider-thread-count="10">
    <test name="e2e-1">
        <classes>
            <class name="pol.bde.runners.e2e.e2e"/>
        </classes>
    </test>
</suite>
```
The testNG xml file needs be mapped in corresponding profile in pom.xml file 
```
<profile>
    <!--Usage: mvn clean test -P <profileName>-->
    <id>E2E</id>
    <activation>
        <activeByDefault>false</activeByDefault>
    </activation>
    <properties>
        <testNG.suiteXmlFile>src/test/resources/testNG/e2e.xml</testNG.suiteXmlFile>
    </properties>
    <build>
    ....
    ....
    </build>
</profile>
```
#### 4. Utilities
#### 4.1 Page Object Utils

provides a set of page level methods that generally return the Element class when performing find operations.  Also includes methods that perform page level waits for DOM loading and JQuery readiness.

##### PageObjectUtils class should be extended in all page object class files:

```
public class LoginPage extends PageObjectUtils {

    public LoginPage() {
        initialise(this);
    }
...
}
```



##### Then for the page object classes the following operations will then be available:

- Obtain the driver instance from the factory for the current thread:

```
getDriver()
```

- Obtain a default selenium wait object for the driver for the current thread:

```
getWait()
```

- Navigate to a given url:

```
gotoURL(<url>);
```

- Wait for page loading (DOM, JQuery, Angular):

```
waitPageToLoad();
```

- Find element with retries

```
findElement(<By>, <boolean retryFindElement>)
findElement(<By>, <retries>)
findElementUsingJQuery(<queryString>)
```

-Static waits

```
waitFor(<milliSecs>)
waitInSeconds(<seconds>)
```
- Wait until the specified element is located
```
waitUntilElementLocated(<By>, <timeOutInSeconds>)
waitUntilElementLocated(<By>)
```

- Wait until the specified element is visible

```
waitUntilElementVisible(<By>, <timeOutInSeconds>)
waitUntilElementVisible(<By>)
waitUntilElementVisible(<WebElement>, <timeOutInSeconds>)
waitUntilElementVisible(<WebElement>)
```

- Wait until the specified element is clickable

```
waitUntilElementClickable(<By>, <timeOutInSeconds>)
waitUntilElementClickable(<By>)
waitUntilElementClickable(<WebElement>, <timeOutInSeconds>)
waitUntilElementClickable(<WebElement>)
```

- Wait until the specified element is visible and clickable

```
waitUntilElementVisibleAndClickable(<WebElement>, <timeOutInSeconds>)
waitUntilElementVisibleAndClickable(<WebElement>)
```

- Wait until the specified element is disabled

```
waitUntilElementDisabled(<By>, <timeOutInSeconds>)
waitUntilElementDisabled(<By>)
waitUntilElementDisabled(<WebElement>, <timeOutInSeconds>)
waitUntilElementDisabled(<WebElement>)
```

- To verify if an element exists
```
isElementExists(<By>, <retries>)
isElementExists(<WebElement>)
```

- To verify if an element is visible
```
isElementVisible(<By>, <retries>)
isElementVisible(<WebElement>)
```

- To verify if an element is displayed
```
isElementDisplayed(<By>, <retries>)
isElementDisplayed(<WebElement>)
```

- To verify if an element is enabled
```
isElementEnabled(<By>, <retries>)
isElementEnabled(<WebElement>)
```

- To select the specified value from a dropdown

```
selectListItem(<By>, <item>)
selectListItem(<WebElement>>, <item>)
```

- To verify whether the specified object exists within the current

```
objectExists(<By>)
objectExists(<List<WebElement>>)
```

- To verify whether the specified text is present within the current page
```
isTextPresent(<textPattern>)
isTextPresent(<WebElement>, <textPattern>)
```
- To check if an alert is present on the current page

```
isAlertPresent(<timeOutInSeconds>)
isAlertPresent()
```

- To performs mouse click action on the element using javascript where native click does not work
```
clickJS(<WebElement>) 
```

- Send text using javascript

```
sendKeysJS(<WebElement>, <val>)
```

- Scrolls to element

```
scroll(<WebElement>)
scroll(<WebElement>, <hAlign>, <vAlign>)
```

- Switch driver to another window:

```
switchWindow(<currentwindowhandle>);
```

- switch driver to another frame:

```
switchFrame(<frameLocator>);
switchFrame(<By>);
switchFrame(<Element>);
switchToDefaultContent();
```

#### 4.2 Test Parameters
The framework includes a thread specific Context object can be used to share any data across multiple step classes or be accessed from any test code including page objects.  This object includes a map variable that can be used to store and share values or objects throughout the executing thread.

```
TestParameters.getInstance().putTestDataClass(<key>, <object>);
TestParameters.getInstance().putTestData("foo", "bar");
.....

- To pull back test data from the test context for the current thread :

(<objectType>) TestParameters.getInstance().getTestDataClass(<key>); //casting to required object type
TestParameters.getInstance().getTestData("foo");
```
#### 4.3 Property Helper
- To read a property file from a given file path:

```
PropertiesConfiguration props = Property.getProperties(<filepath>);			
```

- To get a property value from a given property file:

```
String val = Property.getProperty(<filepath>, <key>);
```

- To get set of values from a given property file where multiple entries exist for same key:

```
String[] vals = Property.getPropertyArray(<filepath>, <key>);
```

- To get a java system property or environment variable value:

```
String val = Property.getVariable(<variable>);
```

- To get a property value from a defaultSettings.properties file:

```
String val = Property.getDefaultProperty(<variable>);
```

- To get a property value from a environment specific properties file: <BR>
E.g.: If -Denv="dev" has been passed to start a test run, then the values configured in the dev.properties file will be referred

```
String val = Property.getEnvSpecificAppParameters(<variable>);
```

#### 4.4 ExcelHelper

The environment specific excel sheet name should be updated in the properties file <Br>
E.g. File name in dev.properties:
```
TestDataExcel=DevTestDataExcel.xlsx
```

The location should be **\src\test\resources\testData**

The first column of data excel should have the scenario title. The framework will automatically match the scenario in the feature file & excel file(Scenario title is the unique key) and get the data based on the below details provided during run time

```
String val = ExcelHelper.getData(<worksheet>, <columnName>);
```
The user can also store values to the excel sheet using below method:
```
ExcelHelper.setData(<worksheet>, <columnName>, <dataToBeStored>);
```
Example Excel sheet format(First column containing scenario title is the primary key):

| Scenario  |  DeviceID           | Device Name                                                      |
| -------- | -----------------------| ------------------------------------------------------------ |
| As a customer, I want to launch a device     | Sample_device_1              | Sample-Device-1 |

For scenario Outlines which is having multiple set of data, which is similar to the below sample feature file
```
Feature: Product Journey Engine
  Scenario Outline: As a customer, I want to launch multiple devices 
    Given the application "DeviceManager"
    When the user login into device manager application
    Then launch the devide with <DeviceID>
  
  Example:
    | DeviceID        |
    | sample_device_1 |
    | sample_device_2 |
    | sample_device_3 |
```
Then below methods can be used to get and set data:
```
String val = ExcelHelper.getSubScenarioData(<worksheet>, <columnName>, <secondaryKey>);
ExcelHelper.getSubScenarioData(<worksheet>, <columnName>, <secondaryKey>, <dataToBeStored>);
```
- The first column cointaining scenario title in the excel file will be the primary key
- The second column in the excel sheet will be the secondary key

Example Excel sheet format:

| Scenario  |  DeviceID           | Device Name                                                      |
| -------- | -----------------------| ------------------------------------------------------------ |
| As a customer, I want to launch multiple devices     | sample_device_1              | Sample-Device-1 |
|                                                      | sample_device_2              | Sample-Device-2 |
|                                                      | sample_device_3              | Sample-Device-3 |