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
# Set up push notifications

Push notifications allow your app server to communicate with your app or your users on a push-basis. You can use push notifications to send out visual notifications to your users, update your badges, or trigger a background task to run code locally.


## Step 1: Register with Windows Notification Service

You'll first need to register your app with Windows Notification Service before you can use push notifications.

Log into the [**Azure Portal**](https://portal.azure.com), and in the top left menu, open **Azure Active Directory** and then open **App registrations**.

**Create a new app registration** (or use an existing one). Then, **copy your Application (client) ID**. You'll also want to create a new secret and save the secret value for later.


## Step 2: Install NuGet package

In your **Windows app project**, install the **unlisted** [Reunion NuGet package](https://www.nuget.org/packages/aleader.Reunion/). Note you must use the Package Manager Console to install it as it's unlisted and won't be installable from the UI. This package allows you to generate notifications via objects instead of raw XML. And for Win32 apps, it also allows you to use notifications from non-UWP apps.

1. In Visual Studio, with your project open, go to `Tools -> NuGet Package Manager -> Package Manager Console`
1. Ensure your correct project is selected for "Default project"
1. Execute `Install-Package aleader.Reunion -Version 7.0.0-build.205.gf55c49e8d8`

> [!IMPORTANT]
> .NET Framework Win32 apps that still use packages.config must migrate to PackageReference, otherwise the Windows 10 SDKs won't be referenced correctly. In your project, right-click on "References", and click "Migrate packages.config to PackageReference".
> 
> .NET Core 3.0 WPF apps must update to .NET Core 3.1, otherwise the APIs will be absent.


## Step 3: Add namespace declarations

```csharp
using Microsoft.UI.Notifications; // Reunion library
```


## Step 4: Obtain a push channel

A push channel is what your server uses to communicate to your app. The channel is tied to the specific device it was created on, so the channel provides a way to send a push notification to a specific device.

```csharp
PushChannelRequest channelRequest = await PushManager.CreateChannelAsync("your-azure-application-id");

if (channelRequest.Channel != null)
{
    string channelUri = channelRequest.Channel.Uri;

    // Send the push channel to your server
    await SendChannelAsync(channelUri); // You must create this method, explanation of it in next step
}
else
{
    // Creating channel failed, see error for info
    var error = channelRequest.Error;
}
```

## Step 5: Send the push channel to your server

Once you've locally obtained a push channel, you need to send the push channel to your web server. You'll probably want to associate your channel with some other information. First, you need to obtain a constant device ID, because new channel URIs can be created every 24 hours and are not constant to the device.

## Step 6: Send a push notification

See the other docs for sending notifications.