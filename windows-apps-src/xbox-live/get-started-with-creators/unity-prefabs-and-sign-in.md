---
title: XBL In Unity Prefabs and Sign-In
author: aablackm
description: Covers the the social prefabs and script examples for social services on Xbox Live
ms.author: aablackm
ms.date: 1/24/2018
ms.topic: get-started-article
ms.prod: windows
ms.technology: uwp
keywords: xbox live, xbox, games, uwp, windows 10, xbox one, unity
---

#Unity Prefabs and Sign-In

Unity is a popular game engine for 2D and 3D games. You can learn more about Unity on their [website](https://unity3d.com/).

##Before you begin
Before you start adding Xbox Live social services to your Unity game there are a few steps you'll need to complete before diving in. First, make sure that you have downloaded and integrated the [Xbox Live Unity plugin](https://github.com/Microsoft/xbox-live-unity-plugin). Second, you'll want to have your title reserved and published through the [Microsoft Development Center](https://developer.microsoft.com/en-us/games/uwp). Read [Create a new Xbox Live Creators Program title](../get-started-with-creators/create-and-test-a-new-creators-title.md) for instructions on publishing your title.
Finally,  read [Configure Xbox Live In Unity](../get-started-with-creators/configure-xbox-live-in-unity.md) to setup your Unity environment properly and configure your title to use Xbox Live services. Once your Unity project is set up properly, it's time to learn about the social tools you can use in your Xbox Live enabled title, as well as the two main ways you can implement Xbox Live in Unity: prefabs and scripts.

##Prefabs
Unity has a prefab asset type that allows you to store a GameObject complete with components and properties. The prefab acts as a template from which you can create new object instances in your Unity scene.
[Learn more about prefabs from the Unity website](https://unity3d.com/learn/tutorials/topics/interface-essentials/prefabs-concept-usage).

The Xbox Live Unity plugin provides a few prefabs that you can use in your project to utilize Xbox Live social features. The following prefabs will allow you to sign in, [add multi-user support](../get-started-with-creators/add-multi-user-support.md) to your title, or display the friends list of a signed in Xbox Live profile. You can find the prefabs under the **Project** tab by following the path: **Assets > Xbox Live > Prefabs**.

###The UserProfile prefab
The first and most important social prefab is the **UserProfile** prefab. The **UserProfile** prefab has everything required to allow an Xbox Live sign-in. This is very important as you must sign-in a user before using Xbox Live services. The prefab contains both the sign-in button and a GameObject to represent a logged in player by their Gamertag, Gamerpic, and Gamerscore.

> [!NOTE]
> In order to use any of the other Xbox Live prefabs, you must include a **UserProfile** prefab or manually invoke the sign-in API.

![UserProfile prefab in assets and hierarchy](../images/unity/unity-userprofile-views.png)

If you expand the **UserProfile** prefab in the **Project** panel or in the **hierarchy** after it???s been added to a scene, you???ll see that the **UserProfile** prefab contains two GameObjects inside of it. The first object is the **SignInPanel** which contains the sign-in button experience. The second object is the **ProfileInfo** which will contain the information about the user once they sign in. The **UserProfile** prefab is what you will use to represent the information of any Xbox Live user signed-in locally to your title.

###The XboxLiveUser prefab
The **UserProfile** prefab uses a second social prefab in its code called the **XboxLiveUser**. This prefab's use is not immediately evident as it does not need to be added to the scene hierarchy, as it may simply be instantiated in code. The **XboxLiveUser** has no visual representation, it simply contains the details that pertain to the Xbox Live user. You will need an instance of the **XboxLiveUser** for every instance of the **UserProfile**. This is important when [adding multi-user support](../get-started-with-creators/add-multi-user-support.md) to your title. In addition to holding information about the user after sign-in this prefab is also a wrapper for the code used to sign-in an Xbox Live user.

###The Social prefab
The **Social** prefab allows your Unity title to display the friends and presence of a signed-in Xbox Live user. The prefab is a scroll-enabled friends list which will also allow users to filter their friends by presence. The **Social** prefab also contains a reference to the **XboxLiveUser** it uses to pull the appropriate friends list to display.

##Sign-in with the UserProfile prefab
The Xbox Live Unity plugin prefabs exist to make certain development tasks much easier. To enable Xbox Live sign-in for your Unity project you will simply need to use the **UserProfile** and **XboxLiveServices** prefabs along with a Unity **EventSystem**.

First drag the **UserProfile** prefab into a scene. Ideally the **UserProfile** should be placed on the initial menu screen of your project.

![drag userprofile to hierarchy](../images/unity/drag-userprofile.gif)

In addition to the **UserProfile** prefab you will also need to make sure that the **XboxLiveServices** prefab is present in at least the first scene of your project.
The **XboxLiveServices** prefab allows you to toggle whether or not certain prefabs will log information for debugging. This is useful for checking on prefab behavior.

![check for xboxliveservices prefab](../images/unity/check-for-xboxliveservices.gif)

Finally, the **UserProfile** also requires an **EventSystem** to run properly. This can be added by right-clicking on the sign-in scene then clicking through **GameObject --> UI --> EventSystem**.

![add event system](../images/unity/add_event_system.gif)

If you enter play mode, the service will sign in a user automatically. In Unity, the Xbox Live SDK simulates calls to the Xbox Live service and sends back fake data for you to work with. In order to view live data, you will need to build the project as a UWP application and run it from Visual Studio. See [Configure Xbox Live in Unity](../get-started-with-creators/configure-xbox-live-in-unity.md) for more information. When you enter play mode in Unity, the prefab will be populated with the fake data, which will simulate information such as the Gamertag, Gamerpic and Gamerscore of a player. This is the information that should be displayed by the **UserProfile** prefab.

A succesful sign-in will look like the following:
![successful userprofile play](../images/unity/correct-user-profile-play.gif)

If you have not configured your project to connect to Xbox Live properly entering play mode will disable the sign-in button and display an error message.

The following is an example of a failed sign-in due to a bad Xbox Live app configuration.
![failed userprofile play](../images/unity/flawed-user-profile-play.gif)

##Scripting sign-in
Now that you know how to use the **UserProfile** prefab it would be best to look at the underlying script that governs the functionality of the prefab. If you look at the **UserProfile** in the **Inspector** you will see that it has a **UserProfile.cs** script attached to it. This script has everything you need to sign in a user and load profile information you'd like to display on sign-in. However, instead of looking at the entire prefab (which may be updated over time) we are going to look at a few sample lines of code to understand what is needed to sign-in an Xbox Live user.

###The XboxLiveUser class
The calls needed to sign-in a user are wrapped in the `XboxLiveUserInfo` class. In the **UserProfile.cs** script you will see that there is an instance of the `XboxLiveUserInfo` class called `XboxLiveUser`. We will use the same variable name in our example code. The `XboxLiveUserInfo` class contains an instance of the `XboxLiveUser` class called `User` as one of its member variables. The `XboxLiveUser` class contains the sign-in functions required to sign in the `XboxLiveUser`. You will use the instance of the `XboxLiveUser` class `User` to sign-in users as well as to gain information that describes them like their Gamertag, Gamerpic, and Gamerscore. In order to do this you must initialize an instance of the `XboxLiveUserInfo` class and use the resulting `XboxLiveUserInfo.User` to call sign-in.

###Initialize the XboxLiveUser
Initializing the Xbox Live User is the first step before actually signing them into Xbox Live. This is done very simply in code by using the `XboxLiveUserInfo.Initialize()` function.
In our example code we use the `XboxLiveUser` member variable as our `XboxLiveUserInfo` instance and initialize that to use for sign-in.

```csharp
    void ButtonClickTask()
    {
        this.StartCoroutine(this.InitializeXboxLiveUser());
    }

    public IEnumerator InitializeXboxLiveUserAndCallSignIn()
    {
        // Disable the sign-in button
        SignInButton.interactable = false;

        this.XboxLiveUser.Initialize();

        //Wait until the Xbox User has been initialized to call SignInAsync()
        yield return new WaitUntil(() => this.XboxLiveUser != null && this.XboxLiveUser.User != null);
        this.StartCoroutine(this.SignInAsync());
    }
```
Looking at this sample code you will see that the `XboxLiveUserInfo.Initialize()` function is called in response to a button click. The full **UserProfile.cs** prefab script has code that allows for automatic sign-in where `XboxLiveUserInfo.Initialize()` is called without interaction.
The `XboxLiveUserInfo.Initialize()` function will create a new `XboxLiveUserInfo.User` which will allow us to call the sign-in functions contained in the `XboxLiveUserInfo.User` class.

###Call Sign-In
Once the XboxLiveUser has been initialized it's time to call sign-in. In **UserProfile.cs** sign-in is called in the `SignInAsync()` function of UserProfile.cs. In the previous example code we simply wait for the `XboxLiveUser` to be initialized before calling the `SignInAsync()` function.

> [!NOTE]
> It is necessary to wait for the `XboxLiveUser` to be initialized before calling sign-in because the `XboxLiveUser` contains the `XboxLiveUser.User` property which is used to call sign-in.

In **UserProfile.cs** the `SignInAsync()` function contains two sign-in functions that may be used to sign in the user. `XboxLiveUser.User.SignInSilentlyAsync()` and `XboxLiveUser.User.SignInAsync()` These are the functions that sign the user in. The `SignInAsync()` function is a good example of how to use these functions appropriately. The following code sample shows one appropriate method for calling the two sign-in functions:

```csharp
SignInStatus signInStatus;
TaskYieldInstruction<SignInResult> signInSilentlyTask = this.XboxLiveUser.User.SignInSilentlyAsync().AsCoroutine();
yield return signInSilentlyTask;

signInStatus = signInSilentlyTask.Result.Status;
if (signInSilentlyTask.Result.Status != SignInStatus.Success)
{
    TaskYieldInstruction<SignInResult> signInTask = this.XboxLiveUser.User.SignInAsync().AsCoroutine();
    yield return signInTask;

    signInStatus = signInTask.Result.Status;
}
```
In this example the results of the sign-in calls are stored in the variable `signInStatus`. This allows us to check whether or not the sign-in was succesful and act accordingly. In this example the function first attempts to sign-in silently, then if the silent sign in fails the function calls the normal sign in function. Once you have a succesful call to one of the sign-in functions you will have signed in the user. You can now use the `XboxLiveUser.User` to obtain and display details about the signed in user. Take a look at the `LoadProfileInfo()` function in **UserProfile.cs** for an example of how to use the `XboxLiveUser.User` to display information about a signed in user.

##Troubleshooting
If you're having issues signing in to Xbox Live try reading [Troubleshooting Xbox Live sign-in](../using-xbox-live/troubleshooting/troubleshooting-sign-in.md).