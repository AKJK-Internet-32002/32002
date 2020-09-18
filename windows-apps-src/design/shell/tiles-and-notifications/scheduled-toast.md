---
Description: Learn how to schedule a local toast notification to appear at a later time.
title: Schedule a toast notification
label: Schedule a toast notification
template: detail.hbs
ms.date: 04/09/2020
ms.topic: article
keywords: windows 10, uwp, scheduled toast notification, scheduledtoastnotification, how to, quickstart, getting started, code sample, walkthrough
ms.localizationpriority: medium
---

# Schedule a notification

Scheduled notifications allow you to schedule a notification to appear at a later time, regardless of whether your app is running at that time. This is useful for scenarios like displaying reminders or other follow-up tasks for the user, where the time and content of the notification is known ahead-of-time.

Note that scheduled notifications have a delivery window of 5 minutes. If the computer is turned off during the scheduled delivery time, and remains off for longer than 5 minutes, the notification will be "dropped" as no longer relevant to the user. If you need guaranteed delivery of notifications regardless of how long the computer was off, we recommend using a background task with a time trigger, as illustrated in [this code sample](https://github.com/WindowsNotifications/quickstart-snoozable-toasts-even-if-computer-is-off).


## Step 1: Install NuGet package

Install the **unlisted** [Reunion NuGet package](https://www.nuget.org/packages/aleader.Reunion/). Note you must use the Package Manager Console to install it as it's unlisted and won't be installable from the UI. This package allows you to generate notifications via objects instead of raw XML. And for Win32 apps, it also allows you to use notifications from non-UWP apps.

1. In Visual Studio, with your project open, go to `Tools -> NuGet Package Manager -> Package Manager Console`
1. Ensure your correct project is selected for "Default project"
1. Execute `Install-Package aleader.Reunion -Version 7.0.0-build.205.gf55c49e8d8`

> [!IMPORTANT]
> .NET Framework Win32 apps that still use packages.config must migrate to PackageReference, otherwise the Windows 10 SDKs won't be referenced correctly. In your project, right-click on "References", and click "Migrate packages.config to PackageReference".
> 
> .NET Core 3.0 WPF apps must update to .NET Core 3.1, otherwise the APIs will be absent.


## Step 2: Add namespace declarations

```csharp
using Microsoft.UI.Notifications; // Reunion library
```


## Step 3: Schedule the notification

Your notification content is described using an adaptive language that allows great flexibility with how your notification looks. See the [notification content documentation](adaptive-interactive-toasts.md) for more information.

We'll use a simple text-based notification reminding a student about the homework they have due today. Construct the notification and schedule it!

```csharp
// Construct the content and schedule the notification to appear in 5 seconds!
new NotificationBuilder()
    .SetLaunchArgs("itemsDueToday")
    .AddText("ASTR 170B1")
    .AddText("You have 3 items due today!")
    .Schedule(DateTime.Now.AddSeconds(5));
```

> [!IMPORTANT]
> Win32 non-MSIX/sparse apps must use the **Schedule** method as seen above. If you use **ToastNotificationManager** itself, you will receive an element not found exception.

After that code executes, you should see a notification in 5 seconds! You can close your app and the notification will still appear at the scheduled time.


## Step 4: Handling activation

Activation of scheduled notifications is identical to normal notifications. See the [Send local notification docs](send-local-toast.md) to learn how to handle activation.


## Provide a primary key for your notification

If you want to programmatically cancel, remove, or replace the scheduled notification, you need to use the Tag property (and optionally the Group property) to provide a primary key for your notification. Then, you can use this primary key in the future to cancel, remove, or replace the notification.

To see more details on replacing/removing already delivered notifications, please see [Quickstart: Managing notifications in action center (XAML)](https://docs.microsoft.com/previous-versions/windows/apps/dn631260(v=win.10)).

Tag and Group combined act as a composite primary key. Group is the more generic identifier, where you can assign groups like "wallPosts", "messages", "friendRequests", etc. And then Tag should uniquely identify the notification itself from within the group. By using a generic group, you can then remove all notifications from that group by using the [RemoveGroup API](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationHistory#Windows_UI_Notifications_ToastNotificationHistory_RemoveGroup_System_String_).

```csharp
// Schedule a notification with a tag and group
var builder = new NotificationBuilder()
    .AddText("Expires in 2 days...")
    .SetTag("18365")
    .SetGroup("wallPosts");
    .Schedule(DateTime.Now.AddSeconds(5));
```


## Cancel scheduled notifications

To cancel a scheduled notification that hasn't been delivered yet, you can call the Cancel function with a tag and group.

```csharp
NotificationManager.Cancel("18365", "ASTR 170B1");
```

To cancel all scheduled notifications, call CancelAll.

```csharp
NotificationManager.CancelAll();
```

Alternatively, you also can obtain all of your scheduled notifications and then cancel a specific notification.

```csharp
List<ScheduledNotification> scheduledNotifs = NotificationManager.GetScheduledNotifications();

ScheduledNotification toRemove = scheduledNotifs.First();

NotificationManager.Cancel(toRemove);
```


## Adding actions, inputs, and more

See the [send a local toast](send-local-toast.md) docs to learn more about advanced topics like actions and inputs. Actions and inputs work the same in local toasts as they do in scheduled toasts.


## Resources

* [Toast content documentation](adaptive-interactive-toasts.md)
* [ScheduledToastNotification Class](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ScheduledToastNotification)
