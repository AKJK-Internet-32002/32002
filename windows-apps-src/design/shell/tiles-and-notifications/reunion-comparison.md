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

## Quick reference

A quick reference of how existing platform APIs map to new Reunion APIs.

Platform API | Reunion API
--|--
AdaptiveNotificationContentKind (enum) | ??
AdaptiveNotificationText (class) | ??
BadgeNotification (class) | BadgeManager.Set(value)
BadgeTemplateType (enum) | N/A
BadgeUpdateManager.CreateBadgeUpdaterForApplication() | BadgeManager.Set(value), etc
BadgeUpdateManager.CreateBadgeUpdaterForApplication(string appId) | Not supported (only MAPs)
BadgeUpdateManager.GetForUser(User user) | BadgeManager.Set(user, value), etc
BadgeUpdateManager.GetTemplateContent | N/A
BadgeUpdateManager.CreateBadgeUpdaterForSecondaryTile(string tileId) | BadgeManager.Set("tileId", value), etc
BadgeUpdateManagerForUser.CreateBadgeUpdaterForSecondaryTile(string tileId) | BadgeManager.Set(user, "tileId", value), etc


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

#### [Toolkit 6.1](#tab/toolkit-6-1)

```csharp
var content = new ToastContentBuilder()
    .AddText("Hello world")
    .GetToastContent();

var notif = new ToastNotification(content.GetXml());

ToastNotificationManager.CreateToastNotifier().Show(notif);
```

#### [Toolkit 7](#tab/toolkit-7)

```csharp
new ToastContentBuilder()
    .AddText("Hello world")
    .Show();
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

#### [Toolkit 6.1](#tab/toolkit-6-1)

```csharp
var content = new ToastContentBuilder()
    .AddText("Hello world")
    .GetToastContent();

var notif = new ToastNotification(content.GetXml())
{
    Tag = "tag",
    Group = "group",
    ExpirationTime = DateTime.Now.AddMinutes(3),
    ExpiresOnReboot = true,
    Priority = ToastNotificationPriority.High,
    SuppressPopup = true
};

ToastNotificationManager.CreateToastNotifier().Show(notif);
```

#### [Toolkit 7](#tab/toolkit-7)

```csharp
new ToastContentBuilder()
    .AddText("Hello world")
    .Show(toast =>
    {
        toast.Tag = "tag";
        toast.Group = "group";
        toast.ExpirationTime = DateTime.Now.AddMinutes(3);
        toast.ExpiresOnReboot = true;
        toast.Priority = ToastNotificationPriority.High;
        toast.SuppressPopup = true;
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

#### [Toolkit 7](#tab/toolkit-7)

```csharp
// Remove specific notification by tag
ToastNotificationManagerCompat.History.Remove("tag");

// Remove specific notification by composite ID of both tag and group
ToastNotificationManagerCompat.History.Remove("tag", "group");

// Remove all notifications under group
ToastNotificationManagerCompat.History.RemoveGroup("tag", "group");

// Clear all notifications
ToastNotificationManagerCompat.History.Clear();

// Get all current notifications
IReadOnlyList<ToastNotification> notifs = ToastNotificationManagerCompat.History.GetHistory();
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

#### [Toolkit 6.1](#tab/toolkit-6-1)

```csharp
var content = new ToastContentBuilder()
    .AddText("Hello world")
    .GetToastContent();

var scheduledNotif = new ScheduledToastNotification(DateTime.Now.AddSeconds(5), content.GetXml());

ToastNotificationManager.CreateToastNotifier().AddToSchedule(notif);
```

#### [Toolkit 7](#tab/toolkit-7)

```csharp
new ToastContentBuilder()
    .AddText("Hello world")
    .Schedule(DateTime.Now.AddSeconds(5));
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


## Primary tile updates

### Update the primary tile

#### [Platform](#tab/platform)

```csharp
const string xml = @"<tile>
    <visual>
        <binding template=""TileMedium"">
            <text></text>
        </binding>
    </visual>
</tile>";

XmlDocument doc = new XmlDocument();
doc.LoadXml(xml);

// Have to use XmlDocument APIs to inject any text from users since it must be properly XML escaped
(doc.SelectSingleNode("//text") as XmlElement).InnerText = "Hello world";

TileUpdateManager.CreateTileUpdaterForApplication().Update(new TileNotification(doc));
```

#### [Reunion](#tab/reunion)

```csharp
new TileContentBuilder()
    .AddText("Hello world")
    .Update();
```

---


### Clear the tile

#### [Platform](#tab/platform)

```csharp
TileUpdateManager.CreateTileUpdaterForApplication().Clear();
```

#### [Reunion](#tab/reunion)

```csharp
TileManager.ClearContent();
```

---


### Enable content queues

#### [Platform](#tab/platform)

```csharp
// Enable for all sizes
TileUpdateManager.CreateTileUpdaterForApplication().EnableNotificationQueue(true);

// Enable for medium
TileUpdateManager.CreateTileUpdaterForApplication().EnableNotificationQueueForSquare150x150(true);

// Enable for large
TileUpdateManager.CreateTileUpdaterForApplication().EnableNotificationQueueForSquare310x310(true);

// Enable for wide
TileUpdateManager.CreateTileUpdaterForApplication().EnableNotificationQueueForWide310x150(true);
```

#### [Reunion](#tab/reunion)

```csharp
/// Enable for all sizes
TileManager.SetContentQueueEnabled(true);

// Enable for medium
TileManager.SetContentQueueEnabledForMediumSize(true);

// Enable for large
TileManager.SetContentQueueEnabledForLargeSize(true);

// Enable for wide
TileManager.SetContentQueueEnabledForWideSize(true);
```

---


## Secondary tile updates

### Update the secondary tile

#### [Platform](#tab/platform)

```csharp
const string xml = @"<tile>
    <visual>
        <binding template=""TileMedium"">
            <text></text>
        </binding>
    </visual>
</tile>";

XmlDocument doc = new XmlDocument();
doc.LoadXml(xml);

// Have to use XmlDocument APIs to inject any text from users since it must be properly XML escaped
(doc.SelectSingleNode("//text") as XmlElement).InnerText = "Hello world";

TileUpdateManager.CreateTileUpdaterForSecondaryTile("tileId").Update(new TileNotification(doc));
```

#### [Reunion](#tab/reunion)

```csharp
new TileContentBuilder()
    .AddText("Hello world")
    .Update("tileId");
```

---


### Enable content queues

#### [Platform](#tab/platform)

```csharp
// Enable for all sizes
TileUpdateManager.CreateTileUpdaterForSecondaryTile("tileId").EnableNotificationQueue(true);

// Enable for medium
TileUpdateManager.CreateTileUpdaterForSecondaryTile("tileId").EnableNotificationQueueForSquare150x150(true);

// Enable for large
TileUpdateManager.CreateTileUpdaterForSecondaryTile("tileId").EnableNotificationQueueForSquare310x310(true);

// Enable for wide
TileUpdateManager.CreateTileUpdaterForSecondaryTile("tileId").EnableNotificationQueueForWide310x150(true);
```

#### [Reunion](#tab/reunion)

```csharp
/// Enable for all sizes
TileManager.SetQueueEnabled("tileId", true);

// Enable for medium
TileManager.SetQueueEnabledForMediumSize("tileId", true);

// Enable for large
TileManager.SetQueueEnabledForLargeSize("tileId", true);

// Enable for wide
TileManager.SetQueueEnabledForWideSize("tileId", true);
```

---



## Secondary tiles

### Create a secondary tile

#### [Platform](#tab/platform)

```csharp
SecondaryTile tile = new SecondaryTile(
    "tileId",
    "Display name",
    "args",
    new Uri("ms-appx:///TileLogo.png"),
    TileSize.Default);

await tile.RequestCreateAsync();
```

#### [Reunion](#tab/reunion)

```csharp
// TODO: Need different name for "SecondaryTile" otherwise class name collisions
SecondaryTile tile = new SecondaryTile(
    "tileId",
    "Display name",
    "args",
    new Uri("ms-appx:///TileLogo.png"),
    TileSize.Default);

await TileManager.PinAsync(tile);
```

---

## ForUser and secondary tiles

Technically the heirarcy is like this...

* User (1-many)
    * AppListEntry (1-many)
        * Primary notification collection
            * Notifications
        * Secondary notification collections
            * Notifications
        * Primary tile
            * Badge
            * Tile content
        * Secondary tiles
            * Badge
            * Tile content

99% of apps aren't MUA-enabled (multi-user apps) nor MAP (multi-app packages). Thus, their world view is simply

* Notifications
* Notification collections
    * Notifications
* Primary tile
    * Badge
    * Tile content
* Secondary tiles
    * Badge
    * Tile content


Many apps also don't care about secondary tiles or notification collections, so their world view is simply

* Notifications
* Badge
* Tile content


Should we design for this case, yet make the other cases possible?


#### [Simplified](#tab/simplified)

```csharp
// Clearing notifications is a simple one-line
NotificationManager.Clear();

// However, if you use collections and/or multi users, clearing is a bit different... and we possibly have a bunch of overloads
NotificationManager.Clear(user, "collectionId");
NotificationManager.Clear("collectionId");

// And note that you then can't have generic code perform operations on either the primary or a secondary collection (see other tab for example)
```

#### [Expressive](#tab/expressive)

```csharp
// Clearing notifications is a bit lengthier (first have to get per-user manager, then get the collection manager)
NotificationManager.GetDefault().PrimaryCollection.Clear();

// However, if you use collections and/or multi users, APIs are more intuitive
NotificationManager.GetForUser(user).GetCollection("collectionId").Clear();

// Additionally, you can have methods that can generically act on either the primary or secondary collection
RemoveObsoleteNotifs(NotificationManager.GetDefault().PrimaryCollection);
RemoveObsoleteNotifs(NotificationManager.GetForUser(user).GetCollection("collectionId"));

private void RemoveObsoleteNotifs(NotificationCollection collection)
{
    var notifs = collection.GetNotifications();
    foreach (var n in notifs)
    {
        if (IsObsolete(n))
        {
            collection.Remove(n);
        }
    }
}
```

---