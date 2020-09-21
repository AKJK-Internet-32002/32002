---
Description: Comparison of Reunion vs platform API
title: Reunion comparison
label: Reunion comparison
template: detail.hbs
ms.date: 04/09/2020
ms.topic: article
keywords: windows 10, uwp, scheduled toast notification, scheduledtoastnotification, how to, quickstart, getting started, code sample, walkthrough
ms.localizationpriority: medium
---

# Reunion comparison

This is a comparison of the APIs in the platform today, and the proposed Reunion APIs.

## Notification APIs

### Send a notification

#### [Platform](#tab/platform)

```csharp
const string xml = @"<toast>
    <visual>
        <binding template=""ToastGeneric"">
            <text></text>
        </binding>
    <visual>
</toast>";

XmlDocument doc = new XmlDocument();
doc.LoadXml(xml);

// Have to use XmlDocument APIs to inject any text from users since it must be properly XML escaped
(doc.SelectSingleNode("//text") as XmlElement).InnerText = "Hello world";

ToastNotificationManager.CreateToastNotifier().Show(new ToastNotification(doc));
```

#### [Reunion](#tab/reunion)

```csharp
new NotificationBuilder()
    .AddText("Hello world")
    .Show();
```

---

### Send a notification with all properties

#### [Platform](#tab/platform)

```csharp
// Simplifying this example by using constant XML
const string xml = @"<toast>
    <visual>
        <binding template=""ToastGeneric"">
            <text>Hello world</text>
        </binding>
    <visual>
</toast>";

XmlDocument doc = new XmlDocument();
doc.LoadXml(xml);

ToastNotificationManager.CreateToastNotifier().Show(new ToastNotification(doc)
{
    Tag = "tag",
    Group = "group",
    ExpirationTime = DateTime.Now.AddMinutes(3),
    ExpiresOnReboot = true,
    Priority = ToastNotificationPriority.High,
    SuppressPopup = true
});
```

#### [Reunion](#tab/reunion)

```csharp
new NotificationBuilder()
    .AddText("Hello world")
    .SetTag("tag")
    .SetGroup("group")
    .SetExpirationTime(DateTime.Now.AddMinutes(3))
    .SetExpiresOnReboot(true)
    .SetPriority(NotificationPriority.High)
    .SetSuppressPopup(true)
    .Show();
```

---

### Clear notifications

#### [Platform](#tab/platform)

```csharp
// Remove specific notification by tag
ToastNotificationManager.History.Remove("tag");

// Remove specific notification by composite ID of both tag and group
ToastNotificationManager.History.Remove("tag", "group");

// Remove all notifications under group
ToastNotificationManager.History.RemoveGroup("tag", "group");

// Clear all notifications
ToastNotificationManager.History.Clear();

// Get all current notifications
IReadOnlyList<ToastNotification> notifs = ToastNotificationManager.History.GetHistory();
```

#### [Reunion](#tab/reunion)

```csharp
// Remove specific notification by tag
NotificationManager.Remove("tag");

// Remove specific notification by composite ID of both tag and group
NotificationManager.Remove("tag", "group");

// Remove all notifications under group
NotificationManager.RemoveGroup("tag", "group");

// Clear all notifications
NotificationManager.Clear();

// Get all current notifications
IReadOnlyList<Notification> notifs = NotificationManager.GetNotifications();
```

---


### Schedule a notification

#### [Platform](#tab/platform)

```csharp
// Simplifying this example by using constant XML
const string xml = @"<toast>
    <visual>
        <binding template=""ToastGeneric"">
            <text>Hello world</text>
        </binding>
    <visual>
</toast>";

XmlDocument doc = new XmlDocument();
doc.LoadXml(xml);

var scheduledNotif = new ScheduledToastNotification(DateTime.Now.AddSeconds(5), doc);

ToastNotificationManager.CreateToastNotifier().AddToSchedule(scheduledNotif);
```

#### [Reunion](#tab/reunion)

```csharp
new NotificationBuilder()
    .AddText("Hello world")
    .Schedule(DateTime.Now.AddSeconds(5));
```

---


## Badges

### Setting and clearing badges

#### [Platform](#tab/platform)

```csharp
// Set badge to 5
var doc = BadgeUpdateManager.GetTemplateContent(BadgeTemplateType.BadgeNumber);
doc.DocumentElement.SetAttribute("value", 5.ToString());

BadgeUpdateManager.CreateBadgeUpdaterForApplication().Update(new BadgeNotification(doc));

// Set badge to alarm glyph
var doc = BadgeUpdateManager.GetTemplateContent(BadgeTemplateType.BadgeGlyph);
doc.DocumentElement.SetAttribute("value", "alarm");

BadgeUpdateManager.CreateBadgeUpdaterForApplication().Update(new BadgeNotification(doc));

// Clear badge
BadgeUpdateManager.CreateBadgeUpdaterForApplication().Clear();
```

#### [Reunion](#tab/reunion)

```csharp
// Set badge to 5
BadgeManager.Set(5);

// Set badge to alarm glyph
BadgeManager.Set(BadgeValue.Alarm);

// Clear badge
BadgeManager.Clear();
```

---



### Periodic badge updates

#### [Platform](#tab/platform)

```csharp
Uri uri = new Uri("https://myapp.com/badgevalues");

// Starting updates
BadgeUpdateManager.CreateBadgeUpdaterForApplication().StartPeriodicUpdate(uri, PeriodicUpdateRecurrence.Daily);

// Stopping updates
BadgeUpdateManager.CreateBadgeUpdaterForApplication().StopPeriodicUpdate();
```

#### [Reunion](#tab/reunion)

```csharp
// Starting updates
BadgeManager.StartPeriodicUpdate(uri, PeriodicUpdateInterval.Daily);

// Stopping updates
BadgeManager.StopPeriodicUpdate();
```

---