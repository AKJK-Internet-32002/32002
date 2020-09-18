---
Description: Learn how to send a local toast notification and handle the user clicking the toast from C# apps.
title: Send a local toast notification from C# apps
ms.assetid: E9AB7156-A29E-4ED7-B286-DA4A6E683638
label: Send a local toast notification from C# apps
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp, send toast notifications, notifications, send notifications, toast notifications, how to, quickstart, getting started, code sample, walkthrough, c#
ms.localizationpriority: medium
---


# Set up push notifications using App Pad

App Pad is the easiest way to set up push notifications for your Windows app. Push notifications allow your app server to communicate with your app or your users on a push-basis. You can use push notifications to send out visual notifications to your users, update your badges, or trigger a background task to run code locally.


## Step 1: Create an App Pad App

If you haven't already created your app in App Pad, sign in to the [**Azure App Pad portal**](https://apppadportal.z22.web.core.windows.net/azure) and create a new app.


## Step 2: Enable push

Inside your **App Pad App**, select the **Push** tab and click **Set up a client**. You can follow the personalized instructions in the portal; the instructions below are copied for convenience.


### Step 2.1: Install NuGet package

In your **Windows app project**, install the **unlisted** [AppPad.Push NuGet package](https://ms-andrewbares.visualstudio.com/AppPad/_packaging?_a=package&feed=AppPad&package=AzureExplorations.AppPad.Push&protocolType=NuGet). Note that this package is currently on an internal feed and is also a prerelease package.

> [!IMPORTANT]
> .NET Framework Win32 apps that still use packages.config must migrate to PackageReference, otherwise the Windows 10 SDKs won't be referenced correctly. In your project, right-click on "References", and click "Migrate packages.config to PackageReference".
> 
> .NET Core 3.0 WPF apps must update to .NET Core 3.1, otherwise the APIs will be absent.


### Step 2.2: Add namespace declarations

```csharp
using Microsoft.Azure.AppPad; // App Pad library
```


### Step 2.3: Start the SDK

Inside the **constructor** of your app's **App.xaml.cs** file, add or update the following such that it includes **Push**. The instructions from the portal will contain your client ID.

```csharp
AppPad.Start("{your-client-id}", typeof(Push));
```

## Step 3: Launch your app

Launch your app so that App Pad has a chance to obtain a push channel and send it up to the server.

## Step 4: Send a test notification!

In your App Pad App's client setup page, click the button to send a test notification. You should see a notification appear, even if you closed your app!