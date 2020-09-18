---
Description: Learn how to use tiles, badges, toasts, and notifications to provide entry points into your app and keep users up-to-date.
title: Badge notifications for Windows apps
ms.assetid: 48ee4328-7999-40c2-9354-7ea7d488c538
label: Tiles, badges, and notifications
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
---
# Badges

 

<div style="float:left; font-size:80%; text-align:left; margin: 0px 15px 15px 0px;">
<img src="images/badge-example.png" alt="A tile with a numeric badge displaying the number 63 to indicate 63 unread mails." style="padding-bottom:0.0em; margin-bottom: 2px" />

A badge conveys summary or status information specific to your app. They can be numeric (1-99) or one of a set of system-provided glyphs. Examples of information best conveyed through a badge include network connection status in an online game, user status in a messaging app, number of unread mails in a mail app, and number of new posts in a social media app. 

Badges appear on your app's taskbar icon and in the lower-right corner of its start tile, regardless of whether the app is running. Badges can be displayed on all tile sizes.  

> [!NOTE]
> You cannot provide your own badge image; only system-provided badge images can be used.


## Numeric badges

<table>
    <tr>
        <th>Value</th>
        <th>Badge</th>
        <th>XML</th>
    </tr>
    <tr>
        <td>A number from 1 to 99. A value of 0 is equivalent to the glyph value "none" and will clear the badge.</td>
        <td><img src="images/badges/badge-numeric.png" alt="A numeric badge less than 100." /></td>
        <td>`<badge value="1"/>`</td>
    </tr>
    <tr>
        <td>Any number greater than 99.</td>
        <td><img src="images/badges/badge-numeric-greater.png" alt="A numeric badge greater than 99." /></td></td>
        <td>`<badge value="100"/>`</td>
    </tr>    
</table>

## Glyph badges
Instead of a number, a badge can display one of a non-extensible set of status glyphs. 

<table>
<tr>
    <th>Status</th>
    <th>Glyph</th>
    <th>XML</th>
</tr>
<tr>
    <td>none</td>
    <td>(No badge shown.)</td>
    <td>`<badge value="none"/>`</td>
</tr>
<tr>
    <td>activity</td>
    <td><img src="images/badges/badge-activity.png" alt="Glyph" /></td>
    <td>`<badge value="activity"/>`</td>
</tr>
<tr>
    <td>alarm</td>
    <td><img src="images/badges/badge-alarm.png" alt="Glyph" /></td>
    <td>`<badge value="alarm"/>`</td>
</tr>
<tr>
    <td>alert</td>
    <td><img src="images/badges/badge-alert.png" alt="Glyph" /></td>
    <td>`<badge value="alert"/>`</td>
</tr>
<tr>
    <td>attention</td>
    <td><img src="images/badges/badge-attention.png" alt="Glyph" /></td>
    <td>`<badge value="attention"/>`</td>
</tr>
<tr>
    <td>available</td>
    <td><img src="images/badges/badge-available.png" alt="Glyph" /></td>
    <td>`<badge value="available"/>`</td>
</tr>
<tr>
    <td>away</td>
    <td><img src="images/badges/badge-away.png" alt="Glyph" /></td>
    <td>`<badge value="away"/>`</td>
</tr>
<tr>
    <td>busy</td>
    <td><img src="images/badges/badge-busy.png" alt="Glyph" /></td>
    <td>`<badge value="busy"/>`</td>
</tr>
<tr>
    <td>error</td>
    <td><img src="images/badges/badge-error.png" alt="Glyph" /></td>
    <td>`<badge value="error"/>`</td>
</tr>
<tr>
    <td>newMessage</td>
    <td><img src="images/badges/badge-newMessage.png" alt="Glyph" /></td>
    <td>`<badge value="newMessage"/>`</td>
</tr>
<tr>
    <td>paused</td>
    <td><img src="images/badges/badge-paused.png" alt="Glyph" /></td>
    <td>`<badge value="paused"/>`</td>
</tr>
<tr>
    <td>playing</td>
    <td><img src="images/badges/badge-playing.png" alt="Glyph" /></td>
    <td>`<badge value="playing"/>`</td>
</tr>
<tr>
    <td>unavailable</td>
    <td><img src="images/badges/badge-unavailable.png" alt="Glyph" /></td>
    <td>`<badge value="unavailable"/>`</td>
</tr>
</table>

## Set a badge

These examples show you how to set a badge.

### Set a numeric badge

````csharp
BadgeManager.Set(5);
````

### Set a glyph badge

````csharp
BadgeManager.Set(BadgeValue.Alert);
````

### Clear the badge

````csharp
BadgeManager.Clear();
````

## Get the sample code

* [Notifications sample](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Notifications)<br/> Shows how to create live tiles, send badge updates, and display toast notifications. 

## Related articles

* [Adaptive and interactive toast notifications](adaptive-interactive-toasts.md)
* [Create tiles](creating-tiles.md)
* [Create adaptive tiles](create-adaptive-tiles.md)
