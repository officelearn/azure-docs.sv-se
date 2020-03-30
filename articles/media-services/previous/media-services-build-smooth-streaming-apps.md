---
title: Smidig Streaming Windows Store App Tutorial | Microsoft-dokument
description: Lär dig hur du använder Azure Media Services för att skapa ett C# Windows Store-program med en XML MediaElement-kontroll för att spela upp Smooth Stream-innehåll.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 9ff961638aa170948d51793a21e86d18dd7e1d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016795"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Så här skapar du ett smidigt Strömningsprogram för Windows Store  

Smooth Streaming Client SDK för Windows 8 gör det möjligt för utvecklare att skapa Windows Store-program som kan spela upp innehåll på begäran och direktstämda strömning. Förutom den grundläggande uppspelningen av Smooth Streaming-innehåll tillhandahåller SDK också omfattande funktioner som Microsoft PlayReady-skydd, kvalitetsnivåbegränsning, Live DVR, ljudströmsväxling, lyssning av statusuppdateringar (t.ex. kvalitetsnivåändringar) och felhändelser och så vidare. Mer information om de funktioner som stöds finns i [viktig information](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Mer information finns i [Player Framework för Windows 8](https://playerframework.codeplex.com/). 

Den här självstudien innehåller fyra lektioner:

1. Skapa ett grundläggande smooth streaming store-program
2. Lägga till ett skjutreglage för att styra medieförloppet
3. Välj utjämnade strömningsströmmar
4. Välj utjämnade strömningsspår

## <a name="prerequisites"></a>Krav
> [!NOTE]
> Windows Store-projekt version 8.1 och tidigare stöds inte i Visual Studio 2017.  Mer information finns i [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Visual Studio 2017 – målplattform och plattformskompatibilitet).

* Windows 8 32- eller 64-bitars.
* Visual Studio versioner 2012 till 2015.
* [Microsoft Smooth Streaming Client SDK för Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Den färdiga lösningen för varje lektion kan hämtas från MSDN Developer Code Samples (Code Gallery): 

* [Lektion 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - En enkel Windows 8 Smooth Streaming Media Player, 
* [Lektion 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - En enkel Windows 8 Smooth Streaming Media Player med en reglagets kontroll, 
* [Lektion 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - En Windows 8 Smooth Streaming Media Player med Stream Selection,  
* [Lektion 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - En Windows 8 Smooth Streaming Media Player med spårval.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lektion 1: Skapa ett grundläggande smooth streaming store-program

I den här lektionen skapar du ett Windows Store-program med en MediaElement-kontroll för att spela upp Smooth Stream-innehåll.  Programmet som körs ser ut som:

![Exempel på Ett smidigt strömmande Windows Store-program][PlayerApplication]

Mer information om hur du utvecklar Windows Store-program finns i [Utveckla fantastiska appar för Windows 8](https://msdn.microsoft.com/windows/apps/br229512.aspx). Den här lektionen innehåller följande procedurer:

1. Skapa ett Windows Store-projekt
2. Designa användargränssnittet (XAML)
3. Ändra koden bakom filen
4. Kompilera och testa programmet

### <a name="to-create-a-windows-store-project"></a>Så här skapar du ett Windows Store-projekt

1. Kör Visual Studio; versioner 2012 till 2015 stöds.
1. Klicka på **Nytt**på **ARKIV-menyn** och sedan på **Project**.
1. Skriv eller välj följande värden i dialogrutan Nytt projekt:

    | Namn | Värde |
    | --- | --- |
    | Mallgrupp |Installerade/mallar/Visual C#/Windows Store |
    | Mall |Tom app (XAML) |
    | Namn |SSPlayer (SSPlayer) |
    | Location |C:\SSTutorials |
    | Namn på lösning |SSPlayer (SSPlayer) |
    | Skapa katalog för lösning |(vald) |

1. Klicka på **OK**.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Så här lägger du till en referens till SDK-klienten För utjämnad strömning

1. Högerklicka på **SSPlayer**från Solution Explorer och klicka sedan på **Lägg till referens**.
1. Ange eller välj följande värden:

    | Namn | Värde |
    | --- | --- |
    | Referensgrupp |Windows/tillägg |
    | Referens |Välj Microsoft Smooth Streaming Client SDK för Windows 8 och Microsoft Visual C++ Runtime Package |

1. Klicka på **OK**. 

När du har lagt till referenserna måste du välja den riktade plattformen (x64 eller x86), lägga till referenser fungerar inte för någon CPU-plattformskonfiguration.  I lösningsutforskaren visas gult varningsmärke för dessa tillagda referenser.

### <a name="to-design-the-player-user-interface"></a>Så här utformar du spelarens användargränssnitt

1. Från Solution Explorer dubbelklickar du på **MainPage.xaml** för att öppna den i designvyn.
2. Leta upp ** &lt;taggarna För&gt; rutnät** och ** &lt;/Grid&gt; ** XAML och klistra in följande kod mellan de två taggarna:

   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   ```
   MediaElement-kontrollen används för att spela upp media. Skjutreglaget med namnet skjutreglageProgress används i nästa lektion för att styra medieförloppet.
3. Spara filen genom att trycka på **CTRL+S.**

ContentElement-kontrollen stöder inte smooth streaming-innehåll direkt. Om du vill aktivera stöd för jämn strömning måste du registrera byteströmshanteraren Smooth Streaming efter filnamnstillägg och MIME-typ.  För att registrera dig använder du metoden MediaExtensionManager.RegisterByteStreamHandler i namnområdet Windows.Media.

I den här XAML-filen associeras vissa händelsehanterare med kontrollerna.  Du måste definiera dessa händelsehanterare.

### <a name="to-modify-the-code-behind-file"></a>Så här ändrar du koden bakom filen

1. Högerklicka på **MainPage.xaml**från Solution Explorer och klicka sedan på **Visa kod**.
2. Lägg till följande med hjälp av uttrycket högst upp i filen:
   
        using Windows.Media;
3. Lägg till följande datamedlem i början av klassen **MainPage:**
   
         private MediaExtensionManager extensions = new MediaExtensionManager();
4. Lägg till följande **MainPage** två rader i slutet av Huvudsidekonstruktorn:
   
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
5. Klistra in följande kod i slutet av klassen **MainPage:**
   ```csharp
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion
   ```
   Den sliderProgress_PointerPressed händelsehanteraren definieras här.  Det finns fler verk att göra för att få det att fungera, som kommer att behandlas i nästa lektion i den här guiden.
6. Spara filen genom att trycka på **CTRL+S.**

Den färdiga koden bakom filen skall se ut så här:

![Codeview i Visual Studio av Smooth Streaming Windows Store-program][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>För att kompilera och testa programmet

1. Klicka på Configuration **Manager**på **BUILD-menyn** .
2. Ändra **active solution-plattformen** för att matcha din utvecklingsplattform.
3. Tryck på **F6** för att sammanställa projektet. 
4. Tryck på **F5** för att köra programmet.
5. Högst upp i programmet kan du antingen använda standardadressen för jämn direktuppspelning eller ange en annan. 
6. Klicka på **Ange källa**. Eftersom **Automatisk uppspelning** är aktiverat som standard ska mediet spelas upp automatiskt.  Du kan styra mediet med knapparna **Spela upp,** **pausa** och **stoppa.**  Du kan styra medievolymen med det lodräta skjutreglaget.  Det horisontella skjutreglaget för att kontrollera medieläget har dock ännu inte genomförts fullt ut. 

Du har slutfört lektionen1.  I den här lektionen använder du en MediaElement-kontroll för att spela upp utjämnat strömmande innehåll.  I nästa lektion lägger du till ett skjutreglage för att styra förloppet för innehållet För jämn strömning.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lektion 2: Lägga till ett skjutreglage för att styra medieförloppet

I lektion 1 skapade du ett Windows Store-program med en MediaElement XAML-kontroll för att spela upp jämnt direktuppspelningsmediainnehåll.  Det kommer några grundläggande mediefunktioner som start, stopp och paus.  I den här lektionen ska du lägga till en reglagelistkontroll i programmet.

I den här självstudien använder vi en timer för att uppdatera skjutreglagets position baserat på den aktuella positionen för MediaElement-kontrollen.  Skjutreglagets start- och sluttid måste också uppdateras vid liveinnehåll.  Detta kan hanteras bättre i den adaptiva källuppdateringshändelsen.

Mediekällor är objekt som genererar mediedata.  Källmatcharen tar en URL- eller byteström och skapar lämplig mediekälla för det innehållet.  Källmatcharen är standardsättet för programmen att skapa mediekällor. 

Den här lektionen innehåller följande procedurer:

1. Registrera hanteraren För jämn direktuppspelning 
2. Lägg till händelsehanterare på adaptiv källhanterare
3. Lägg till händelsehanterare på adaptiv källnivå
4. Lägga till MediaElement-händelsehanterare
5. Lägga till reglagestrecksrelaterad kod
6. Kompilera och testa programmet

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>Så här registrerar du byteströmshanteraren Smooth Streaming och skickar egenskapsuppsättningen

1. Högerklicka på **MainPage.xaml**från Solution Explorer och klicka sedan på **Visa kod**.
2. Lägg till följande i början av filen:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. Lägg till följande datamedlemmar i början av klassen MainPage:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. Lägg till följande kod i **Huvudsidekonstruktorn** efter **detta. Initiera komponenter();** och de registreringskodrader som skrivits i föregående lektion:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. I **MainPage-konstruktorn** ändrar du de två RegisterByteStreamHandler-metoderna för att lägga till de fram-parametrarna:

   ```csharp
         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
   ```
6. Spara filen genom att trycka på **CTRL+S.**

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>Så här lägger du till händelsehanterarens hanterare på nivå för adaptiv källa

1. Högerklicka på **MainPage.xaml**från Solution Explorer och klicka sedan på **Visa kod**.
2. Lägg till följande datamedlem i klassen **MainPage:**

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. Lägg till följande händelsehanterare i slutet av klassen **MainPage:**

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. Lägg till följande rad i slutet av **Huvudsidekonstruktorn** för att prenumerera på den öppna händelsen med adaptiv källa:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Spara filen genom att trycka på **CTRL+S.**

### <a name="to-add-adaptive-source-level-event-handlers"></a>Så här lägger du till händelsehanterare på adaptiv källnivå

1. Högerklicka på **MainPage.xaml**från Solution Explorer och klicka sedan på **Visa kod**.
2. Lägg till följande datamedlem i klassen **MainPage:**

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. Lägg till följande händelsehanterare i slutet av klassen **MainPage:**

   ```csharp
         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
   ```
4. Lägg till följande kod i slutet av metoden **mediaElement AdaptiveSourceOpened** för att prenumerera på händelserna:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Spara filen genom att trycka på **CTRL+S.**

Samma händelser är tillgängliga även på instrumenteringshanteringsnivå, som kan användas för att hantera funktioner som är gemensamma för alla medieelement i appen. Varje AdaptiveSource innehåller sina egna händelser och alla AdaptiveSource-händelser kommer att kaskad under AdaptiveSourceManager.

### <a name="to-add-media-element-event-handlers"></a>Så här lägger du till händelsehanterare för medieelement

1. Högerklicka på **MainPage.xaml**från Solution Explorer och klicka sedan på **Visa kod**.
2. Lägg till följande händelsehanterare i slutet av klassen **MainPage:**

   ```csharp
         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
   ```
3. Lägg till följande **MainPage** kod i nedsänkta händelser i slutet av Huvudsidekonstruktorn:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Spara filen genom att trycka på **CTRL+S.**

### <a name="to-add-slider-bar-related-code"></a>Så här lägger du till reglagestrecksrelaterad kod

1. Högerklicka på **MainPage.xaml**från Solution Explorer och klicka sedan på **Visa kod**.
2. Lägg till följande i början av filen:

   ```csharp
        using Windows.UI.Core;
   ```
3. Lägg till följande datamedlemmar i klassen **MainPage:**

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. Lägg till följande **MainPage** kod i slutet av Huvudsidekonstruktorn:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. Lägg till följande kod i slutet av klassen **MainPage:**

   ```csharp
         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
   ```

   > [!NOTE]
   > CoreDispatcher används för att göra ändringar i gränssnittstråden från icke-gränssnittstråd. Vid flaskhals på avsändare tråd, kan utvecklare välja att använda avsändaren som tillhandahålls av UI-element som de avser att uppdatera.  Ett exempel:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. Lägg till följande kod i slutet av **mediaElement_AdaptiveSourceStatusUpdated:At** the end of the mediaElement_AdaptiveSourceStatusUpdated method, add the following code:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. Lägg till följande kod i slutet av metoden **MediaOpened:**

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Spara filen genom att trycka på **CTRL+S.**

### <a name="to-compile-and-test-the-application"></a>För att kompilera och testa programmet

1. Tryck på **F6** för att sammanställa projektet. 
2. Tryck på **F5** för att köra programmet.
3. Högst upp i programmet kan du antingen använda standardadressen för jämn direktuppspelning eller ange en annan. 
4. Klicka på **Ange källa**. 
5. Testa skjutreglaget.

Du har slutfört lektion 2.  I den här lektionen har du lagt till ett skjutreglage i programmet. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lektion 3: Välj jämna strömningsströmmar
Smooth Streaming kan strömma innehåll med flera ljudspår på flera språk som kan väljas av tittarna.  I den här lektionen gör du det möjligt för tittarna att välja strömmar. Den här lektionen innehåller följande procedurer:

1. Ändra XAML-filen
2. Ändra koden bakom filen
3. Kompilera och testa programmet

### <a name="to-modify-the-xaml-file"></a>Så här ändrar du XAML-filen

1. Högerklicka på **MainPage.xaml**från Solution Explorer och klicka sedan på **Visa designer**.
2. Leta &lt;upp Grid.RowDefinitions&gt;och ändra RowDefinitions så att de ser ut så här:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. Lägg &lt;till&gt;&lt;följande&gt; kod i taggarna Grid /Grid för att definiera en listrutekontroll så att användarna kan se listan över tillgängliga strömmar och välja strömmar:

   ```xml
         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
   ```
4. Spara ändringarna genom att trycka på **CTRL+S.**

### <a name="to-modify-the-code-behind-file"></a>Så här ändrar du koden bakom filen

1. Högerklicka på **MainPage.xaml**från Solution Explorer och klicka sedan på **Visa kod**.
2. Lägg till en ny klass i SSPlayer-namnområdet:

   ```csharp
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
   ```
3. Lägg till följande variabeldefinitioner i början av klassen MainPage:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. Lägg till följande region i klassen MainPage:
   ```csharp
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the first video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the first audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
   ```
5. Leta reda på mediaElement_ManifestReady-metoden, lägg till följande kod i slutet av funktionen:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    Så när MediaElement-manifestet är klart får koden en lista över tillgängliga strömmar och fyller i listrutan för användargränssnittet med listan.
6. I klassen MainPage letar du upp gränssnittsknapparna på händelseområdet och lägger sedan till följande funktionsdefinition:
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```

### <a name="to-compile-and-test-the-application"></a>För att kompilera och testa programmet

1. Tryck på **F6** för att sammanställa projektet. 
2. Tryck på **F5** för att köra programmet.
3. Högst upp i programmet kan du antingen använda standardadressen för jämn direktuppspelning eller ange en annan. 
4. Klicka på **Ange källa**. 
5. Standardspråket är audio_eng. Försök att växla mellan audio_eng och audio_es. Varje gång du väljer en ny ström måste du klicka på knappen Skicka.

Du har slutfört lektion 3.  I den här lektionen lägger du till funktionerna för att välja strömmar.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lektion 4: Välj jämna strömmande spår

En jämn strömningspresentation kan innehålla flera videofiler som kodats med olika kvalitetsnivåer (bithastigheter) och upplösningar. I den här lektionen gör du det möjligt för användare att välja spår. Den här lektionen innehåller följande procedurer:

1. Ändra XAML-filen
2. Ändra koden bakom filen
3. Kompilera och testa programmet

### <a name="to-modify-the-xaml-file"></a>Så här ändrar du XAML-filen

1. Högerklicka på **MainPage.xaml**från Solution Explorer och klicka sedan på **Visa designer**.
2. Leta &lt;upp&gt; grid-taggen med namnet **gridStreamAndBitrateSelection**, lägg till följande kod i slutet av taggen:
   ```xml
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
   ```
3. Spara ändringarna genom att trycka på **CTRL+S**

### <a name="to-modify-the-code-behind-file"></a>Så här ändrar du koden bakom filen

1. Högerklicka på **MainPage.xaml**från Solution Explorer och klicka sedan på **Visa kod**.
2. Lägg till en ny klass i SSPlayer-namnområdet:
   ```csharp
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
   ```
3. Lägg till följande variabeldefinitioner i början av klassen MainPage:
   ```csharp
        private List<Track> availableTracks;
   ```
4. Lägg till följande region i klassen MainPage:
   ```csharp
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
   ```
5. Leta reda på mediaElement_ManifestReady-metoden, lägg till följande kod i slutet av funktionen:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. I klassen MainPage letar du upp gränssnittsknapparna på händelseområdet och lägger sedan till följande funktionsdefinition:
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
   
### <a name="to-compile-and-test-the-application"></a>För att kompilera och testa programmet

1. Tryck på **F6** för att sammanställa projektet. 
2. Tryck på **F5** för att köra programmet.
3. Högst upp i programmet kan du antingen använda standardadressen för jämn direktuppspelning eller ange en annan. 
4. Klicka på **Ange källa**. 
5. Som standard är alla spår i videoströmmen markerade. Om du vill experimentera bithastighetsändringarna kan du välja den lägsta bithastigheten som är tillgänglig och sedan välja den högsta bithastigheten som är tillgänglig. Du måste klicka på Skicka efter varje ändring.  Du kan se ändringar av videokvaliteten.

Du har slutfört lektion 4.  I den här lektionen lägger du till funktionen för att välja spår.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Övriga resurser:
* [Så här skapar du ett Smooth Streaming Windows 8 JavaScript-program med avancerade funktioner](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Teknisk översikt över smooth streaming](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png

