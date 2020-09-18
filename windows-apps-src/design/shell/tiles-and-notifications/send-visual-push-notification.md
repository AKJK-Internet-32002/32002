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
# Send a visual push notification

A visual push notification is a push notification that is displayed directly in Action Center, without waking up your app. This quickstart walks you through the steps to create and send a visual push notification.

## Step 1: Set up push notifications

If you haven't already obtained a push channel, complete the [Set up push notifications](set-up-push-notifications.md) documentation first.

## Step 2: Install NuGet package

In your **server project** where you want to send push notifications from, install the **unlisted** [Reunion.Server NuGet package](https://www.nuget.org/packages/aleader.Reunion/). Note you must use the Package Manager Console to install it as it's unlisted and won't be installable from the UI. This package allows you to generate notifications via objects instead of raw XML. And for Win32 apps, it also allows you to use notifications from non-UWP apps.

1. In Visual Studio, with your project open, go to `Tools -> NuGet Package Manager -> Package Manager Console`
1. Ensure your correct project is selected for "Default project"
1. Execute `Install-Package aleader.Reunion -Version 7.0.0-build.205.gf55c49e8d8`



## Step 3: Add namespace declarations

```csharp
using Microsoft.UI.Notifications; // Reunion library
using Microsoft.UI.Notifications.Server; // Reunion library
```


## Step 4: Start the SDK

In your startup code, add the following code.

```csharp
PushServerManager.Start("your-application-secret-from-azure-app-registration");
```


## Step 5: Send a visual push notification

```csharp
string channelUri = "your-channel-from-step-1";

// Construct the content and send the notification!
await new NotificationBuilder()
    .SetLaunchArgs("picOfHappyCanyon")
    .AddText("Andrew sent you a picture")
    .AddText("Check this out, Happy Canyon in Utah!")
    .SendAsync(channelUri);
```