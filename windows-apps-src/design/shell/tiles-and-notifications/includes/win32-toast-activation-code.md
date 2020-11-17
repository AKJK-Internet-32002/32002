```csharp
// Listen to notification activation
ToastNotificationManagerCompat.OnActivated += toastArgs =>
{
    // Obtain the arguments from the notification
    ToastArguments args = ToastArguments.Parse(toastArgs.Argument);

    // Obtain any user input (text boxes, menu selections) from the notification
    ValueSet userInput = e.UserInput;

    // TODO: Show the corresponding content
};
```

> [!NOTE]
> The **OnActivated** event is not called on the UI thread. If you'd like to perform UI thread operations, you must call `Application.Current.Dispatcher.Invoke(callback)`.