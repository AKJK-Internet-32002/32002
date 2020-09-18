---
Description: Learn how to send a local toast notification and handle the user clicking the toast from C++ apps.
title: Send a local toast notification from C++ apps
ms.assetid: E9AB7156-A29E-4ED7-B286-DA4A6E683638
label: Send a local toast notification from C++ apps
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp, send toast notifications, notifications, send notifications, toast notifications, how to, quickstart, getting started, code sample, walkthrough, c++
ms.localizationpriority: medium
---
# Send a local toast notification from C++ apps


A toast notification is a message that an app can construct and deliver to the user while they are not currently inside your app. This Quickstart walks you through the steps to create, deliver, and display a Windows 10 toast notification with the new adaptive templates and interactive actions. These actions are demonstrated through a local notification, which is the simplest notification to implement.

> **Important APIs**: [ToastNotification Class](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotification), [ToastNotificationActivatedEventArgs Class](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ToastNotificationActivatedEventArgs)


## Step 1: Enable the Windows 10 SDK

#### [UWP C++/CX](#tab/uwp)

UWP apps already have the Windows 10 SDK referenced. You don't need to do anything!

#### [Win32 WRL MSIX/sparse](#tab/win32-wrl-msix-sparse)

If you haven't enabled the Windows 10 SDK for your Win32 app, you must do that first. There are a few key steps...

1. Add `runtimeobject.lib` to **Additional Dependencies**
2. Target the Windows 10 SDK

Right click your project and select **Properties**.

In the top **Configuration** menu, select **All Configurations** so that the following change is applied to both Debug and Release.

Under **Linker -> Input**, add `runtimeobject.lib` to the **Additional Dependencies**.

Then under **General**, make sure that the **Windows SDK Version** is set to something 10.0 or higher (not Windows 8.1).

#### [Win32 WRL](#tab/win32-wrl)

If you haven't enabled the Windows 10 SDK for your Win32 app, you must do that first. There are a few key steps...

1. Add `runtimeobject.lib` to **Additional Dependencies**
2. Target the Windows 10 SDK

Right click your project and select **Properties**.

In the top **Configuration** menu, select **All Configurations** so that the following change is applied to both Debug and Release.

Under **Linker -> Input**, add `runtimeobject.lib` to the **Additional Dependencies**.

Then under **General**, make sure that the **Windows SDK Version** is set to something 10.0 or higher (not Windows 8.1).

---


## Step 2: Copy compat library code

Copy the **[DesktopNotificationManagerCompat.h](https://raw.githubusercontent.com/WindowsNotifications/desktop-toasts/aleader/converged/CPP-WRL/DesktopToastsCppWrlApp/DesktopNotificationManagerCompat.h)** and **[DesktopNotificationManagerCompat.cpp](https://raw.githubusercontent.com/WindowsNotifications/desktop-toasts/aleader/converged/CPP-WRL/DesktopToastsCppWrlApp/DesktopNotificationManagerCompat.cpp)** file from GitHub into your project. The compat library abstracts much of the complexity of desktop notifications. The following instructions require the compat library.

If you're using precompiled headers, make sure to `#include "stdafx.h"` as the first line of the DesktopNotificationManagerCompat.cpp file.



## Step 3: Include the header files and namespaces

Include the compat library header file, and the header files and namespaces related to using the Windows toast APIs.

#### [UWP C++/CX](#tab/uwp)

```cpp
using namespace Windows::Data::Xml::Dom;
using namespace Windows::UI::Notifications;
```

#### [Win32 WRL MSIX/sparse](#tab/win32-wrl-msix-sparse)

```cpp
#include "DesktopNotificationManagerCompat.h"
#include <NotificationActivationCallback.h>
#include <windows.ui.notifications.h>

using namespace ABI::Windows::Data::Xml::Dom;
using namespace ABI::Windows::UI::Notifications;
using namespace Microsoft::WRL;
```

#### [Win32 WRL](#tab/win32-wrl)

```cpp
#include "DesktopNotificationManagerCompat.h"
#include <NotificationActivationCallback.h>
#include <windows.ui.notifications.h>

using namespace ABI::Windows::Data::Xml::Dom;
using namespace ABI::Windows::UI::Notifications;
using namespace Microsoft::WRL;
```

---



## Step 3: Register with notification platform

#### [UWP C++/CX](#tab/uwp)

UWP apps are automatically registered with the notification platform as part of app deployment. You don't need to do anything!

#### [Win32 WRL MSIX/sparse](#tab/win32-wrl-msix-sparse)

Win32 apps packaged using MSIX or using sparse-signed packages are automatically registered with the notification platform as part of app deployment. You don't need to do anything!

#### [Win32 WRL](#tab/win32-wrl)

Win32 apps not using MSIX/sparse must register their Application User Model ID (AUMID), display name, and icon programmatically.

Pick a unique AUMID that will identify your Win32 app. This is typically in the form of [CompanyName].[AppName], but you want to ensure this is unique across all apps.

Add the following code to your app's initialization code (for WPF apps, you can override OnStartup within App.xaml.cs).

```cpp
// Register application (for Desktop Bridge apps, this no-ops)
DesktopNotificationManagerCompat::RegisterApplication(
    L"WindowsNotifications.DesktopToastsCpp",
    L"C++ toasts",
    L"C:\\icon.png");
```

If you support both MSIX/sparse and Win32, feel free to call this method regardless. If you're running in a MSIX/sparse package, this method will simply no-op. There's no need to fork your code.

---


## Step 4: Send a toast

In Windows 10, your toast notification content is described using an adaptive language that allows great flexibility with how your notification looks. See the [toast content documentation](adaptive-interactive-toasts.md) for more information.

We'll start with a simple text-based notification. Construct the toast content, create a toast notification, and send the toast.

<img alt="Simple text toast notification" src="images/send-toast-01.png" width="364"/>

#### [UWP C++/CX](#tab/uwp)

```cpp
// Create the content
XmlDocument^ doc = ref new XmlDocument();
doc->LoadXml(LR"(<toast>
<visual>
    <binding template="ToastGeneric">
        <text>Andrew sent you a picture</text>
        <text>Check this out, Happy Canyon in Utah!</text>
    </binding>
</visual>
</toast>)");

// Create the notification
ToastNotification^ notification = ref new ToastNotification(doc);

// And send it
ToastNotificationManager::CreateToastNotifier()->Show(notification);
```

#### [Win32 WRL MSIX/sparse](#tab/win32-wrl-msix-sparse)

```cpp
// Create the content
ComPtr<IXmlDocument> doc;
RETURN_IF_FAILED(ToastNotificationManagerCompat::CreateXmlDocumentFromString(LR"(<toast>
<visual>
    <binding template="ToastGeneric">
        <text>Andrew sent you a picture</text>
        <text>Check this out, Happy Canyon in Utah!</text>
    </binding>
</visual>
</toast>)", &doc));

// Create the notifier
ComPtr<IToastNotifier> notifier;
RETURN_IF_FAILED(ToastNotificationManagerCompat::CreateToastNotifier(&notifier));

// And create the notification itself
ComPtr<IToastNotification> notification;
RETURN_IF_FAILED(ToastNotificationManagerCompat::CreateToastNotification(doc.Get(), &notification));

// And show it!
return notifier->Show(notification.Get());
```

#### [Win32 WRL](#tab/win32-wrl)

```cpp
// Create the content
ComPtr<IXmlDocument> doc;
RETURN_IF_FAILED(ToastNotificationManagerCompat::CreateXmlDocumentFromString(LR"(<toast>
<visual>
    <binding template="ToastGeneric">
        <text>Andrew sent you a picture</text>
        <text>Check this out, Happy Canyon in Utah!</text>
    </binding>
</visual>
</toast>)", &doc));

// Create the notifier
ComPtr<IToastNotifier> notifier;
RETURN_IF_FAILED(ToastNotificationManagerCompat::CreateToastNotifier(&notifier));

// And create the notification itself
ComPtr<IToastNotification> notification;
RETURN_IF_FAILED(ToastNotificationManagerCompat::CreateToastNotification(doc.Get(), &notification));

// And show it!
return notifier->Show(notification.Get());
```

Note that Win32 non-MSIX/sparse apps must use the **ToastNotificationManagerCompat** class as seen above. If they use **ToastNotificationManager** itself, they will receive an element not found exception.

---


## Step 5: Handling activation

The first step in making your toasts clickable is to add some activation info to your toast, so that your app can know what to launch when the user clicks it (in this case, we're including some information that later tells us we should open a conversation, and we know which specific conversation to open).

Edit your toast XML and add the **launch** attribute, as seen below.

```xml
<toast launch="action=viewConversation&amp;conversationId=384928">
    <visual>
        <binding template="ToastGeneric">
            <text>Andrew sent you a picture</text>
            <text>Check this out, Happy Canyon in Utah!</text>
        </binding>
    </visual>
</toast>
```


Then, the steps for handling activation differ for UWP, Win32 MSIX/sparse, and Win32 apps.


#### [UWP C++/CX](#tab/uwp)

When the user clicks your toast (or a button on the toast with foreground activation), your app's **App.xaml.cpp** **OnActivated** will be invoked.

In the example you see below, you can retrieve the arguments string you initially provided in the toast content. You can also retrieve the input the user provided in your text boxes and selection boxes.

> [!IMPORTANT]
> You must initialize your frame and activate your window just like your **OnLaunched** code. **OnLaunched is NOT called if the user clicks on your toast**, even if your app was closed and is launching for the first time. We often recommend combining **OnLaunched** and **OnActivated** into your own `OnLaunchedOrActivated` method since the same initialization needs to occur in both.

**App.xaml.cpp**

```csharp
void App::OnActivated(Windows::ApplicationModel::Activation::IActivatedEventArgs^ e)
{
    // Get the root frame
    auto rootFrame = dynamic_cast<Frame^>(Window::Current->Content);
 
    // TODO: Initialize root frame just like in OnLaunched
 
    // Handle toast activation
    if (e is ToastNotificationActivatedEventArgs toastActivationArgs)
    {            
        // Parse the query string (using QueryString.NET)
        QueryString args = QueryString.Parse(toastActivationArgs.Argument);
 
        // See what action is being requested 
        switch (args["action"])
        {
            // Open the image
            case "viewImage":
 
                // The URL retrieved from the toast args
                string imageUrl = args["imageUrl"];
 
                // If we're already viewing that image, do nothing
                if (rootFrame.Content is ImagePage && (rootFrame.Content as ImagePage).ImageUrl.Equals(imageUrl))
                    break;
 
                // Otherwise navigate to view it
                rootFrame.Navigate(typeof(ImagePage), imageUrl);
                break;
                             
 
            // Open the conversation
            case "viewConversation":
 
                // The conversation ID retrieved from the toast args
                int conversationId = int.Parse(args["conversationId"]);
 
                // If we're already viewing that conversation, do nothing
                if (rootFrame.Content is ConversationPage && (rootFrame.Content as ConversationPage).ConversationId == conversationId)
                    break;
 
                // Otherwise navigate to view it
                rootFrame.Navigate(typeof(ConversationPage), conversationId);
                break;
        }
 
        // If we're loading the app for the first time, place the main page on
        // the back stack so that user can go back after they've been
        // navigated to the specific page
        if (rootFrame.BackStack.Count == 0)
            rootFrame.BackStack.Add(new PageStackEntry(typeof(MainPage), null, null));
    }
 
    // TODO: Handle other types of activation
 
    // Ensure the current window is active
    Window.Current.Activate();
}
```

#### [Win32 WRL MSIX/sparse](#tab/win32-wrl-msix-sparse)

When the user clicks your toast (or a button on the toast with foreground activation), your app's **NotificationActivator** will be invoked. You must extend this class for toast activation, so that when the user clicks on your toast, your app can do something. This lets you handle activation when your toast is clicked days later while your app is closed. This class can be placed anywhere in your project.

Implement the **INotificationActivationCallback** interface as seen below, including a UUID, and also call **CoCreatableClass** to flag your class as COM creatable. For your UUID, create a unique GUID using one of the many online GUID generators. This GUID CLSID (class identifier) is how Action Center knows what class to COM activate.

```cpp
// The UUID CLSID must be unique to your app. Create a new GUID if copying this code.
class DECLSPEC_UUID("replaced-with-your-guid-C173E6ADF0C3") NotificationActivator WrlSealed WrlFinal
    : public RuntimeClass<RuntimeClassFlags<ClassicCom>, INotificationActivationCallback>
{
public:
    virtual HRESULT STDMETHODCALLTYPE Activate(
        _In_ LPCWSTR appUserModelId,
        _In_ LPCWSTR invokedArgs,
        _In_reads_(dataCount) const NOTIFICATION_USER_INPUT_DATA* data,
        ULONG dataCount) override
    {
        // TODO: Handle activation
    }
};

// Flag class as COM creatable
CoCreatableClass(NotificationActivator);
```

Then, in your **Package.appxmanifest**, add:

1. Declaration for **xmlns:com**
2. Declaration for **xmlns:desktop**
3. In the **IgnorableNamespaces** attribute, **com** and **desktop**
4. **com:Extension** for the COM activator using the GUID from your NotificationActivator above. Be sure to include the `Arguments="-ToastActivated"` so that you know your launch was from a toast
5. **desktop:Extension** for **windows.toastNotificationActivation** to declare your toast activator CLSID (the GUID from your NotificationActivator above).

**Package.appxmanifest**

```xml
<!--Add these namespaces-->
<Package
  ...
  xmlns:com="http://schemas.microsoft.com/appx/manifest/com/windows10"
  xmlns:desktop="http://schemas.microsoft.com/appx/manifest/desktop/windows10"
  IgnorableNamespaces="... com desktop">
  ...
  <Applications>
    <Application>
      ...
      <Extensions>

        <!--Register COM CLSID LocalServer32 registry key-->
        <com:Extension Category="windows.comServer">
          <com:ComServer>
            <com:ExeServer Executable="YourProject\YourProject.exe" Arguments="-ToastActivated" DisplayName="Toast activator">
              <com:Class Id="replaced-with-your-guid-C173E6ADF0C3" DisplayName="Toast activator"/>
            </com:ExeServer>
          </com:ComServer>
        </com:Extension>

        <!--Specify which CLSID to activate when toast clicked-->
        <desktop:Extension Category="windows.toastNotificationActivation">
          <desktop:ToastNotificationActivation ToastActivatorCLSID="replaced-with-your-guid-C173E6ADF0C3" /> 
        </desktop:Extension>

      </Extensions>
    </Application>
  </Applications>
 </Package>
```

Finally, in your app's startup code, call the following RegisterActivator method, passing in your implementation of the NotificationActivator class you created above. This must be called in order for you to receive any toast activations.

```cpp
// Register activator
DesktopNotificationManagerCompat::RegisterActivator(__uuidof(NotificationActivator));
```

Now, when the user clicks on your toast, the **Activate** method of your **NotificationActivator** class is invoked.

Inside the Activate method, you can parse the args that you specified in the toast and obtain the user input that the user typed or selected, and then activate your app accordingly.

> [!NOTE]
> The **Activate** method is called on a separte thread from your main thread.

```csharp
// The GUID must be unique to your app. Create a new GUID if copying this code.
class DECLSPEC_UUID("replaced-with-your-guid-C173E6ADF0C3") NotificationActivator WrlSealed WrlFinal
    : public RuntimeClass<RuntimeClassFlags<ClassicCom>, INotificationActivationCallback>
{
public: 
    virtual HRESULT STDMETHODCALLTYPE Activate(
        _In_ LPCWSTR appUserModelId,
        _In_ LPCWSTR invokedArgs,
        _In_reads_(dataCount) const NOTIFICATION_USER_INPUT_DATA* data,
        ULONG dataCount) override
    {
        std::wstring arguments(invokedArgs);
        HRESULT hr = S_OK;

        // Background: Quick reply to the conversation
        if (arguments.find(L"action=reply") == 0)
        {
            // Get the response user typed.
            // We know this is first and only user input since our toasts only have one input
            LPCWSTR response = data[0].Value;

            hr = DesktopToastsApp::SendResponse(response);
        }

        else
        {
            // The remaining scenarios are foreground activations,
            // so we first make sure we have a window open and in foreground
            hr = DesktopToastsApp::GetInstance()->OpenWindowIfNeeded();
            if (SUCCEEDED(hr))
            {
                // Open the image
                if (arguments.find(L"action=viewImage") == 0)
                {
                    hr = DesktopToastsApp::GetInstance()->OpenImage();
                }

                // Open the app itself
                // User might have clicked on app title in Action Center which launches with empty args
                else
                {
                    // Nothing to do, already launched
                }
            }
        }

        if (FAILED(hr))
        {
            // Log failed HRESULT
        }

        return S_OK;
    }

    ~NotificationActivator()
    {
        // If we don't have window open
        if (!DesktopToastsApp::GetInstance()->HasWindow())
        {
            // Exit (this is for background activation scenarios)
            exit(0);
        }
    }
};

// Flag class as COM creatable
CoCreatableClass(NotificationActivator);
```

To properly support being launched while your app is closed, in your WinMain function, you'll want to determine whether you're being launched from a toast or not. If launched from a toast, there will be a launch arg of "-ToastActivated". When you see this, you should stop performing any normal launch activation code, and allow your **NotificationActivator** to handle launching windows if needed.

```cpp
// Main function
int WINAPI wWinMain(_In_ HINSTANCE hInstance, _In_opt_ HINSTANCE, _In_ LPWSTR cmdLineArgs, _In_ int)
{
    RoInitializeWrapper winRtInitializer(RO_INIT_MULTITHREADED);

    HRESULT hr = winRtInitializer;
    if (SUCCEEDED(hr))
    {
        // Register AUMID and COM server (for MSIX/sparse package apps, this no-ops)
        hr = DesktopNotificationManagerCompat::RegisterAumidAndComServer(L"WindowsNotifications.DesktopToastsCpp", __uuidof(NotificationActivator));
        if (SUCCEEDED(hr))
        {
            // Register activator type
            hr = DesktopNotificationManagerCompat::RegisterActivator();
            if (SUCCEEDED(hr))
            {
                DesktopToastsApp app;
                app.SetHInstance(hInstance);

                std::wstring cmdLineArgsStr(cmdLineArgs);

                // If launched from toast
                if (cmdLineArgsStr.find(TOAST_ACTIVATED_LAUNCH_ARG) != std::string::npos)
                {
                    // Let our NotificationActivator handle activation
                }

                else
                {
                    // Otherwise launch like normal
                    app.Initialize(hInstance);
                }

                app.RunMessageLoop();
            }
        }
    }

    return SUCCEEDED(hr);
}
```


### Activation sequence of events

The activation sequence is the following...

If your app is already running:

1. **Activate** in your **NotificationActivator** is called

If your app is not running:

1. Your app is EXE launched, you get a command line args of "-ToastActivated"
2. **Activate** in your **NotificationActivator** is called


#### [Win32 WRL](#tab/win32-wrl)

When the user clicks your toast (or a button on the toast with foreground activation), your app's **NotificationActivator** will be invoked. You must extend this class for toast activation, so that when the user clicks on your toast, your app can do something. This lets you handle activation when your toast is clicked days later while your app is closed. This class can be placed anywhere in your project.

Implement the **INotificationActivationCallback** interface as seen below, including a UUID, and also call **CoCreatableClass** to flag your class as COM creatable. For your UUID, create a unique GUID using one of the many online GUID generators. This GUID CLSID (class identifier) is how Action Center knows what class to COM activate.

```cpp
// The UUID CLSID must be unique to your app. Create a new GUID if copying this code.
class DECLSPEC_UUID("replaced-with-your-guid-C173E6ADF0C3") NotificationActivator WrlSealed WrlFinal
    : public RuntimeClass<RuntimeClassFlags<ClassicCom>, INotificationActivationCallback>
{
public:
    virtual HRESULT STDMETHODCALLTYPE Activate(
        _In_ LPCWSTR appUserModelId,
        _In_ LPCWSTR invokedArgs,
        _In_reads_(dataCount) const NOTIFICATION_USER_INPUT_DATA* data,
        ULONG dataCount) override
    {
        // TODO: Handle activation
    }
};

// Flag class as COM creatable
CoCreatableClass(NotificationActivator);
```

Then, in your app's startup code, call the following **RegisterActivator** method, passing in your implementation of the NotificationActivator class you created above. This must be called in order for you to receive any toast activations.

```cpp
// Register activator
DesktopNotificationManagerCompat::RegisterActivator(__uuidof(NotificationActivator));
```

Now, when the user clicks on your toast, the **Activate** method of your **NotificationActivator** class is invoked.

Inside the Activate method, you can parse the args that you specified in the toast and obtain the user input that the user typed or selected, and then activate your app accordingly.

> [!NOTE]
> The **Activate** method is called on a separte thread from your main thread.

```csharp
// The GUID must be unique to your app. Create a new GUID if copying this code.
class DECLSPEC_UUID("replaced-with-your-guid-C173E6ADF0C3") NotificationActivator WrlSealed WrlFinal
    : public RuntimeClass<RuntimeClassFlags<ClassicCom>, INotificationActivationCallback>
{
public: 
    virtual HRESULT STDMETHODCALLTYPE Activate(
        _In_ LPCWSTR appUserModelId,
        _In_ LPCWSTR invokedArgs,
        _In_reads_(dataCount) const NOTIFICATION_USER_INPUT_DATA* data,
        ULONG dataCount) override
    {
        std::wstring arguments(invokedArgs);
        HRESULT hr = S_OK;

        // Background: Quick reply to the conversation
        if (arguments.find(L"action=reply") == 0)
        {
            // Get the response user typed.
            // We know this is first and only user input since our toasts only have one input
            LPCWSTR response = data[0].Value;

            hr = DesktopToastsApp::SendResponse(response);
        }

        else
        {
            // The remaining scenarios are foreground activations,
            // so we first make sure we have a window open and in foreground
            hr = DesktopToastsApp::GetInstance()->OpenWindowIfNeeded();
            if (SUCCEEDED(hr))
            {
                // Open the image
                if (arguments.find(L"action=viewImage") == 0)
                {
                    hr = DesktopToastsApp::GetInstance()->OpenImage();
                }

                // Open the app itself
                // User might have clicked on app title in Action Center which launches with empty args
                else
                {
                    // Nothing to do, already launched
                }
            }
        }

        if (FAILED(hr))
        {
            // Log failed HRESULT
        }

        return S_OK;
    }

    ~NotificationActivator()
    {
        // If we don't have window open
        if (!DesktopToastsApp::GetInstance()->HasWindow())
        {
            // Exit (this is for background activation scenarios)
            exit(0);
        }
    }
};

// Flag class as COM creatable
CoCreatableClass(NotificationActivator);
```

To properly support being launched while your app is closed, in your WinMain function, you'll want to determine whether you're being launched from a toast or not. If launched from a toast, there will be a launch arg of "-ToastActivated". When you see this, you should stop performing any normal launch activation code, and allow your **NotificationActivator** to handle launching windows if needed.

```cpp
// Main function
int WINAPI wWinMain(_In_ HINSTANCE hInstance, _In_opt_ HINSTANCE, _In_ LPWSTR cmdLineArgs, _In_ int)
{
    RoInitializeWrapper winRtInitializer(RO_INIT_MULTITHREADED);

    HRESULT hr = winRtInitializer;
    if (SUCCEEDED(hr))
    {
        // Register AUMID and COM server (for MSIX/sparse package apps, this no-ops)
        hr = DesktopNotificationManagerCompat::RegisterAumidAndComServer(L"WindowsNotifications.DesktopToastsCpp", __uuidof(NotificationActivator));
        if (SUCCEEDED(hr))
        {
            // Register activator type
            hr = DesktopNotificationManagerCompat::RegisterActivator();
            if (SUCCEEDED(hr))
            {
                DesktopToastsApp app;
                app.SetHInstance(hInstance);

                std::wstring cmdLineArgsStr(cmdLineArgs);

                // If launched from toast
                if (cmdLineArgsStr.find(TOAST_ACTIVATED_LAUNCH_ARG) != std::string::npos)
                {
                    // Let our NotificationActivator handle activation
                }

                else
                {
                    // Otherwise launch like normal
                    app.Initialize(hInstance);
                }

                app.RunMessageLoop();
            }
        }
    }

    return SUCCEEDED(hr);
}
```


### Activation sequence of events

The activation sequence is the following...

If your app is already running:

1. **Activate** in your **NotificationActivator** is called

If your app is not running:

1. Your app is EXE launched, you get a command line args of "-ToastActivated"
2. **Activate** in your **NotificationActivator** is called

---


## Adding images

You can add rich content to notifications. We'll add an inline image and a profile (app logo override) image.

> [!NOTE]
> Images can be used from the app's package, the app's local storage, or from the web. As of the Fall Creators Update, web images can be up to 3 MB on normal connections and 1 MB on metered connections. On devices not yet running the Fall Creators Update, web images must be no larger than 200 KB.

> [!IMPORTANT]
> Http images are only supported in UWP/MSIX/sparse apps that have the internet capability in their manifest. Win32 non-MSIX/sparse apps do not support http images; you must download the image to your local app data and reference it locally.

<img alt="Toast with images" src="images/send-toast-02.png" width="364"/>



## Adding buttons and inputs

You can add buttons and inputs to make your notifications interactive. Buttons can launch your foreground app, a protocol, or your background task. We'll add a reply text box, a "Like" button, and a "View" button that opens the image.

<img alt="Toast with images and buttons" src="images/send-toast-03.png" width="364"/>

The activation of foreground buttons are handled in the same way as the main toast body (your App.xaml.cs OnActivated will be called).



## Handling background activation

#### [UWP C++/CX](#tab/uwp)

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


Then in your App.xaml.cs, override the OnBackgroundActivated method you can retrieve the pre-defined arguments and user input, similar to the foreground activation.

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

#### [Win32 WRL MSIX/sparse](#tab/win32-wrl-msix-sparse)

For Win32 applications, background activations are handled the same as foreground activations (they'll call your **NotificationActivator**). You can choose to not show any UI and close your app after handling activation.

#### [Win32 WRL](#tab/win32-wrl)

For Win32 applications, background activations are handled the same as foreground activations (they'll call your **NotificationActivator**). You can choose to not show any UI and close your app after handling activation.

---



## Set an expiration time

In Windows 10, all toast notifications go in Action Center after they are dismissed or ignored by the user, so users can look at your notification after the popup is gone.

However, if the message in your notification is only relevant for a period of time, you should set an expiration time on the toast notification so the users do not see stale information from your app. For example, if a promotion is only valid for 12 hours, set the expiration time to 12 hours. In the code below, we set the expiration time to be 2 days.

> [!NOTE]
> The default and maximum expiration time for local toast notifications is 3 days.

```cpp
notification->put_ExpirationTime(expirationTime);
```


## Provide a primary key for your toast

If you want to programmatically remove or replace the notification you send, you need to use the Tag property (and optionally the Group property) to provide a primary key for your notification. Then, you can use this primary key in the future to remove or replace the notification.

To see more details on replacing/removing already delivered toast notifications, please see [Quickstart: Managing toast notifications in action center (XAML)](https://docs.microsoft.com/previous-versions/windows/apps/dn631260(v=win.10)).

Tag and Group combined act as a composite primary key. Group is the more generic identifier, where you can assign groups like "wallPosts", "messages", "friendRequests", etc. And then Tag should uniquely identify the notification itself from within the group. By using a generic group, you can then remove all notifications from that group by using the [RemoveGroup API](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationHistory#Windows_UI_Notifications_ToastNotificationHistory_RemoveGroup_System_String_).

```csharp
notification.Tag = "18365";
notification.Group = "wallPosts";
```



## Clear your notifications

UWP apps are responsible for removing and clearing their own notifications. When your app is launched, we do NOT automatically clear your notifications.

Windows will only automatically remove a notification if the user explicitly clicks the notification.

Here's an example of what a messaging app should do…

1. User receives multiple toasts about new messages in a conversation
2. User taps one of those toasts to open the conversation
3. The app opens the conversation and then clears all toasts for that conversation (by using [RemoveGroup](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationHistory#Windows_UI_Notifications_ToastNotificationHistory_RemoveGroup_System_String_) on the app-supplied group for that conversation)
4. User's Action Center now properly reflects the notification state, since there are no stale notifications for that conversation left in Action Center.

To learn about clearing all notifications or removing specific notifications, see [Quickstart: Managing toast notifications in action center (XAML)](https://docs.microsoft.com/previous-versions/windows/apps/dn631260(v=win.10)).





## Resources

* [Full code sample on GitHub](https://github.com/WindowsNotifications/quickstart-sending-local-toast-win10)
* [Toast content documentation](adaptive-interactive-toasts.md)
* [ToastNotification Class](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotification)
* [ToastNotificationActivatedEventArgs Class](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ToastNotificationActivatedEventArgs)
