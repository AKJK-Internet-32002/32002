---
Description: Learn how to migrate your Win32 apps from DesktopNotificationManagerCompat to ToastNotificationManagerCompat.
title: Migrate to ToastNotificationManagerCompat
label: Migrate to ToastNotificationManagerCompat
template: detail.hbs
ms.date: 08/28/2020
ms.topic: article
keywords: windows 10, win32, migrate, desktopnotificationmanagercompat, toastnotificationmanagercompat, toolkit
ms.localizationpriority: medium
---

# Migrate to ToastNotificationManagerCompat

If you're currently using the **DesktopNotificationManagerCompat** library within your Win32 app, follow along in this doc to learn how to migrate to the new **ToastNotificationManagerCompat**!

## What's changed

**The Start menu shortcut is no longer required to send toasts!** That means you no longer need a WiX (or other) installer, you can just use toast notifications without any additional configuration!

**You also no longer need to specify a COM GUID and class!** You just simply subscribe to the OnActiv

## How to migrate

### Step 1: Delete the DesktopNotificationManagerCompat.cs class

If you locally copied the compat library code to your project, delete that file (if you were using the NuGet package, skip this step).

### Step 2: Update or install the NuGet package

Ensure you have the latest version of the [Microsoft.Toolkit.Uwp.Notifications](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) NuGet package. This package provides the new (and deprecated old) Compat classes.

> [!IMPORTANT]
> .NET Framework Win32 apps that still use packages.config must migrate to PackageReference, otherwise the Windows 10 SDKs won't be referenced correctly. In your project, right-click on "References", and click "Migrate packages.config to PackageReference".

### Step 3: Replace all calls to CreateToastNotifier or History

Replace any of the following lines...

```csharp
DesktopNotificationManagerCompat.CreateToastNotifier()

DesktopNotificationManagerCompat.History

DesktopNotificationManagerCompat.CanUseHttpImages
```

With the following...

```csharp
ToastNotificationManagerCompat.CreateToastNotifier()

ToastNotificationManagerCompat.History

ToastNotificationManagerCompat.CanUseHttpImages
```

### Step 4: Replace RegisterActivator and RegisterAumidAndComServer

Replace the following lines of code (should be in your startup code)...

```csharp
// Register AUMID, COM server, and activator
DesktopNotificationManagerCompat.RegisterAumidAndComServer<MyNotificationActivator>("MyAumid");
DesktopNotificationManagerCompat.RegisterActivator<MyNotificationActivator>();
```

With the following...

```csharp
// Listen to notification activation
ToastNotificationManagerCompat.OnActivated += (toastArgs) =>
{
    // TODO: Code from MyNotificationActivator goes here
};
```

### Step 5: Move your activation code into OnActivated

Locate your class that extends the **NotificationActivator** (probably called **MyNotificationActivator**), copy your code that's within **OnActivated** and move that code into the new **OnActivated** event handler. Then, delete your NotificationActivator.

```csharp
// Listen to notification activation
ToastNotificationManagerCompat.OnActivated += (toastArgs) =>
{
    Application.Current.Dispatcher.Invoke(delegate
    {
        if (toastArgs.Argument == "reply")
        {
            //...
        }
    });
};
```

### Step 6: Use WasCurrentProcessToastActivated

Replace the following lines of code...

```csharp
if (e.Args.Contains(DesktopNotificationManagerCompat.ToastActivatedLaunchArg))
```

With...

```csharp
if (ToastNotificationManagerCompat.WasCurrentProcessToastActivated())
```

### Step 7: Modifying display name and icon

Your app's display name is now retrieved from the process itself instead of a shortcut. Set the AssemblyTitle attribute to customize the app's display name (this is also the name task manager uses to display your app).

Your icon comes from the process itself too. In Visual Studio, you can edit your project's properties and specify a custom .ico file.