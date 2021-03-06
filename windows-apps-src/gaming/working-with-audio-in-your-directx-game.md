---
author: mtoepke
title: Audio for games
description: Learn how to develop and incorporate music and sounds into your DirectX game, and how to process the audio signals to create dynamic and positional sounds.
ms.assetid: ab29297a-9588-c79b-24c5-3b94b85e74a8
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, games, audio, directx
ms.localizationpriority: medium
---

# Audio for games



Learn how to develop and incorporate music and sounds into your DirectX game, and how to process the audio signals to create dynamic and positional sounds.

For audio programming, we recommend using the XAudio2 library in DirectX, and we use it here. XAudio2 is a low-level audio library that provides a signal processing and mixing foundation for games, and it supports a variety of formats.

You can also implement simple sounds and music playback with [Microsoft Media Foundation](https://msdn.microsoft.com/library/windows/desktop/ms694197). Microsoft Media Foundation is designed for the playback of media files and streams, both audio and video, but can also be used in games, and is particularly useful for cinematic scenes or non-interactive components of your game.

## Concepts at a glance


Here are a few audio programming concepts we use in this section.

-   Signals are the basic unit of sound programming, analogous to pixels in graphics. The digital signal processors (DSPs) that process them are like the pixel shaders of game audio. They can transform signals, or combine them, or filter them. By programming to the DSPs, you can alter your game's sound effects and music with as little or as much complexity as you need.
-   Voices are the submixed composites of two or more signals. There are 3 types of XAudio2 voice objects: source, submix, and mastering voices. Source voices operate on audio data provided by the client. Source and submix voices send their output to one or more submix or mastering voices. Submix and mastering voices mix the audio from all voices feeding them, and operate on the result. Mastering voices write audio data to an audio device.
-   Mixing is the process of combining several discrete voices, such as the sound effects and the background audio that are played back in a scene, into a single stream. Submixing is the process of combining several discrete signals, such as the component sounds of an engine noise, and creating a voice.
-   Audio formats. Music and sound effects can be stored in a variety of digital formats for your game. There are uncompressed formats, like WAV, and compressed formats like MP3 and OGG. The more a sample is compressed -- typically designated by its bit rate, where the lower the bit rate is, the more lossy the compression -- the worse fidelity it has. Fidelity can vary across compression schemes and bit rates, so experiment with them to find what works best for your game.
-   Sample rate and quality. Sounds can be sampled at different rates, and sounds sampled at a lower rate have much poorer fidelity. The sample rate for CD quality is 44.1 Khz (44100 Hz). If you don't need high fidelity for a sound, you can choose a lower sample rate. Higher rates may be appropriate for professional audio applications, but you probably don't need them unless your game demands professional fidelity sound.
-   Sound emitters (or sources). In XAudio2, sound emitters are locations that emit a sound, be it a mere blip of a background noise or a snarling rock track played by an in-game jukebox. You specify emitters by world coordinates.
-   Sound listeners. A sound listener is often the player, or perhaps an AI entity in a more advanced game, that processes the sounds received from a listener. You can submix that sound into the audio stream for playback to the player, or you can use it to take a specific in-game action, like awakening an AI guard marked as a listener.

## Design considerations


Audio is a tremendously important part of game design and development. Many gamers can recall a mediocre game elevated to legendary status just because of a memorable soundtrack, or great voice work and sound mixing, or overall stellar audio production. Music and sound define a game's personality, and establish the main motive that defines the game and makes it stand apart from other similar games. The effort you spend designing and developing your game's audio profile will be well worth it.

Positional 3D audio can add a level of immersion beyond that provided by 3D graphics. If you are developing a complex game that simulates a world, or which demands a cinematic style, consider using 3D positional audio techniques to really draw the player in.

## DirectX audio development roadmap


### XAudio2 conceptual resources

XAudio2 is the audio mixing library for DirectX, and is primarily intended for developing high performance audio engines for games. For game developers who want to add sound effects and background music to their modern games, XAudio2 offers an audio graph and mixing engine with low-latency and support for dynamic buffers, synchronous sample-accurate playback, and implicit source rate conversion.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Topic</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[Introduction to XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415813)</p></td>
<td align="left"><p>The topic provides a list of the audio programming features supported by XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Getting Started with XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415762)</p></td>
<td align="left"><p>This topic provides information on key XAudio2 concepts, XAudio2 versions, and the RIFF audio format.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Common Audio Programming Concepts](https://msdn.microsoft.com/library/windows/desktop/ee415692)</p></td>
<td align="left"><p>This topic provides an overview of common audio concepts with which an audio developer should be familiar.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[XAudio2 Voices](https://msdn.microsoft.com/library/windows/desktop/ee415825)</p></td>
<td align="left"><p>This topic contains an overview of XAudio2 voices, which are used to submix, operate on, and master audio data.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[XAudio2 Callbacks](https://msdn.microsoft.com/library/windows/desktop/ee415745)</p></td>
<td align="left"><p>This topic covers the XAudio 2 callbacks, which are used to prevent breaks in the audio playback.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[XAudio2 Audio Graphs](https://msdn.microsoft.com/library/windows/desktop/ee415739)</p></td>
<td align="left"><p>This topic covers the XAudio2 audio processing graphs, which take a set of audio streams from the client as input, process them, and deliver the final result to an audio device.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[XAudio2 Audio Effects](https://msdn.microsoft.com/library/windows/desktop/ee415756)</p></td>
<td align="left"><p>The topic covers XAudio2 audio effects, which take incoming audio data and perform some operation on the data (such as a reverb effect) before passing it on.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Streaming Audio Data with XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415821)</p></td>
<td align="left"><p>This topic covers audio streaming with XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[X3DAudio](https://msdn.microsoft.com/library/windows/desktop/ee415714)</p></td>
<td align="left"><p>this topic covers X3DAudio, an API used in conjunction with XAudio2 to create the illusion of a sound coming from a point in 3D space.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[XAudio2 Programming Reference](https://msdn.microsoft.com/library/windows/desktop/ee415899)</p></td>
<td align="left"><p>This section contains the complete reference for the XAudio2 APIs.</p></td>
</tr>
</tbody>
</table>

??

### XAudio2 "how to" resources

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Topic</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[How to: Initialize XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415779)</p></td>
<td align="left"><p>Learn how to initialize XAudio2 for audio playback by creating an instance of the XAudio2 engine, and creating a mastering voice.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Load Audio Data Files in XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415781)</p></td>
<td align="left"><p>Learn how to populate the structures required to play audio data in XAudio2.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Play a Sound with XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415787)</p></td>
<td align="left"><p>Learn how to play previously-loaded audio data in XAudio2.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Use Submix Voices](https://msdn.microsoft.com/library/windows/desktop/ee415794)</p></td>
<td align="left"><p>Learn how to set groups of voices to send their output to the same submix voice.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Use Source Voice Callbacks](https://msdn.microsoft.com/library/windows/desktop/ee415769)</p></td>
<td align="left"><p>Learn how to use XAudio2 source voice callbacks.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Use Engine Callbacks](https://msdn.microsoft.com/library/windows/desktop/ee415774)</p></td>
<td align="left"><p>Learn how to use XAudio2 engine callbacks.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Build a Basic Audio Processing Graph](https://msdn.microsoft.com/library/windows/desktop/ee415767)</p></td>
<td align="left"><p>Learn how to create an audio processing graph, constructed from a single mastering voice and a single source voice.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Dynamically Add or Remove Voices From an Audio Graph](https://msdn.microsoft.com/library/windows/desktop/ee415772)</p></td>
<td align="left"><p>Learn how to add or remove submix voices from a graph that has been created following the steps in [How to: Build a Basic Audio Processing Graph](https://msdn.microsoft.com/library/windows/desktop/ee415767).</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Create an Effect Chain](https://msdn.microsoft.com/library/windows/desktop/ee415789)</p></td>
<td align="left"><p>Learn how to apply an effect chain to a voice to allow custom processing of the audio data for that voice.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Create an XAPO](https://msdn.microsoft.com/library/windows/desktop/ee415730)</p></td>
<td align="left"><p>Learn how to implement [<strong>IXAPO</strong>](https://msdn.microsoft.com/library/windows/desktop/ee415893) to create an XAudio2 audio processing object (XAPO).</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Add Run-time Parameter Support to an XAPO](https://msdn.microsoft.com/library/windows/desktop/ee415728)</p></td>
<td align="left"><p>Learn how to add run-time parameter support to an XAPO by implementing the [<strong>IXAPOParameters</strong>](https://msdn.microsoft.com/library/windows/desktop/ee415896) interface.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Use an XAPO in XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415733)</p></td>
<td align="left"><p>Learn how to use an effect implemented as an XAPO in an XAudio2 effect chain.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Use XAPOFX in XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415723)</p></td>
<td align="left"><p>Learn how to use one of the effects included in XAPOFX in an XAudio2 effect chain.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Stream a Sound from Disk](https://msdn.microsoft.com/library/windows/desktop/ee415791)</p></td>
<td align="left"><p>Learn how to stream audio data in XAudio2 by creating a separate thread to read an audio buffer, and to use callbacks to control that thread.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[How to: Integrate X3DAudio with XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415798)</p></td>
<td align="left"><p>Learn how to use X3DAudio to provide the volume and pitch values for XAudio2 voices as well as the parameters for the XAudio2 built-in reverb effect.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[How to: Group Audio Methods as an Operation Set](https://msdn.microsoft.com/library/windows/desktop/ee415783)</p></td>
<td align="left"><p>Learn how to use XAudio2 operation sets to make a group of method calls take effect at the same time.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Debugging Audio Glitches in XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415765)</p></td>
<td align="left"><p>Learn how to set the debug logging level for XAudio2.</p></td>
</tr>
</tbody>
</table>

??

### Media Foundation resources

Media Foundation (MF) is a media platform for streaming audio and video playback. You can use the Media Foundation APIs to stream audio and video encoded and compressed with a variety of algorithms. It is not designed for real-time gameplay scenarios; instead, it provides powerful tools and broad codec support for more linear capture and presentation of audio and video components.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Topic</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[About Media Foundation](https://msdn.microsoft.com/library/windows/desktop/ms696274)</p></td>
<td align="left"><p>This section contains general information about the Media Foundation APIs, and the tools available to support them.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Media Foundation: Essential Concepts](https://msdn.microsoft.com/library/windows/desktop/ee663601)</p></td>
<td align="left"><p>This topic introduces some concepts that you will need to understand before writing a Media Foundation application.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Media Foundation Architecture](https://msdn.microsoft.com/library/windows/desktop/ms696219)</p></td>
<td align="left"><p>This section describes the general design of Microsoft Media Foundation, as well as the media primitives and processing pipeline it uses.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Audio/Video Capture](https://msdn.microsoft.com/library/windows/desktop/dd317910)</p></td>
<td align="left"><p>This topic describes how to use Microsoft Media Foundation to perform audio and video capture.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Audio/Video Playback](https://msdn.microsoft.com/library/windows/desktop/dd317914)</p></td>
<td align="left"><p>This topic describes how to implement audio/video playback in your app.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Supported Media Formats in Media Foundation](https://msdn.microsoft.com/library/windows/desktop/dd757927)</p></td>
<td align="left"><p>This topic lists the media formats that Microsoft Media Foundation supports natively. (Third parties can support additional formats by writing custom plug-ins.)</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Encoding and File Authoring](https://msdn.microsoft.com/library/windows/desktop/dd318778)</p></td>
<td align="left"><p>This topic describes how to use Microsoft Media Foundation to perform audio and video encoding, and author media files.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Windows Media Codecs](https://msdn.microsoft.com/library/windows/desktop/ff819508)</p></td>
<td align="left"><p>This topic describes how to use the features of the Windows Media Audio and Video codecs to produce and consume compressed data streams.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Media Foundation Programming Reference](https://msdn.microsoft.com/library/windows/desktop/ms704847)</p></td>
<td align="left"><p>This section contains reference information for the Media Foundation APIs.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Media Foundation SDK Samples](https://msdn.microsoft.com/library/windows/desktop/aa371827)</p></td>
<td align="left"><p>This section lists sample apps that demonstrate how to use Media Foundation.</p></td>
</tr>
</tbody>
</table>

??

### Windows Runtime XAML media types

If you are using [DirectX-XAML interop](https://msdn.microsoft.com/library/windows/apps/hh825871), you can incorporate the Windows Runtime XAML media APIs into your UWP apps using DirectX with C++ for simpler game scenarios.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Topic</th>
<th align="left">Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[<strong>Windows.UI.Xaml.Controls.MediaElement</strong>](https://msdn.microsoft.com/library/windows/apps/br242926)</p></td>
<td align="left"><p>XAML element that represents an object that contains audio, video, or both.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Audio, video, and camera](https://msdn.microsoft.com/library/windows/apps/mt203788)</p></td>
<td align="left"><p>Learn how to incorporate basic audio and video in your Universal Windows Platform (UWP) app.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[MediaElement](https://msdn.microsoft.com/library/windows/apps/mt187272)</p></td>
<td align="left"><p>Learn how to play a locally-stored media file in your UWP app.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[MediaElement](https://msdn.microsoft.com/library/windows/apps/mt187272)</p></td>
<td align="left"><p>Learn how to stream a media file with low-latency in your UWP app.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Media casting](https://msdn.microsoft.com/library/windows/apps/mt282143)</p></td>
<td align="left"><p>Learn how to use the Play To contract to stream media from your UWP app to another device.</p></td>
</tr>
</tbody>
</table>

??

## Reference


-   [XAudio2 Introduction](https://msdn.microsoft.com/library/windows/desktop/ee415813)
-   [XAudio2 Programming Guide](https://msdn.microsoft.com/library/windows/desktop/ee415737)
-   [Microsoft Media Foundation overview](https://msdn.microsoft.com/library/windows/desktop/ms694197)

??

## Related topics


-   [XAudio2 Programming Guide](https://msdn.microsoft.com/library/windows/desktop/ee415737)

??

??




