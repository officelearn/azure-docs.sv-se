---
title: 'Snabbstart: Anpassad röst – första virtuella assistenten (för hands C# version), (UWP) – tal tjänsten'
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar du ett C# universell Windows-plattform-program (UWP) med hjälp av Cognitive Services Speech Software Development Kit (SDK). Du ansluter ditt klient program till en tidigare skapad bot Framework-robot som kon figurer ATS för att använda den direkta rad igenkännings kanalen. Programmet har skapats med tal SDK NuGet-paketet och Microsoft Visual Studio 2019.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: travisw
ms.openlocfilehash: c676e98eb812a31d6fb8d7cc0f58929f803c868e
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382078"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>Snabbstart: Skapa en röst-första virtuell assistent med talet SDK, UWP

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-csharp-uwp.md), [tal syntes](quickstart-text-to-speech-csharp-uwp.md)och [tal översättning](quickstart-translate-speech-uwp.md).

I den här artikeln ska du utveckla ett C# universell Windows-plattform-program (UWP) med hjälp av [tal-SDK](speech-sdk.md). Programmet ansluter till en tidigare skapad och konfigurerad bot för att aktivera en röst-och första funktion i den virtuella assistenten från klient programmet. Programmet har skapats med [tal SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2019 (alla versioner).

> [!NOTE]
> Med Universell Windows Platform kan du utveckla appar som körs på valfri enhet som stöder Windows 10, inklusive datorer, Xbox, Surface Hub och andra enheter.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* En Azure-prenumerations nyckel för tal tjänster. [Hämta ett kostnads fritt](get-started.md) eller skapa det på [Azure Portal](https://portal.azure.com).
* En tidigare skapad robot som kon figurer ATS med den [direkta rad igenkännings kanalen](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

  > [!NOTE]
  > Direkt linje tal (för hands version) är för närvarande tillgängligt i en delmängd av tal Services-regioner. Se [listan över regioner som stöds för röst-första virtuella assistenter](regions.md#voice-first-virtual-assistants) och se till att dina resurser distribueras i någon av dessa regioner.

## <a name="optional-get-started-fast"></a>Valfritt: Kom igång snabbt

I den här snabb starten beskrivs steg för steg hur du gör ett klient program för att ansluta till din Speech-aktiverade bot. Om du föredrar att använda den fullständiga käll koden för färdig att kompilera som används i den här snabb starten finns den i [exemplen för tal-SDK](https://aka.ms/csspeech/samples) under `quickstart` mappen.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

Lägg nu till XAML-koden som definierar användar gränssnittet för programmet och Lägg till C# koden för bakomliggande implementering.

### <a name="xaml-code"></a>XAML-kod

Först skapar du programmets användar gränssnitt genom att lägga till XAML-koden:

1. Öppna`MainPage.xaml`i **Solution Explorer**.

1. I designerns XAML-vy ersätter du hela innehållet med följande kodfragment:

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
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  
                        Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
                        Margin="0,10,10,0" Click="ListenButton_ButtonClicked" 
                        Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" 
                            RelativePanel.AlignBottomWithPanel="True" 
                            RelativePanel.AlignRightWithPanel="True" 
                            RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" 
                               TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  
                                      VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce 
                                 the status update. -->
                            <TextBlock 
                                x:Name="StatusBlock" FontWeight="Bold" 
                                AutomationProperties.LiveSetting="Assertive"
                                MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" 
                                Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

Vyn Design uppdateras för att Visa programmets användar gränssnitt.

### <a name="c-code-behind-source"></a>C#Källkod-bakom källa

Sedan lägger du till källa för bakomliggande kod så att programmet fungerar som förväntat. Källan för bakomliggande kod innehåller:

- `using`instruktioner för `Speech` namn områdena `Speech.Dialog` och
- En enkel implementering för att säkerställa mikrofon åtkomst, kabelansluten till en knapp hanterare
- Grundläggande användar gränssnitt hjälper dig att presentera meddelanden och fel i programmet
- En landnings punkt för den initierings kod Sök väg som kommer att fyllas i senare
- En hjälpare som kan spela upp text till tal (utan strömnings stöd)
- En tom knapp hanterare för att starta lyssningen som kommer att fyllas i senare

Följ dessa steg om du vill lägga till koden bakom källan:

1. Öppnakäll filen `MainPage.xaml.cs`bakomliggande kod i Solution Explorer. (Det är grupperat `MainPage.xaml`under.)

1. Ersätt filens innehåll med följande kodfragment:

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
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = 
                        new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = 
                        Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(
                        new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(
                string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(
                        Windows.UI.Core.CoreDispatcherPriority.Normal, 
                        () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) 
                    ? strMessage : "\n" + strMessage;

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
                // Raise an event if necessary to enable a screen reader 
                // to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(
                        Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for and accumulates all audio associated with a given 
            // PullAudioOutputStream and then plays it to the MediaElement. Long spoken 
            // audio will create extra latency and a streaming playback solution 
            // (that plays audio while it continues to be received) should be used -- 
            // see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(
                PullAudioOutputStream activityAudio)
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

                var task = Dispatcher.RunAsync(
                    Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(
                        playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. Lägg till följande kodfragment i metod texten i `InitializeDialogServiceConnector`. Den här koden skapar `DialogServiceConnector` med din prenumerations information.

    ```csharp
    // create a DialogServiceConfig by providing a bot secret key 
    // and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); 
    // note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret"; // Your channel secret
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key

    // Your subscription service region. 
    // Note: only a subset of regions are currently supported
    const string region = "YourServiceRegion"; 

    var botConfig = DialogServiceConfig.FromBotSecret(
        channelSecret, speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > Direkt linje tal (för hands version) är för närvarande tillgängligt i en delmängd av tal Services-regioner. Se [listan över regioner som stöds för röst-första virtuella assistenter](regions.md#voice-first-virtual-assistants) och se till att dina resurser distribueras i någon av dessa regioner.

   > [!NOTE]
   > Information om hur du konfigurerar roboten och hämtar en kanal hemlighet finns i dokumentationen till bot Framework för [den direkta rad igenkännings kanalen](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

1. Ersätt strängarna `YourChannelSecret`, `YourSpeechSubscriptionKey`och `YourServiceRegion` med dina egna värden för din robot, tal prenumeration och [region](regions.md).

1. Lägg till följande kodfragment i slutet av metod texten i `InitializeDialogServiceConnector`. Den här koden konfigurerar hanterare för händelser som förlitar sig `DialogServiceConnector` på att kommunicera med sina robot aktiviteter, tal igenkännings resultat och annan information.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
    connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser(
            $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser(
                $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };

    // Recognizing (not 'Recognized') will provide the intermediate recognized text 
    // while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };

    // Recognized (not 'Recognizing') will provide the final recognized text 
    // once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };

    // SessionStopped will notify when a turn is complete and 
    // it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. Lägg till följande kodfragment i bröd texten `ListenButton_ButtonClicked` i-metoden `MainPage` i-klassen. Den här koden ställs `DialogServiceConnector` in för att lyssna, eftersom du redan har upprättat konfigurationen och registrerat händelse hanterare.

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```

1. Välj **Arkiv** > **Spara alla** på Meny raden för att spara ändringarna.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

Nu är du redo att bygga och testa ditt program.

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**). Fönstret **HelloWorld** visas.

   ![Exempel på UWP Virtual Assistant- C# program i-snabb start](media/sdk/qs-virtual-assistant-uwp-helloworld-window.png)

1. Välj **aktivera mikrofon**och när åtkomst behörighets förfrågan öppnas, Välj **Ja**.

   ![Åtkomst behörighets förfrågan för mikrofon](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Välj **prata med din robot**och tala om en engelsk fras eller mening i enhetens mikrofon. Ditt tal överförs till den direkta linjens tal kanal och skrivs till text, som visas i fönstret.
<!--
    ![Successful bot response](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png)
-->
## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och distribuera en grundläggande robot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Se också

- [Om röst-första virtuella assistenter](voice-first-virtual-assistants.md)
- [Hämta en prenumerations nyckel för tal tjänster kostnads fritt](get-started.md)
- [Anpassade Väcknings ord](speech-devices-sdk-create-kws.md)
- [Anslut direkt linje tal till din robot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)
