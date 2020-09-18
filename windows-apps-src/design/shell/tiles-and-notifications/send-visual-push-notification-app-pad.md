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
# Send a visual push notification using App Pad

A visual push notification is a push notification that is displayed directly in Action Center, without waking up your app. This quickstart walks you through the steps to create and send a visual push notification.

## Step 1: Set up push notifications

If you haven't already set up push notifications, complete the [Set up push notifications](set-up-push-notifications-app-pad.md) documentation first.

## Step 2: Add your admin client

Inside your **App Pad App**, select the **Push** tab and click **Set up an admin client**. You can follow the personalized instructions in the portal; the instructions below are copied for convenience.


### Step 2.1: Install NuGet package

In your **Windows app project**, install the **unlisted** [AppPad.Admin NuGet package](https://ms-andrewbares.visualstudio.com/AppPad/_packaging?_a=package&feed=AppPad&package=AzureExplorations.AppPad.Push&protocolType=NuGet). Note that this package is currently on an internal feed and is also a prerelease package.


### Step 2.2: Add namespace declarations

```csharp
using Microsoft.UI.Notifications; // Installed through App Pad Admin library
using Microsoft.Azure.AppPad.Admin; // App Pad Admin library
```


### Step 2.3: Start the SDK

In your startup code, add the following code. The instructions from the portal will contain your admin client ID.

```csharp
AppPadAdmin.Start("{your-admin-client-id}");
```


## Step 3: Send a visual notification

To send a visual notification to all users, call the **SendToAllAsync** API.

```csharp
// Construct the content and send the notification!
await new NotificationBuilder()
    .SetLaunchArgs("picOfHappyCanyon")
    .AddText("Andrew sent you a picture")
    .AddText("Check this out, Happy Canyon in Utah!")
    .SendToAllAsync();
```