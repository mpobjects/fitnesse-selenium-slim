[![Build Status](https://travis-ci.org/mpobjects/fitnesse-selenium-slim.svg?branch=master)](https://travis-ci.org/mpobjects/fitnesse-selenium-slim)
[![Maven Central](https://img.shields.io/maven-central/v/com.mpobjects/fitnesse-selenium-slim.svg)](http://www.maven.org/#search%7Cga%7C1%7Cg%3A%22com.mpobjects%22%20AND%20a%3A%22fitnesse-selenium-slim%22)
![Sonatype Nexus (Snapshots)](https://img.shields.io/nexus/s/https/oss.sonatype.org/com.mpobjects/fitnesse-selenium-slim.svg)

# fitnesse-selenium-slim

[FitNesse](https://github.com/unclebob/fitnesse) Selenium fixture in [slim format](http://www.fitnesse.org/FitNesse.UserGuide.WritingAcceptanceTests.SliM). Resembles [Xebium](http://xebia.github.io/Xebium/), but it&apos;s even more similar to [Selenium IDE Firefox Plugin](http://www.seleniumhq.org/projects/ide/). Also gets inspiration from [hsac-fitnesse-fixtures](https://github.com/fhoeben/hsac-fitnesse-fixtures) but doesn&apos;t try to &quot;simplify&quot; Selenium IDE development flow. This project is licensed under [MIT](LICENSE).

This is a continued fork from [André Albino Pereira's original work](https://github.com/andreptb/fitnesse-selenium-slim).

* [Getting started](#getting-started)
* [Installation](#installation)
* [Testing and Building](#testing-and-building)
* [Advanced](#advanced)
  * [Aborting test on first failure](#aborting-test-on-first-failure)
  * [Screenshots](#screenshots)
  * [Wait behavior](#wait-behavior)
  * [Browser downloads](#browser-downloads)
  * [Dry run](#dry-run)



###  Getting started

Since the plugin tests itself with FitNesse, take a look at [this](fitnesse/FitNesseRoot/FitNesseSeleniumSlim/BasicUsageSample/content.txt) FitNesseRoot test. Furthermore, detailed information about the available fixture commands can be found  [here](http://andreptb.github.io/fitnesse-selenium-slim/apidocs/com/github/andreptb/fitnesse/SeleniumFixture.html#startBrowser-java.lang.String-).

### Installation

* This module and selenium dependencies must be in [FitNesse classpath](http://www.fitnesse.org/FitNesse.FullReferenceGuide.UserGuide.WritingAcceptanceTests.ClassPath). You can download the jars from [here](http://repo1.maven.org/maven2/com/github/andreptb/fitnesse-selenium-slim/) or with [maven](https://github.com/lvonk/fitnesse-maven-classpath) (see below).
* The [WebDriver](http://www.seleniumhq.org/projects/webdriver/) which the fixture will be used to connect also must be on [FitNesse](https://github.com/unclebob/fitnesse) classpath.

```xml
<dependency>
  <groupId>com.github.andreptb</groupId>
  <artifactId>fitnesse-selenium-slim</artifactId>
  <version>1.0.3</version>
</dependency>
```

### Testing and Building
```
BROWSER=firefox mvn test
```

* To start FitNesse server and navigate through samples:

```
mvn exec:java -Dexec.mainClass=fitnesseMain.FitNesseMain -Dexec.args="-d fitnesse" -DBROWSER=firefox
```

* To build this plugin and add to maven local repository:

```
mvn install -Dgpg.skip
```

### Advanced

The following sections details some advanced features provided by this plugin.

#### Aborting test on first failure

In some use cases, such as when you select an option that changes form fields in the page, if selenium can't select this option it wouldn't make sense to go on with the test.
Since subsequent actions will respect **wait behavior**, this test would waste a lot of time executing actions even though it will fail for sure.

To address this, the action **[stop test on first failure](http://andreptb.github.io/fitnesse-selenium-slim/apidocs/com/github/andreptb/fitnesse/SeleniumFixture.html#stopTestOnFirstFailure-java.lang.String-)** can be enabled. This will abort all actions that come after the action that failed. Note that this flag is kept across tests and suites, so if this functionality is needed, it's wise to configure it properly in each suite or test **SetUp**.

Some situations will cause the test to be aborted even if **[stop test on first failure](http://andreptb.github.io/fitnesse-selenium-slim/apidocs/com/github/andreptb/fitnesse/SeleniumFixture.html#stopTestOnFirstFailure-java.lang.String-)** is **false**, such as using an action that requires the browser to be started (with **start browser**.

#### Screenshots

This plugin provides a screenshot feature, showing the screenshot preview (and link) similar to [hsac-fitnesse-plugin](https://github.com/fhoeben/hsac-fitnesse-plugin). Screenshots can be triggered when:

* An assertion fails, mostly when an element can't be found in the page or it's contents is somehow unexpected.
* Manually, by using along with **screenshot** action along with **show** prefix, as demonstrated below:

```
| selenium |
|
| show | screenshot |
```

**Known limitations:**

* If a [dialog is present the screenshot action will fail, throwing UnhandledAlertException](https://code.google.com/p/selenium/issues/detail?id=4412).
* If an action preceding the screenshot fails and **[stop test on first failure](http://andreptb.github.io/fitnesse-selenium-slim/apidocs/com/github/andreptb/fitnesse/SeleniumFixture.html#stopTestOnFirstFailure-java.lang.String-)** is enabled, subsequent screenshot actions will also be aborted.  
* Browsers must support [data scheme](https://en.wikipedia.org/wiki/Data_URI_scheme) to properly visualize screenshots via FitNesse UI.

#### Wait behavior

Every actions that involves searching for elements within the page will respect the specified timeout configuration before failing. You can change the timeout configuration with the following:

```
| selenium |
| note | changes timeout configuration for 5 seconds (default is 20) |
| set wait timeout | 5 |
```

**Important:**
* If **[stop test on first failure](http://andreptb.github.io/fitnesse-selenium-slim/apidocs/com/github/andreptb/fitnesse/SeleniumFixture.html#stopTestOnFirstFailure-java.lang.String-)** is disabled, **present** action will return false if timeout is reached and no element was found with the given selector.
* Wait behavior using [FitNesse Slim action](http://www.fitnesse.org/FitNesse.FullReferenceGuide.UserGuide.WritingAcceptanceTests.SliM.ScriptTable) such as **ensure**, **reject**, **check** and **check not** will only work properly if **selenium** table is used.

#### Browser downloads

This plugin applies default configurations for **Firefox** and **Chrome** to download files without opening confirmation dialogs.
If you wish to change the default download directory, you can use specific browser preferences, as described **[here (Firefox) ](http://stackoverflow.com/questions/25240468/change-firefox-default-download-settings-within-selenium)** and **[here (Chrome)](http://stackoverflow.com/questions/23530399/chrome-web-driver-download-files)**. Take a look at [this test](fitnesse/FitNesseRoot/FitNesseSeleniumSlim/SeleniumFixtureTests/ManualTests/DownloadFileTest/content.txt) for a sample on how changing the default download directory can be achieved with **Firefox** and **Chrome**.

However, as mentioned [here](http://stackoverflow.com/questions/12002324/waiting-for-file-to-download-on-selenium-grid) and [here](https://blog.codecentric.de/en/2010/07/file-downloads-with-selenium-mission-impossible/), accessing downloaded files proves to be a rather difficult task, especially when working with remote test providers such as [Selenium Grid](http://www.seleniumhq.org/projects/grid/) and [SauceLabs](https://saucelabs.com/). In these situations, another technique must be used. For example, if you're running selenium grid with **[Docker](https://www.docker.com/)**, you could create **[volumes](https://docs.docker.com/userguide/dockervolumes/)** to provide filesystem access between the container with the Selenium Node running the test and the container running FitNesse itself.

#### Dry run

Development with selenium can be slow, specially when tests contain many assertions. With FitNesse in the mix, currently there's no easy way to assert if code syntax and driver configuration is valid without having to effectively run the test.

This plugin offers a **dry run** mode, aiming to address just that. When enabled, the plugin runs your tests in a blank page and will fail only if:
  * The driver connection is incorrect or the browser is not available to receive selenium commands.
  * There are FitNesse or Selenium syntax errors.

As this practice [implies](https://en.wikipedia.org/wiki/Dry_run_(testing)), this feature intends to help with the following tasks:
  * Mitigate FitNesse and Selenium syntax problems in your test pages.
  * Verify the test effective order of execution and their resources (e.g. which setup page comes before the test itself)
  * Provide a quick sanity check as fast as possible.

Please note that the action **start browser** must be executed **before** dry run mode is enabled. Take a look at [this test](fitnesse/FitNesseRoot/FitNesseSeleniumSlim/SeleniumFixtureTests/ManualTests/DryRunTest/content.txt) for an usage example.
