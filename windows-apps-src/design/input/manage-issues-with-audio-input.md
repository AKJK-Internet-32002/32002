---
author: Karl-Bridge-Microsoft
Description: Learn how to manage issues with speech-recognition accuracy caused by audio-input quality.
title: Manage issues with audio input
ms.assetid: 3E36C683-C96A-4FEE-AD52-FDB87E0CC299
label: Manage audio input issues
template: detail.hbs
keywords: speech, voice, speech recognition, natural language, dictation, input, user interaction
ms.author: kbridge
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
---

# Manage issues with audio input


Learn how to manage issues with speech-recognition accuracy caused by audio-input quality.

<div class="important-apis" >
<b>Important APIs</b><br/>
<ul>
<li>[**SpeechRecognizer**](https://msdn.microsoft.com/library/windows/apps/dn653226)</li>
<li>[**RecognitionQualityDegrading**](https://msdn.microsoft.com/library/windows/apps/dn653243)</li>
<li>[**SpeechRecognitionAudioProblem**](https://msdn.microsoft.com/library/windows/apps/dn631406)</li>

</ul>
</div>


## Assess audio-input quality


When speech recognition is active, use the [**RecognitionQualityDegrading**](https://msdn.microsoft.com/library/windows/apps/dn653243) event of your speech recognizer to determine whether one or more audio issues might be interfering with speech input. The event argument ([**SpeechRecognitionQualityDegradingEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn631430)) provides the [**Problem**](https://msdn.microsoft.com/library/windows/apps/dn631431) property, which describes the issues detected with the audio input.

Recognition can be affected by too much background noise, a muted microphone, and the volume or speed of the speaker.

Here, we configure a speech recognizer and start listening for the [**RecognitionQualityDegrading**](https://msdn.microsoft.com/library/windows/apps/dn653243) event.

```CSharp
private async void WeatherSearch_Click(object sender, RoutedEventArgs e)
{
    // Create an instance of SpeechRecognizer.
    var speechRecognizer = new Windows.Media.SpeechRecognition.SpeechRecognizer();

    // Listen for audio input issues.
    speechRecognizer.RecognitionQualityDegrading += speechRecognizer_RecognitionQualityDegrading;

    // Add a web search grammar to the recognizer.
    var webSearchGrammar = new Windows.Media.SpeechRecognition.SpeechRecognitionTopicConstraint(Windows.Media.SpeechRecognition.SpeechRecognitionScenario.WebSearch, "webSearch");


    speechRecognizer.UIOptions.AudiblePrompt = "Say what you want to search for...";
    speechRecognizer.UIOptions.ExampleText = "Ex. 'weather for London'";
    speechRecognizer.Constraints.Add(webSearchGrammar);

    // Compile the constraint.
    await speechRecognizer.CompileConstraintsAsync();

    // Start recognition.
    Windows.Media.SpeechRecognition.SpeechRecognitionResult speechRecognitionResult = await speechRecognizer.RecognizeWithUIAsync();
    //await speechRecognizer.RecognizeWithUIAsync();

    // Do something with the recognition result.
    var messageDialog = new Windows.UI.Popups.MessageDialog(speechRecognitionResult.Text, "Text spoken");
    await messageDialog.ShowAsync();
}
```

## Manage the speech-recognition experience


Use the description provided by the [**Problem**](https://msdn.microsoft.com/library/windows/apps/dn631431) property to help the user improve conditions for recognition.

Here, we create a handler for the [**RecognitionQualityDegrading**](https://msdn.microsoft.com/library/windows/apps/dn653243) event that checks for a low volume level. We then use a [**SpeechSynthesizer**](https://msdn.microsoft.com/library/windows/apps/dn298152) object to suggest that the user try speaking louder.

```CSharp
private async void speechRecognizer_RecognitionQualityDegrading(
    Windows.Media.SpeechRecognition.SpeechRecognizer sender,
    Windows.Media.SpeechRecognition.SpeechRecognitionQualityDegradingEventArgs args)
{
    // Create an instance of a speech synthesis engine (voice).
    var speechSynthesizer =
        new Windows.Media.SpeechSynthesis.SpeechSynthesizer();

    // If input speech is too quiet, prompt the user to speak louder.
    if (args.Problem == Windows.Media.SpeechRecognition.SpeechRecognitionAudioProblem.TooQuiet)
    {
        // Generate the audio stream from plain text.
        Windows.Media.SpeechSynthesis.SpeechSynthesisStream stream;
        try
        {
            stream = await speechSynthesizer.SynthesizeTextToStreamAsync("Try speaking louder");
            stream.Seek(0);
        }
        catch (Exception)
        {
            stream = null;
        }

        // Send the stream to the MediaElement declared in XAML.
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.High, () =>
        {
            this.media.SetSource(stream, stream.ContentType);
        });
    }
}
```

## Related articles


* [Speech interactions](speech-interactions.md)

**Samples**
* [Speech recognition and speech synthesis sample](http://go.microsoft.com/fwlink/p/?LinkID=619897)
??

??




