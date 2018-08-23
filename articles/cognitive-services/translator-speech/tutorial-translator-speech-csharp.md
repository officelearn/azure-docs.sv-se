---
title: Translator Speech självstudien (C#) | Microsoft Docs
titleSuffix: Cognitive Services
description: Lär dig hur du använder Translator speech-tjänsten för att översätta text i realtid.
services: cognitive-services
author: v-jerkin
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.devlang: csharp
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: 010ad8b5ceeaf046c8d361ff352e6058154a482d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "41987539"
---
# <a name="tutorial-microsoft-translator-wpf-application-in-c"></a>Självstudie: Microsoft Translator WPF-program i C#

Den här självstudien är en genomgång av en interaktiva speech translation-verktyg som använder tjänsten Microsoft Translator Speech translation, en del av Microsoft Cognitive Services i Azure. Lär dig att:

> [!div class="checklist"]
> * Begära en lista över de språk som stöds av tjänsten
> * Spela in ljud och överföra dem till tjänsten
> * Ta emot och visa översättningar av talet som text
> * Om du vill spela upp en talat (text-till-tal) version av översättningen

En Visual Studio-lösningsfil för det här programmet är [finns på GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien behöver du en utgåva av Visual Studio 2017, inklusive Community-utgåvan av. 

Visual Studio-lösningen skapar också ett installationsprogram för programmet. Du behöver den [WiX Toolset](http://wixtoolset.org/) och [WiX Toolset Visual Studio-tillägget](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) att stödja den här funktionen.

Du behöver också en prenumerationsnyckel för Translator Speech-tjänsten, som du kan hämta från Microsoft Azure-instrumentpanelen. En kostnadsfri prisnivå är tillgänglig som gör det möjligt att översätta upp till 10 timmar tal per månad utan kostnad. Den här nivån är tillräcklig för den här självstudien.

Tredje parts [JSON.Net biblioteket](https://www.newtonsoft.com/json) (från Newtonsoft) krävs också. Den här sammansättningen installeras automatiskt som NuGet om båda Paketåterställning kryssrutorna är aktiverade i Visual Studio-alternativ.

## <a name="trying-the-translation-app"></a>Provar translation-app

När du har öppnat Talöversättning för Microsoft-lösning (`SpeechTranslator.sln`) i Visual STudio trycker du på F5 för att skapa och starta programmet.  Programmets huvudsakliga fönster visas.

![[Tal Translator huvudfönstret]](media/speech-translator-main-window.png)

På den första körningen väljer **kontoinställningar** från den **inställningar** menyn för att öppna fönstret som visas här.

![[Tal Translator huvudfönstret]](media/speech-translator-settings-window.png)

Klistra in din prenumerationsnyckel för Microsoft Translator Speech i det här fönstret och klicka sedan på **spara.** Nyckeln sparas mellan körningar.

Tillbaka i huvudfönstret väljer du ljud indata och utdataenheter som du vill ska användas och från och till andra språk. Om du vill höra ljudet från översättningen kontrollerar den **text till tal** (text-till-tal) alternativet är markerat. Om du vill se spekulativ partiella översättningar som du talar, aktivera den **delresultat** alternativet.

Klicka slutligen på **starta** att börja översättning. Anta att något du vill ha översatta och titta på den tolkade texten och översättningen visas i fönstret. Om du har aktiverat alternativet text till tal kan hör du också översättningen.

## <a name="obtaining-supported-languages"></a>Hämta språk som stöds

När detta skrivs stöder tjänsten Microsoft Translator mer än fem dussin språk för textöversättning. Ett mindre antal språk stöds för talöversättning. Sådana språk kräver stöd för både taltranskription (Taligenkänning) och för text till tal-utdata syntes.

Med andra ord för talöversättning, måste källspråket vara något stöd för avskrift. Utdata-språket kan vara något av de språk som stöds för textöversättning, förutsatt att du vill att ett text-resultat. Om du vill tal-utdata, kan du endast översätta till ett språk som stöds för text till tal.

Microsoft kan lägga till stöd för nya språk från tid till annan. Därför bör du inte hårdkoda någon kunskap om språk som stöds i ditt program. I stället innehåller Translator Speech API en slutpunkt för språk som gör att du kan hämta språk som stöds vid körning. Du kan välja att få en eller flera listor över språk: 

| | |
|-|-|
|`speech`|De språk som stöds för taltranskription. Kan vara språk för talöversättning.|
|`text`|De språk som stöds för text-till-text. Kan vara mål språk för talöversättning när textutdata används.|
|`tts`|Röster som stöds för talsyntes, var och en som är associerade med ett visst språk. Kan vara mål språk för talöversättning när text till tal används. Ett visst språk kan stödjas av mer än en röst.|

Språk-slutpunkten kräver inte en prenumerationsnyckel och användningen räknas inte mot din kvot. Dess URI: N är `https://dev.microsofttranslator.com/languages` och resultaten returneras i JSON-format.

Metoden `UpdateLanguageSettingsAsync()` i `MainWindow.xaml.cs`visas här, anropar språk-slutpunkt för att hämta listan över språk som stöds. 

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

Den här metoden skapar först en HTTP-begäran till slutpunkten språk, begär alla tre listor över språk (`text`, `speech`, och `tts`).

Språk-slutpunkten använder begärandets `Accept-Languages` rubrik för att bestämma vilket språk som namnen på språk som representeras. Till exempel visar det språk som är kända för engelskspråkiga användare som ”tyska” kallas ”Deutsch” på tyska och ”Alemán” på spanska och listan över språk som dessa skillnader. Standardspråk för systemets används för den här rubriken.

När begäran har skickats och JSON-svar emot svaret parsas till interna datastrukturer. Dessa strukturer används sedan för att konstruera menyerna från språk och att språk. 

Eftersom röster som är tillgängliga beror på att språket som valts av användaren, är det inte går att ställa in röst-menyn ännu. I stället lagras rösterna för varje språk för senare användning. Den `ToLanguage_SelectionChanged` hanterare (i samma källfil) senare uppdaterar Voice-menyn genom att anropa `UpdateVoiceComboBox()` när användaren väljer ett till språk. 

För skojs skull väljs slumpmässigt ett till språk som användaren inte har kört programmet före. (Menyn inställningar lagras mellan sessioner.)

## <a name="authenticating-requests"></a>Autentisera förfrågningar

Att autentisera till tjänsten Microsoft Translator Speech du behöver skicka din nyckel för Azure-prenumeration i rubriken som värde för `Ocp-Apim-Subscription-Key` i begäran.

## <a name="translation-overview"></a>Översikt över översättning

Översätt-API (WebSockets endpoint `wss://dev.microsofttranslator.com/speech/translate`) accepterar ljud översättas i monophonic, 16 kHz, 16-bitars signerade WAVE-format. Tjänsten returnerar en eller flera JSON-svaren som innehåller både välkända och översatta texten. Om text till tal har begärts, skickas en ljudfil.

Användaren väljer den ljudkälla med hjälp av menyn mikrofon/fil som indata. Ljudet kan komma från en ljudenhet (till exempel en mikrofon) eller från en `.WAV` fil.

Metoden `StartListening_Click` anropas när användaren klickar på Start-knappen. Den här händelsehanteraren i sin tur anropar `Connect()` att starta processen för att skicka ljud service API-slutpunkten. Den `Connect()` metoden utför följande uppgifter:


> [!div class="checklist"]
> * Hämta användarinställningar i huvudfönstret och validera dem.
> * Initierar ljudindata och utdataströmmar
> * Anropa `ConnectAsync()` till hand om resten av arbetet

`ConnectAsync()`, i sin tur hanterar sysslor som följande:

> [!div class="checklist"]
> * Autentisering med Azure-prenumeration nyckeln i rubriken `Ocp-Apim-Subscription-Key`
> * Skapa en `SpeechClient` instans (finns i `SpeechClient.cs`) att kommunicera med tjänsten
> * Initierar `TextMessageDecoder` och `BinaryMessageDecoder` instanser (se `SpeechResponseDecoder.cs`) att hantera svar
> * Skicka ljud via den `SpeechClient` instans till Translator Speech-tjänsten
> * Ta emot och bearbeta resultaten av översättningen

Ansvaret för `SpeechClient` är färre:

> [!div class="checklist"]
> * Upprätta en WebSocket-anslutning till Translator Speech-tjänsten
> * Skicka ljuddata och ta emot svar via socket

## <a name="a-closer-look"></a>En närmare titt

Det bör vara tydligare nu hur delar av programmet fungerar tillsammans för att utföra begäran om översättning. Låt oss ta en titt på kod, fokusera på de relevanta delarna.

Här är en partiell version av `Connect()` som visas i ljudströmmar inställningen:

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };
    
    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

En betydande del av `Connect()` omfattar att skapa en `SpeechClientOptions` instans (se `SpeechClientOptions.cs`) för alternativ för översättning. Till exempel information som behövs för att ansluta till tjänsten (till exempel autentiseringsnyckeln och värdnamn) och de funktioner som används för översättningen. De här fälten mappas till huvudfält-HTTP-parametrarna exponerade av [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

`Connect()` skapar även och initierar enheten för ljudinspelning (variabeln `sampleProvider`) som fungerar som källa för tal till att översätta. Den här enheten är antingen en inkommande maskinvaruenhet som en mikrofon eller en fil som innehåller WAVE ljuddata.

Här är den `ConnectAsync()` metod som skapar en instans av den `speechClient` klass och krokar in anonyma funktioner för hantering av text och binära svar från tjänsten.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);
    
    TextMessageDecoder textDecoder;
    
    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);
    
    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected. 
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

När de har autentiserat, metoden skapar den `SpeechClient` instans. Den `SpeechClient` klass (i `SpeechClient.cs`) anropar händelsehanterare vid mottagande binär data och textdata. Ytterligare hanterare anropas när anslutningen misslyckas eller kopplas från.

Binära data är ljud (text till tal utdata) som skickas av tjänsten när text till tal är aktiverad. Textdata är antingen en partiell eller en fullständig översättning av talade texten. Så efter att, metoden skapar in funktioner för att hantera dessa meddelanden: ljud genom att lagra den för senare uppspelning och text genom att visa i fönstret.

## <a name="next-steps"></a>Nästa steg

Det här kodexemplet är en funktionsrik-program som beskriver användning av Translator Speech API. Det finns därför ett verkligt antal rörliga delar att förstå. Du har gått igenom de viktigaste delarna. För rest, kan det vara nyttigt att ange några brytpunkter i Visual Studio och gå igenom process för översättning. När du förstår exempelprogrammet, är du utrustade för att använda tjänsten Talöversättning i dina egna program.

> [!div class="nextstepaction"]
> [Microsoft Translator Speech API-referens](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
