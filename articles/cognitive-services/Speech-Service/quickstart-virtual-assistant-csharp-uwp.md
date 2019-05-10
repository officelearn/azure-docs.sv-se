---
title: 'Snabbstart: Anpassade röst första virtuella assistenter (förhandsversion), C# (UWP) – Speech Services'
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar du en C# Universal Windows Platform (UWP)-program med hjälp av den Cognitive Services tal Software Development Kit (SDK). Du ansluter ditt klientprogram till en tidigare skapade Bot Framework-robot som konfigurerats för att använda tal för Direct Line-kanalen. Programmet har skapats med Speech SDK NuGet-paketet och Microsoft Visual Studio 2017.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: travisw
ms.custom: ''
ms.openlocfilehash: e2b25875a0dff12bba32b033bca0c35394d407aa
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465633"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>Snabbstart: Skapa en röst-första virtuella assistenter med tal SDK, UWP

Snabbstarter kan också användas för [tal till text](quickstart-csharp-uwp.md) och [talöversättning](quickstart-translate-speech-uwp.md).

I den här artikeln ska du utveckla en C# Universal Windows Platform (UWP)-program med hjälp av den [tal SDK](speech-sdk.md). Programmet ansluter till en tidigare skapade och konfigurerade robot att aktivera en röst-första virtuella assistenter upplevelse från klientprogrammet. Programmet skapas med [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017 (valfri version).

> [!NOTE]
> Med Universell Windows Platform kan du utveckla appar som körs på valfri enhet som stöder Windows 10, inklusive datorer, Xbox, Surface Hub och andra enheter.

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här snabbstarten krävs:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).
* En tidigare skapad robot som konfigurerats med den [tal för Direct Line-kanal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)

    > [!NOTE]
    > I förhandsversion, tal för Direct Line-kanal stöder för närvarande endast den **westus2** region.

    > [!NOTE]
    > En 30-dagars utvärderingsversion för standardprisnivån som beskrivs i [testa Speech Services kostnadsfritt](get-started.md) är begränsad till **westus** (inte **westus2**) och är därför inte kompatibel med direktanslutning Rad tal. Nivåerna kostnadsfri och standard **westus2** prenumerationer är kompatibla.

## <a name="optional-get-started-fast"></a>Valfritt: Kom igång snabbt

Den här snabbstarten visar steg för steg hur du gör ett enkelt klientprogram att ansluta till din talbaserade robot. Om du vill sätta igång direkt i, fullständiga och redo att kompilera källkoden används i den här snabbstarten finns i den [tal SDK-prov](https://aka.ms/csspeech/samples) under den `quickstart` mapp.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Programmets användargränssnitt definieras med hjälp av XAML. Öppna `MainPage.xaml` i Solution Explorer. Ersätt hela innehållet med i den designer XAML visas den nedan.

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    
        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" Margin="0,10,10,0" Click="ListenButton_ButtonClicked" Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" RelativePanel.AlignBottomWithPanel="True" RelativePanel.AlignRightWithPanel="True" RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce the status update. -->
                            <TextBlock x:Name="StatusBlock" FontWeight="Bold" AutomationProperties.LiveSetting="Assertive"
                    MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

1. Öppna källfilen bakomliggande kod `MainPage.xaml.cs`. Du hittar det grupperade under `MainPage.xaml`. Ersätt innehållet med koden nedan. Här är vad som beskrivs i det här exemplet: 

    * Med hjälp av uttryck för tal- och Speech.Dialog-namnområden
    * En enkel implementeras, så att åtkomst till mikrofonen, anslutna till en knapp-hanterare
    * Grundläggande Användargränssnittet inlärningsverktyg att visa meddelanden och fel i programmet
    * En startsida tidpunkt för initiering kod sökvägen som fylls senare
    * En hjälp att spela upp tillbaka text till tal (utan stöd för direktuppspelning)
    * En tom knappen hanterare påbörja avlyssning som fylls senare

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private SpeechBotConnector botConnector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for accumulates all audio associated with a given PullAudioOutputStream and then plays it to the
            // MediaElement. Long spoken audio will create extra latency and a streaming playback solution (that plays
            // audio while it continues to be received) should be used -- see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeBotConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. Därefter skapar du den `SpeechBotConnector` med din prenumerationsinformation. Lägg till följande metodbrödtexten i `InitializeBotConnector`, ersätta strängarna `YourChannelSecret`, `YourSpeechSubscriptionKey`, och `YourServiceRegion` med dina egna värden för din robot, tal-prenumeration och [region](regions.md).

    > [!NOTE]
    > I förhandsversion, tal för Direct Line-kanal stöder för närvarande endast den **westus2** region.

    > [!NOTE]
    > Information om hur du konfigurerar din robot och hämta en hemlighet channel finns i Bot Framework-dokumentationen för [Direct Line tal kanalen](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

    ```csharp
    // create a BotConnectorConfig by providing a bot secret key and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret";
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey";
    const string region = "YourServiceRegion"; // note: this is assumed as westus2 for preview

    var botConnectorConfig = BotConnectorConfig.FromSecretKey(channelSecret, speechSubscriptionKey, region);
    botConnectorConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    botConnector = new SpeechBotConnector(botConnectorConfig);
    ```

1. `SpeechBotConnector` förlitar sig på flera händelser att kommunicera aktiviteterna bot, tal resultat och annan information. Lägg till hanterare för dessa händelser, lägga till följande i slutet av metodbrödtexten i `InitializeBotConnector`.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };
    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser($"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };
    // Recognizing (not 'Recognized') will provide the intermediate recognized text while an audio stream is being processed
    botConnector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };
    // Recognized (not 'Recognizing') will provide the final recognized text once audio capture is completed
    botConnector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };
    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };
    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. Med den konfiguration som upprättats och händelsehanterare registrerad, den `SpeechBotConnector` nu bara måste lyssna. Lägg till följande till i brödtexten för den `ListenButton_ButtonClicked` -metod i den `MainPage` klass.

    ```csharp
    private async void ListenButton_ButtonClicked(object sender, RoutedEventArgs e)
    {
        if (botConnector == null)
        {
            InitializeBotConnector();
            // Optional step to speed up first interaction: if not called, connection happens automatically on first use
            var connectTask = botConnector.ConnectAsync();
        }

        try
        {
            // Start sending audio to your speech-enabled bot
            var listenTask = botConnector.ListenOnceAsync();

            // You can also send activities to your bot as JSON strings -- Microsoft.Bot.Schema can simplify this
            string speakActivity = @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
            await botConnector.SendActivityAsync(speakActivity);

        }
        catch (Exception ex)
        {
            NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
        }
    }
    ```

1. Spara alla ändringar i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. Från menyn fältet för Visual Studio, Välj **skapa** > **skapa lösning**. Koden bör nu kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-uwp-08-build.png "Slutförd byggprocess")

1. Starta programmet. Från menyn fältet för Visual Studio, Välj **felsöka** > **Starta felsökning**, eller tryck på **F5**.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-uwp-09-start-debugging.png "Starta appen i felsökningsläge")

1. Ett fönster öppnas. I ditt program, Välj **Aktivera mikrofon**, och bekräfta behörighet-begäran som visas.

    ![Skärmbild av behörighetsbegäran](media/sdk/qs-csharp-uwp-10-access-prompt.png "Starta appen i felsökningsläge")

1. Välj **tala med din robot**, och talar ett engelska fras eller en mening i enhetens mikrofon. Ditt tal är överförda till tal för Direct Line-kanal och transkriberas till text som visas i fönstret.
quickstart-cs-uwp-bot-successful-turn

    ![Skärmbild av lyckade bot aktivera](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png "en lyckad bot-aktivera")

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Översätta tal](how-to-translate-speech-csharp.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
