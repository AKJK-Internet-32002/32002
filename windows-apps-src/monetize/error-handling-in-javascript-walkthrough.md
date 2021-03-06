---
author: mcleanbyron
ms.assetid: 08b4ae43-69e8-4424-b3c0-a07c93d275c3
description: Learn how to catch AdControl errors in your app.
title: Error handling in JavaScript walkthrough
ms.author: mcleans
ms.date: 08/23/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, ads, advertising, error handling, javascript
ms.localizationpriority: medium
---

# Error handling in JavaScript walkthrough

This walkthrough demonstrates how to catch ad-related errors in your JavaScript app. This walkthrough uses an [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx), but the general concepts in it also apply to [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) and [NativeAdsManager](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.nativeadsmanager.aspx).

These examples assume that you have a JavaScript app that contains an **AdControl**. For step-by-step instructions that demonstrate how to add an **AdControl** to your app, see [AdControl in HTML 5 and Javascript](adcontrol-in-html-5-and-javascript.md). For a complete sample project that demonstrates how to add banner ads to a JavaScript/HTML app, see the [advertising samples on GitHub](http://aka.ms/githubads).

1.  In the default.html file, add a value for the **onErrorOccurred** event where you define the **data-win-options** in the **div** for the **AdControl**. Find the following code in the default.html file.
    ``` HTML
    <div id="myAd" style="position: absolute; top: 53px; left: 0px; width: 300px; height: 250px; z-index: 1"
      data-win-control="MicrosoftNSJS.Advertising.AdControl"
      data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: 'test'}">
    </div>
    ```
    Following the **adUnitId** attribute, add the value for the **onErrorOccurred** event.
    ``` HTML
    <div id="myAd" style="position: absolute; top: 53px; left: 0px; width: 300px; height: 250px; z-index: 1"
      data-win-control="MicrosoftNSJS.Advertising.AdControl"
      data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: 'test', onErrorOccurred: errorLogger}">
  </div>
  ```

2.  Create a **div** that will display text so you can see the messages being generated. To do this, add the following code after the **div** for **myAd**.
    ``` HTML
    <div style="position:absolute; width:100%; height:130px; top:300px; left:0px">
        <b>Ad Events</b><br />
        <div id="adEvents" style="width:100%; height:110px; overflow:auto"></div>
    </div>
    ```

3.  Create an **AdControl** that will trigger an error event. There can only be one application id for all **AdControl** objects in an app. So creating an additional one with a different application id will trigger an error at runtime. To do this, after the previous **div** sections you have added, add the following code to the body of the default.html page.
    ``` HTML
    <!-- Because only one applicationId can be used, the following ad control will fire an error event. -->
    <div id="liveAd" style="position: absolute; top:500px; left:0px; width:480px; height:80px"
      data-win-control="MicrosoftNSJS.Advertising.AdControl"
      data-win-options="{applicationId: '00000000-0000-0000-0000-000000000000', adUnitId: 'test', onErrorOccurred: errorLogger }" >
    </div>
    ```

4.  In the project???s default.js file, after the default initialization function, you will add the event handler for **errorLogger**. Scroll to the end of the file and after the last semi-colon is where you will put the following code.
    ``` javascript
    WinJS.Utilities.markSupportedForProcessing(
    window.errorLogger = function (sender, evt) {
        adEvents.innerHTML = (new Date()).toLocaleTimeString() + ": " +
        sender.element.id + " error: " + evt.errorMessage + " error code: " +
        evt.errorCode + "<br>" + adEvents.innerHTML;
        console.log("errorhandler hit. \n");
    });
    ```

5.  Build and run the file. You will see the original ad from the sample app you built previously and text under that ad describing the error. You will not see the ad with the id of **liveAd**.

## Related topics

* [Advertising samples on GitHub](http://aka.ms/githubads)
