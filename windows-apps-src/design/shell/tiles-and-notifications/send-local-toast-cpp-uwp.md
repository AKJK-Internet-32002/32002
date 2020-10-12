---
Description: Learn how to send a local toast notification from a C++ UWP app and handle the user clicking the toast.
title: Send a local toast notification from a C++ UWP app
ms.assetid: E9AB7156-A29E-4ED7-B286-DA4A6E683638
label: Send a local toast notification from a C++ UWP app
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp, send toast notifications, notifications, send notifications, toast notifications, how to, quickstart, getting started, code sample, walkthrough, cpp, c++, uwp
ms.localizationpriority: medium
---
# Send a local toast notification from C++ UWP apps


A toast notification is a message that an app can construct and deliver to the user while they are not currently inside your app. This Quickstart walks you through the steps to create, deliver, and display a Windows 10 toast notification with the new adaptive templates and interactive actions. These actions are demonstrated through a local notification, which is the simplest notification to implement.

> **Important APIs**: [ToastNotification Class](/uwp/api/Windows.UI.Notifications.ToastNotification), [ToastNotificationActivatedEventArgs Class](/uwp/api/Windows.ApplicationModel.Activation.ToastNotificationActivatedEventArgs)



## Step 1: Install NuGet package

Install the [Microsoft.Toolkit.Uwp.Notifications NuGet package 7.0 preview](https://www.nuget.org/packages/aleader.Microsoft.Toolkit.Uwp.Notifications/). Our code sample will use this package. This package allows you to create toast notifications without using XML.


## Step 2: Add namespace declarations

```cpp
using namespace Microsoft::Toolkit::Uwp::Notifications;
```


## Step 3: Send a toast

In Windows 10, your toast notification content is described using an adaptive language that allows great flexibility with how your notification looks. See the [toast content documentation](adaptive-interactive-toasts.md) for more information.

We'll start with a simple text-based notification. Construct the notification content (using the Notifications library), and show the notification!

<img alt="Simple text notification" src="images/send-toast-01.png" width="364"/>

```cpp
// Construct the content and show the toast!
(ref new ToastContentBuilder())
    ->AddText("Andrew sent you a picture")
    ->AddText("Check this out, Happy Canyon in Utah!")
    ->Show();
```


## Step 4: Handling activation

When the user clicks your notification (or a button on the notification with foreground activation), your app's **App.xaml.cpp** **OnActivated** will be invoked.

**App.xaml.cpp**

```cpp
void App::OnActivated(IActivatedEventArgs^ e)
{
    // Handle notification activation
    if (e->Kind == ActivationKind::ToastNotification)
    {
        ToastNotificationActivatedEventArgs^ toastActivationArgs = (ToastNotificationActivatedEventArgs^)e;

        // Obtain the arguments from the notification
        auto args = toastActivationArgs->Argument;

        // Obtain any user input (text boxes, menu selections) from the notification
        auto userInput = toastActivationArgs->UserInput;
 
        // TODO: Show the corresponding content
    }
}
```

> [!IMPORTANT]
> You must initialize your frame and activate your window just like your **OnLaunched** code. **OnLaunched is NOT called if the user clicks on your toast**, even if your app was closed and is launching for the first time. We often recommend combining **OnLaunched** and **OnActivated** into your own `OnLaunchedOrActivated` method since the same initialization needs to occur in both.


## Activation in depth

The first step in making your notifications actionable is to add some launch args to your notification, so that your app can know what to launch when the user clicks the notification (in this case, we're including some information that later tells us we should open a conversation, and we know which specific conversation to open).

```cpp
// Construct the content and show the toast!
(ref new ToastContentBuilder())

    // Arguments returned when user taps body of notification
    ->AddToastActivationInfo("action=viewConversation&conversationId=38392")

    ->AddText("Andrew sent you a picture")
    ->Show();
```



## Adding images

You can add rich content to notifications. We'll add an inline image and a profile (app logo override) image.

> [!NOTE]
> Images can be used from the app's package, the app's local storage, or from the web. As of the Fall Creators Update, web images can be up to 3 MB on normal connections and 1 MB on metered connections. On devices not yet running the Fall Creators Update, web images must be no larger than 200 KB.

<img alt="Toast with images" src="images/send-toast-02.png" width="364"/>

```cpp
// Construct the content and show the toast!
(ref new ToastContentBuilder())
    ...

    // Inline image
    ->AddInlineImage(ref new Uri("https://picsum.photos/360/202?image=883"))

    // Profile (app logo override) image
    ->AddAppLogoOverride(ref new Uri("ms-appdata:///local/Andrew.jpg"), ToastGenericAppLogoCrop::Circle)
    
    ->Show();
```



## Adding buttons and inputs

You can add buttons and inputs to make your notifications interactive. Buttons can launch your foreground app, a protocol, or your background task. We'll add a reply text box, a "Like" button, and a "View" button that opens the image.

<img alt="Toast with images and buttons" src="images/send-toast-03.png" width="364"/>

```cpp
// Construct the content
(ref new ToastContentBuilder())
    ...

    // Text box for replying
    ->AddInputTextBox("tbReply", "Type a response")

    // Reference the text box's ID in order to place this button next to the text box
    ->AddButton("tbReply", "Reply", "action=reply&conversationId=39382" ref new Uri("Assets/Reply.png", UriKind::Relative))

    ->AddButton("Like", ToastActivationType::Background, "action=like&conversationId=39382")

    ->AddButton("View", ToastActivationType::Foreground, "action=view&conversationId=39382")
    
    ->Show();
```

The activation of foreground buttons are handled in the same way as the main toast body (your App.xaml.cpp OnActivated will be called).



## Handling background activation

When you specify background activation on your toast (or on a button inside the toast), your background task will be executed instead of activating your foreground app.

For more information on background tasks, please see [Support your app with background tasks](/windows/uwp/launch-resume/support-your-app-with-background-tasks).

If you are targeting build 14393 or higher, you can use in-process background tasks, which greatly simplify things. Note that in-process background tasks will fail to run on older versions of Windows. We'll use an in-process background task in this code sample.

```csharp
const string taskName = "ToastBackgroundTask";

// If background task is already registered, do nothing
if (BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name.Equals(taskName)))
    return;

// Otherwise request access
BackgroundAccessStatus status = await BackgroundExecutionManager.RequestAccessAsync();

// Create the background task
BackgroundTaskBuilder builder = new BackgroundTaskBuilder()
{
    Name = taskName
};

// Assign the toast action trigger
builder.SetTrigger(new ToastNotificationActionTrigger());

// And register the task
BackgroundTaskRegistration registration = builder.Register();
```


Then in your App.xaml.cs, override the OnBackgroundActivated method. You can then retrieve the pre-defined arguments and user input, similar to the foreground activation.

**App.xaml.cs**

```csharp
protected override async void OnBackgroundActivated(BackgroundActivatedEventArgs args)
{
    var deferral = args.TaskInstance.GetDeferral();
 
    switch (args.TaskInstance.Task.Name)
    {
        case "ToastBackgroundTask":
            var details = args.TaskInstance.TriggerDetails as ToastNotificationActionTriggerDetail;
            if (details != null)
            {
                string arguments = details.Argument;
                var userInput = details.UserInput;

                // Perform tasks
            }
            break;
    }
 
    deferral.Complete();
}
```


## Set an expiration time

In Windows 10, all toast notifications go in Action Center after they are dismissed or ignored by the user, so users can look at your notification after the popup is gone.

However, if the message in your notification is only relevant for a period of time, you should set an expiration time on the toast notification so the users do not see stale information from your app. For example, if a promotion is only valid for 12 hours, set the expiration time to 12 hours. In the code below, we set the expiration time to be 2 days.

> [!NOTE]
> The default and maximum expiration time for local toast notifications is 3 days.

```cpp
// Create toast content and show the toast!
(ref new ToastContentBuilder())
    ->AddText("Expires in 2 days...")
    ->Show(toast =>
    {
        toast->ExpirationTime = DateTime::Now->AddDays(2);
    });
```


## Provide a primary key for your toast

If you want to programmatically remove or replace the notification you send, you need to use the Tag property (and optionally the Group property) to provide a primary key for your notification. Then, you can use this primary key in the future to remove or replace the notification.

To see more details on replacing/removing already delivered toast notifications, please see [Quickstart: Managing toast notifications in action center (XAML)](https://docs.microsoft.com/previous-versions/windows/apps/dn631260(v=win.10)).

Tag and Group combined act as a composite primary key. Group is the more generic identifier, where you can assign groups like "wallPosts", "messages", "friendRequests", etc. And then Tag should uniquely identify the notification itself from within the group. By using a generic group, you can then remove all notifications from that group by using the [RemoveGroup API](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationHistory#Windows_UI_Notifications_ToastNotificationHistory_RemoveGroup_System_String_).

```cpp
// Create toast content and show the toast!
(ref new ToastContentBuilder())
    ->AddText("New post on your wall!")
    ->Show(toast =>
    {
        toast.Tag = "18365";
        toast.Group = "wallPosts";
    });
```



## Clear your notifications

Apps are responsible for removing and clearing their own notifications. When your app is launched, we do NOT automatically clear your notifications.

Windows will only automatically remove a notification if the user explicitly clicks the notification.

Here's an example of what a messaging app should do…

1. User receives multiple toasts about new messages in a conversation
2. User taps one of those toasts to open the conversation
3. The app opens the conversation and then clears all toasts for that conversation (by using [RemoveGroup](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationHistory#Windows_UI_Notifications_ToastNotificationHistory_RemoveGroup_System_String_) on the app-supplied group for that conversation)
4. User's Action Center now properly reflects the notification state, since there are no stale notifications for that conversation left in Action Center.

To learn about clearing all notifications or removing specific notifications, see [Quickstart: Managing toast notifications in action center (XAML)](https://docs.microsoft.com/previous-versions/windows/apps/dn631260(v=win.10)).

```cpp
ToastNotificationManagerCompat::History->Clear();
```



## Resources

* [Full code sample on GitHub](https://github.com/WindowsNotifications/quickstart-sending-local-toast-win10)
* [Toast content documentation](adaptive-interactive-toasts.md)
* [ToastNotification Class](/uwp/api/Windows.UI.Notifications.ToastNotification)
* [ToastNotificationActivatedEventArgs Class](/uwp/api/Windows.ApplicationModel.Activation.ToastNotificationActivatedEventArgs)