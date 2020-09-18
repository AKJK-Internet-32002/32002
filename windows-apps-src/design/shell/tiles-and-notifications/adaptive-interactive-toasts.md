---
Description: Adaptive and interactive notifications let you create flexible pop-up notifications with more content, optional inline images, and optional user interaction.
title: Toast content
ms.assetid: 1FCE66AF-34B4-436A-9FC9-D0CF4BDA5A01
label: Toast content
template: detail.hbs
ms.date: 11/20/2017
ms.topic: article
keywords: windows 10, uwp, notifications, interactive toasts, adaptive toasts, notification content, notification payload
ms.localizationpriority: medium
---
# Toast content

Adaptive and interactive notifications let you create flexible notifications with text, images, and buttons/inputs.

> **Important APIs**: [UWP Community Toolkit Notifications nuget package](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/)

> [!NOTE]
> To see the legacy templates from Windows 8.1 and Windows Phone 8.1, see the [legacy notification template catalog](https://docs.microsoft.com/previous-versions/windows/apps/hh761494(v=win.10)).


## Getting started

**Install Notifications library.** If you'd like to use C# instead of XML to generate notifications, install the NuGet package named [Microsoft.Toolkit.Uwp.Notifications](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) (search for "notifications uwp"). The C# samples provided in this article use version 1.0.0 of the NuGet package.

**Install Notifications Visualizer.** This free Windows app helps you design interactive notifications by providing an instant visual preview of your notification as you edit it, similar to Visual Studio's XAML editor/design view. See [Notifications Visualizer](notifications-visualizer.md) for more information, or [download Notifications Visualizer from the Store](https://www.microsoft.com/store/apps/notifications-visualizer/9nblggh5xsl1).


## Sending a notification

To learn how to send a notification, see [Send local notification](send-local-notification.md). This documentation only covers creating the notification content.


## Notification structure

Notifications are a combination of some data properties like Tag/Group (which let you identify the notification) and the *notification content*.

The core components of notification content are...
* **launchArgs/launch**: This defines what arguments will be passed back to your app when the user clicks your notification, allowing you to deep link into the correct content that the notification was displaying. To learn more, see [Send local notification](send-local-notification.md).
* **visual**: The visual portion of the notification, including the generic binding that contains text and images.
* **actions**: The interactive portion of the notification, including inputs and actions.
* **audio**: Controls the audio played when the notification is shown to the user.

The notification content is defined in raw XML, but you can use our [NuGet library](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) to get a C# (or C++) object model for constructing the notification content. This article documents everything that goes within the notification content.

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    .SetLaunchArgs("app-defined-string")
    .AddText("Some text")
    .AddButton("Archive", NotificationActivationType.Background, "archive")
    .AddAudio(new Uri("ms-appx:///Sound.mp3"));
```

#### [XML](#tab/xml)

```xml
<toast launch="app-defined-string">

  <visual>
    <binding template="ToastGeneric">
      ...
    </binding>
  </visual>

  <actions>
    ...
  </actions>

  <audio src="ms-winsoundevent:Notification.Reminder"/>

</toast>
```

---



Here is a visual representation of the notification's content:

![notification structure](images/adaptivetoasts-structure.jpg)


## Visual

Each notification must specify a visual, where you must provide a generic notification binding, which can contain text, images, and more. These elements will be rendered on various Windows devices, including desktop, phones, tablets, and Xbox.

For all attributes supported in the visual section and its child elements, [see the schema documentation](toast-schema.md#toastvisual).

Your app's identity on the notification is conveyed via your app icon. However, if you use the app logo override, we will display your app name beneath your lines of text.

| App identity for normal notification | App identity with appLogoOverride |
| -- | -- |
| <img src="images/adaptivetoasts-withoutapplogooverride.jpg" alt="notification without appLogoOverride" width="364"/> | <img alt="notification with appLogoOverride" src="images/adaptivetoasts-withapplogooverride.jpg" width="364"/> |


## Text elements

Each notification must have at least one text element, and can contain two additional text elements, all of type [**AdaptiveText**](toast-schema.md#adaptivetext).

<img alt="Toast with title and description" src="images/toast-title-and-description.jpg" width="364"/>

Since the Windows 10 Anniversary Update, you can control how many lines of text are displayed by using the **HintMaxLines** property on the text. The default (and maximum) is up to 2 lines of text for the title, and up to 4 lines (combined) for the two additional description elements (the second and third **AdaptiveText**).

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    .AddText("Adaptive Tiles Meeting", hintMaxLines: 1)
    .AddText("Conf Room 2001 / Building 135")
    .AddText("10:00 AM - 10:30 AM");
```

#### [XML](#tab/xml)

```xml
<binding template="ToastGeneric">
    <text hint-maxLines="1">Adaptive Tiles Meeting</text>
    <text>Conf Room 2001 / Building 135</text>
    <text>10:00 AM - 10:30 AM</text>
</binding>
```

---


## App logo override

By default, your notification will display your app's logo. However, you can override this logo with your own [**AppLogo**](toast-schema.md#toastgenericapplogo) image. For example, if this is a notification from a person, we recommend overriding the app logo with a picture of that person.

<img alt="Toast with app logo override" src="images/toast-applogooverride.jpg" width="364"/>

You can use the **HintCrop** property to change the cropping of the image. For example, **Circle** results in a circle-cropped image. Otherwise, the image is square. Image dimensions are 48x48 pixels at 100% scaling.

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddAppLogoOverride(new Uri("https://picsum.photos/48?image=883"), NotificationAppLogoCrop.Circle);
```

#### [XML](#tab/xml)

```xml
<binding template="ToastGeneric">
    ...
    <image placement="appLogoOverride" hint-crop="circle" src="https://picsum.photos/48?image=883"/>
</binding>
```

---


## Hero image

**New in Anniversary Update**: Notifications can display a hero image, which is a featured [**HeroImage**](toast-schema.md#toastgenericheroimage) displayed prominently within the notification banner and while inside Action Center. Image dimensions are 364x180 pixels at 100% scaling.

<img alt="Toast with hero image" src="images/toast-heroimage.jpg" width="364"/>

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddHeroImage(new Uri("https://picsum.photos/364/180?image=1043"));
```

#### [XML](#tab/xml)

```xml
<binding template="ToastGeneric">
    ...
    <image placement="hero" src="https://picsum.photos/364/180?image=1043"/>
</binding>
```

---


## Inline image

You can provide a full-width inline-image that appears when the notification is full size.

<img alt="Toast with additional image" src="images/toast-additionalimage.jpg" width="364"/>

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddInlineImage(new Uri("https://picsum.photos/360/202?image=1043"));
```

#### [XML](#tab/xml)

```xml
<binding template="ToastGeneric">
    ...
    <image src="https://picsum.photos/360/202?image=1043" />
</binding>
```

---


## Image size restrictions

The images you use in your notification can be sourced from...

 - http://
 - ms-appx:///
 - ms-appdata:///

For http and https remote web images, there are limits on the file size of each individual image. In the Fall Creators Update (16299), we increased the limit to be 3 MB on normal connections and 1 MB on metered connections. Before that, images were always limited to 200 KB.

| Normal connection | Metered connection | Before Fall Creators Update |
| - | - | - |
| 3 MB | 1 MB | 200 KB |

If an image exceeds the file size, or fails to download, or times out, the image will be dropped and the rest of the notification will be displayed.


## Attribution text

**New in Anniversary Update**: If you need to reference the source of your content, you can use attribution text. This text is always displayed at the bottom of your notification, along with your app's identity or the notification's timestamp.

On older versions of Windows that don't support attribution text, the text will simply be displayed as another text element (assuming you don't already have the maximum of three text elements).

<img alt="Toast with attribution text" src="images/toast-attributiontext.jpg" width="364"/>

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddAttributionText("Via SMS");
```

#### [XML](#tab/xml)

```xml
<binding template="ToastGeneric">
    ...
    <text placement="attribution">Via SMS</text>
</binding>
```

---


## Custom timestamp

**New in Creators Update**: You can now override the system-provided timestamp with your own timestamp that accurately represents when the message/information/content was generated. This timestamp is visible within Action Center.

<img alt="Toast with custom timestamp" src="images/toast-customtimestamp.jpg" width="396"/>

To learn more about using a custom timestamp, please see [custom timestamps on toasts](custom-timestamps-on-toasts.md).

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddCustomTimeStamp(new DateTime(2017, 04, 15, 19, 45, 00, DateTimeKind.Utc));
```

#### [XML](#tab/xml)

```xml
<toast displayTimestamp="2017-04-15T19:45:00Z">
  ...
</toast>
```

---


## Progress bar

**New in Creators Update**: You can provide a progress bar on your notification to keep the user informed of the progress of operations such as downloads.

<img alt="Toast with progress bar" src="images/toast-progressbar.png" width="364"/>

To learn more about using a progress bar, please see [Notification progress bar](toast-progress-bar.md).


## Headers

**New in Creators Update**: You can group notifications under headers within Action Center. For example, you can group messages from a group chat under a header, or group notifications of a common theme under a header, or more.

<img alt="Toasts with header" src="images/toast-headers-action-center.png" width="396"/>

To learn more about using headers, please see [Notification headers](toast-headers.md).


## Adaptive content

**New in Anniversary Update**: In addition to the content specified above, you can also display additional adaptive content that is visible when the notification is expanded.

This additional content is specified using Adaptive, which you can learn more about by reading the [Adaptive Tiles documentation](create-adaptive-tiles.md).

Note that any adaptive content must be contained within an [**AdaptiveGroup**](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/toast-schema#adaptivegroup). Otherwise it will not be rendered using adaptive.


### Columns and text elements

Here's an example where columns and some advanced adaptive text elements are used. Since the text elements are within an **AdaptiveGroup**, they support all the rich adaptive styling properties.

<img alt="Toast with additional text" src="images/toast-additionaltext.jpg" width="364"/>

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddVisualChild(new AdaptiveGroup()
    {
        Children =
        {
            new AdaptiveSubgroup()
            {
                Children =
                {
                    new AdaptiveText()
                    {
                        Text = "52 attendees",
                        HintStyle = AdaptiveTextStyle.Base
                    },
                    new AdaptiveText()
                    {
                        Text = "23 minute drive",
                        HintStyle = AdaptiveTextStyle.CaptionSubtle
                    }
                }
            },
            new AdaptiveSubgroup()
            {
                Children =
                {
                    new AdaptiveText()
                    {
                        Text = "1 Microsoft Way",
                        HintStyle = AdaptiveTextStyle.CaptionSubtle,
                        HintAlign = AdaptiveTextAlign.Right
                    },
                    new AdaptiveText()
                    {
                        Text = "Bellevue, WA 98008",
                        HintStyle = AdaptiveTextStyle.CaptionSubtle,
                        HintAlign = AdaptiveTextAlign.Right
                    }
                }
            }
        }
    });
```

#### [XML](#tab/xml)

```xml
<binding template="ToastGeneric">
    ...
    <group>
        <subgroup>
            <text hint-style="base">52 attendees</text>
            <text hint-style="captionSubtle">23 minute drive</text>
        </subgroup>
        <subgroup>
            <text hint-style="captionSubtle" hint-align="right">1 Microsoft Way</text>
            <text hint-style="captionSubtle" hint-align="right">Bellevue, WA 98008</text>
        </subgroup>
    </group>
</binding>
```

---


## Buttons

Buttons make your notification interactive, letting the user take quick actions on your notification without interrupting their current workflow. For example, users can reply to a message directly from within a notification, or delete an email without even opening the email app. Buttons appear in the expanded portion of your notification.

To learn more about implementing buttons end-to-end, see [Send local notification](send-local-notification.md).

Buttons can perform the following different actions...

-   Activating the app in the foreground, with an argument that can be used to navigate to a specific page/context.
-   Activating the app's background task, for a quick-reply or similar scenario.
-   Activating another app via protocol launch.
-   Performing a system action, such as snoozing or dismissing the notification.

> [!NOTE]
> You can only have up to 5 buttons (including context menu items which we discuss later).

<img alt="notification with actions, example 1" src="images/adaptivetoasts-xmlsample02.jpg" width="364"/>

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddButton("See more details", NotificationActivationType.Foreground, "action=viewdetails&contentId=351")
    .AddButton("Remind me later", NotificationActivationType.Background, "action=remindlater&contentId=351");
```

#### [XML](#tab/xml)

```xml
<toast launch="app-defined-string">

    ...

    <actions>

        <action
            content="See more details"
            arguments="action=viewdetails&amp;contentId=351"
            activationType="foreground"/>

        <action
            content="Remind me later"
            arguments="action=remindlater&amp;contentId=351"
            activationType="background"/>

    </actions>

</toast>
```

---


### Buttons with icons

You can add icons to your buttons. These icons are white transparent 16x16 pixel images at 100% scaling, and should have no padding included in the image itself. If you choose to provide icons on a notification, you must provide icons for ALL of your buttons in the notification, as it transforms the style of your buttons into icon buttons.

> [!NOTE]
> For accessibility, be sure to include a contrast-white version of the icon (a black icon for white backgrounds), so that when the user turns on High Contrast White mode, your icon is visible. Learn more on the [toast accessiblity page](tile-toast-language-scale-contrast.md).

<img src="images\adaptivetoasts-buttonswithicons.png" width="364" alt="Toast that has buttons with icons"/>

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddButton(
        "Dismiss",
        NotificationActivationType.Foreground,
        "dismiss", new Uri("Assets/NotificationButtonIcons/Dismiss.png", UriKind.Relative));
```

#### [XML](#tab/xml)

```xml
<action
    content="Dismiss"
    imageUri="Assets/NotificationButtonIcons/Dismiss.png"
    arguments="dismiss"
    activationType="background"/>
```

---


### Buttons with pending update activation

**New in Fall Creators Update**: On background activation buttons, you can use an after activation behavior of **PendingUpdate** to create multi-step interactions in your notifications. When the user clicks your button, your background task is activated, and the notification gets placed in a "pending update" state, where it stays on screen till your background task replaces the notification with a new notification.

To learn how to implement this, see [Notification pending update](toast-pending-update.md).

![Notification with pending update](images/toast-pendingupdate.gif)


### Context menu actions

**New in Anniversary Update**: You can add additional context menu actions to the existing context menu that appears when the user right clicks your notification from within Action Center. Note that this menu only appears when right clicked from Action Center. It does not appear when right clicking a notification popup banner.

> [!NOTE]
> On older devices, these additional context menu actions will simply appear as normal buttons on your notification.

The additional context menu actions you add (such as "Change location") appear above the two default system entries.

<img alt="Toast with context menu" src="images/toast-contextmenu.png" width="444"/>

#### [Builder syntax](#tab/builder-syntax)

The builder syntax doesn't support context menu actions, so we recommend using initializer syntax.

```csharp
ToastContent content = new ToastContent()
{
    ...
 
    Actions = new ToastActionsCustom()
    {
        ContextMenuItems =
        {
            new ToastContextMenuItem("Change location", "action=changeLocation")
        }
    }
};
```

#### [XML](#tab/xml)

```xml
<toast>

    ...

    <actions>

        <action
            placement="contextMenu"
            content="Change location"
            arguments="action=changeLocation"/>

    </actions>

</toast>
```

---

> [!NOTE]
> Additional context menu items contribute to the total limit of 5 buttons on a notification.

Activation of additional context menu items is handled identical to notification buttons.


## Inputs

Inputs are specified within the Actions region of the notification region of the notification, meaning they are only visible when the notification is expanded.


### Quick reply text box

To enable a quick reply text box (for example, in a messaging app) add a text input and a button, and reference the ID of the text input field so that the button is displayed next to to the input field. The icon for the button should be a 32x32 pixel image with no padding, white pixels set to transparent, and 100% scale.

<img alt="notification with text input and actions" src="images/adaptivetoasts-xmlsample05.jpg" width="364"/>

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddInputTextBox("tbReply", "Type a reply")

    .AddButton(
        textBoxId: "tbReply", // To place button next to text box, reference text box's id
        content: "Reply",
        activationType: NotificationActivationType.Background,
        arguments: "action=reply&convId=9318",
        imageUri: new Uri("Assets/Reply.png", UriKind.Relative));
```

#### [XML](#tab/xml)

```xml
<toast launch="app-defined-string">

    ...

    <actions>

        <input id="textBox" type="text" placeHolderContent="Type a reply"/>

        <action
            content="Send"
            arguments="action=reply&amp;convId=9318"
            activationType="background"
            hint-inputId="textBox"
            imageUri="Assets/Reply.png"/>

    </actions>

</toast>
```

---


### Inputs with buttons bar

You also can have one (or many) inputs with normal buttons displayed below the inputs.

<img alt="notification with text and input actions" src="images/adaptivetoasts-xmlsample04.jpg" width="364"/>

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddInputTextBox("tbReply", "Type a reply")

    .AddButton("Reply", NotificationActivationType.Background, "action=reply&threadId=9218")
    .AddButton("Video call", NotificationActivationType.Foreground, "action=videocall&threadId=9218");
```

#### [XML](#tab/xml)

```xml
<toast launch="app-defined-string">

    ...

    <actions>

        <input id="textBox" type="text" placeHolderContent="Type a reply"/>

        <action
            content="Reply"
            arguments="action=reply&amp;threadId=9218"
            activationType="background"/>

        <action
            content="Video call"
            arguments="action=videocall&amp;threadId=9218"
            activationType="foreground"/>

    </actions>

</toast>
```

---


### Selection input

In addition to text boxes, you can also use a selection menu.

<img alt="notification with selection input and actions" src="images/adaptivetoasts-xmlsample06.jpg" width="364"/>

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddInput(new NotificationSelectionBox("time")
    {
        DefaultSelectionBoxItemId = "lunch",
        Items =
        {
            new NotificationSelectionBoxItem("breakfast", "Breakfast"),
            new NotificationSelectionBoxItem("lunch", "Lunch"),
            new NotificationSelectionBoxItem("dinner", "Dinner")
        }
    })

    .AddButton(...)
    .AddButton(...);
```

#### [XML](#tab/xml)

```xml
<toast launch="app-defined-string">

    ...

    <actions>

        <input id="time" type="selection" defaultInput="lunch">
            <selection id="breakfast" content="Breakfast" />
            <selection id="lunch" content="Lunch" />
            <selection id="dinner" content="Dinner" />
        </input>

        ...

    </actions>

</toast>
```

---


### Snooze/dismiss

Using a selection menu and two buttons, we can create a reminder notification that utilizes the system snooze and dismiss actions. Make sure to set the scenario to Reminder for the notification to behave like a reminder.

<img alt="reminder notification" src="images/adaptivetoasts-xmlsample07.jpg" width="364"/>

We link the Snooze button to the selection menu input using the **SelectionBoxId** property on the notification button.

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    .SetScenario(NotificationScenario.Reminder)
    
    ...
    
    .AddInput(new NotificationSelectionBox("snoozeTime")
    {
        DefaultSelectionBoxItemId = "15",
        Items =
        {
            new NotificationSelectionBoxItem("5", "5 minutes"),
            new NotificationSelectionBoxItem("15", "15 minutes"),
            new NotificationSelectionBoxItem("60", "1 hour"),
            new NotificationSelectionBoxItem("240", "4 hours"),
            new NotificationSelectionBoxItem("1440", "1 day")
        }
    })

    .AddButton(new NotificationButtonSnooze() { SelectionBoxId = "snoozeTime" })
    .AddButton(new NotificationButtonDismiss());
```

#### [XML](#tab/xml)

```xml
<toast scenario="reminder" launch="action=viewEvent&amp;eventId=1983">
   
  ...
 
  <actions>
     
    <input id="snoozeTime" type="selection" defaultInput="15">
      <selection id="1" content="1 minute"/>
      <selection id="15" content="15 minutes"/>
      <selection id="60" content="1 hour"/>
      <selection id="240" content="4 hours"/>
      <selection id="1440" content="1 day"/>
    </input>
 
    <action activationType="system" arguments="snooze" hint-inputId="snoozeTime" content="" />
 
    <action activationType="system" arguments="dismiss" content=""/>
     
  </actions>
   
</toast>
```

---

To use the system snooze and dismiss actions:

-   Specify a **NotificationButtonSnooze** or **NotificationButtonDismiss**
-   Optionally specify a custom content string:
    -   If you don't provide a string, we'll automatically use localized strings for "Snooze" and "Dismiss".
-   Optionally specify the **SelectionBoxId**:
    -   If you don't want the user to select a snooze interval and instead just want your notification to snooze only once for a system-defined time interval (that is consistent across the OS), then don't construct any &lt;input&gt; at all.
    -   If you want to provide snooze interval selections:
        -   Specify **SelectionBoxId** in the snooze action
        -   Match the id of the input with the **SelectionBoxId** of the snooze action
        -   Specify **NotificationSelectionBoxItem**'s value to be a nonNegativeInteger which represents snooze interval in minutes.



## Audio

Custom audio has always been supported by Mobile, and is supported in Desktop Version 1511 (build 10586) or newer. Custom audio can be referenced via the following paths:

-   ms-appx:///
-   ms-appdata:///

Alternatively, you can pick from the [list of ms-winsoundevents](/uwp/schemas/tiles/toastschema/element-audio#attributes-and-elements), which have always been supported on both platforms.

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    ...
    
    .AddAudio(new Uri("ms-appx:///Assets/NewMessage.mp3"));
```

#### [XML](#tab/xml)

```xml
<toast launch="app-defined-string">

    ...

    <audio src="ms-appx:///Assets/NewMessage.mp3"/>

</toast>
```

---

See the [audio schema page](/uwp/schemas/tiles/toastschema/element-audio) for information on audio in notifications. To learn how to send a notification using custom audio, see [custom audio on toasts](custom-audio-on-toasts.md).


## Alarms, reminders, and incoming calls

To create alarms, reminders, and incoming call notifications, you simply use a normal notification with a scenario value assigned to it. The scenario adusts a few behaviors to create a consistent and unified user experience.

> [!IMPORTANT]
> When using Reminder or Alarm, you must provide at least one button on your notification. Otherwise, the notification will be treated as a normal notification.

* **Reminder**: The notification will stay on screen until the user dismisses it or takes action. On Windows Mobile, the notification will also show pre-expanded. A reminder sound will be played.
* **Alarm**: In addition to the reminder behaviors, alarms will additionally loop audio with a default alarm sound.
* **IncomingCall**: Incoming call notifications are displayed full screen on Windows Mobile devices. Otherwise, they have the same behaviors as alarms except they use ringtone audio and their buttons are styled differently.

#### [Builder syntax](#tab/builder-syntax)

```csharp
new NotificationBuilder()
    .SetScenario(NotificationScenario.Reminder)
    ...
```

#### [XML](#tab/xml)

```xml
<toast scenario="reminder" launch="app-defined-string">

    ...

</toast>
```

---


## Localization and accessibility

Your tiles and toasts can load strings and images tailored for display language, display scale factor, high contrast, and other runtime contexts. For more info, see [Tile and notification support for language, scale, and high contrast](tile-toast-language-scale-contrast.md).


## Handling activation
To learn how to handle notification activations (the user clicking your notification or buttons on the notification), see [Send local notification](send-local-notification.md).
 
## Related topics

* [Send a local notification and handle activation](send-local-notification.md)
* [Notifications library on GitHub (part of the UWP Community Toolkit)](https://github.com/windows-toolkit/WindowsCommunityToolkit/tree/master/Microsoft.Toolkit.Uwp.Notifications)
* [Tile and notification support for language, scale, and high contrast](tile-toast-language-scale-contrast.md)
