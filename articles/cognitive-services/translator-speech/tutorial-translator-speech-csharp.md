---
title: Översättare tal kursen (C#) | Microsoft Docs
titleSuffix: Cognitive Services
description: Lär dig hur du använder tjänsten översättare tal för att översätta text i realtid.
services: cognitive-services
author: v-jerkin
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.devlang: csharp
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jerkin
ms.openlocfilehash: e82c5c5ccfa6b7de8a9ec111140dad1a40ad44f6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352287"
---
# <a name="tutorial-microsoft-translator-wpf-application-in-c"></a>Självstudier: Microsoft Translator WPF-program i C#

Den här kursen är en genomgång av en interaktiv tal översättning verktyg som använder tjänsten Microsoft översättare Speech översättning, en del av kognitiva Microsoft-tjänster i Azure. Lär dig att:

> [!div class="checklist"]
> * Begära en lista över de språk som stöds av tjänsten
> * Kopiera ljud och överför den till tjänsten
> * Ta emot och visa översättningar av tal som text
> * Du kan också spela en talade (text till tal-) version översättningens

En fil med Visual Studio-lösning för det här programmet är [finns på GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Förutsättningar

För den här kursen behöver du en utgåva av Visual Studio-2017, inklusive Community Edition. 

Visual Studio-lösning skapar också ett installationsprogram för programmet. Du behöver den [WiX Toolset](http://wixtoolset.org/) och [WiX Toolset Visual Studio-tillägget](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) att stödja den här funktionen.

Du måste också en prenumeration nyckel för tjänsten översättare tal som du kan hämta från Microsoft Azure-instrumentpanelen. Kostnadsfria prisnivån är tillgänglig som gör det möjligt att översätta upp till 10 timmar tal per månad utan kostnad. Det här skiktet är tillräcklig för den här kursen.

Tredjeparts- [JSON.Net biblioteket](https://www.newtonsoft.com/json) (från Newtonsoft) krävs också. Den här sammansättningen installeras automatiskt av NuGet om båda Paketåterställning kryssrutorna är aktiverade i Visual Studio-alternativ.

## <a name="trying-the-translation-app"></a>Försök översättning appen

När du har öppnat Microsoft tal Translator-lösning (`SpeechTranslator.sln`) i Visual STudio trycker du på F5 för att skapa och starta programmet.  Programmets huvudsakliga fönster visas.

![[Tal översättare huvudfönstret]](media/speech-translator-main-window.png)

Första gången du kör, Välj **kontoinställningar** från den **inställningar** menyn för att öppna fönstret som visas här.

![[Tal översättare huvudfönstret]](media/speech-translator-settings-window.png)

Klistra in Microsoft översättare Speech prenumeration nyckeln i det här fönstret och klicka sedan på **spara.** Nyckeln sparas mellan körs.

Välj tillbaka i huvudfönstret ljudinsignal och utdataenheter som du vill använda och från och till andra språk. Om du vill höra ljud översättningens kontrollerar du att den **TTS** (text-till-tal) alternativet är markerat. Om du vill se spekulativ partiella översättningar som du talar, aktivera den **ofullständiga resultat** alternativet.

Klicka slutligen på **starta** ska börja översättning. Anta att något du vill ha översättas och titta på den tolkade texten och översättningen visas i fönstret. Om du har aktiverat alternativet TTS höra du även översättningen.

## <a name="obtaining-supported-languages"></a>Hämta de språk som stöds

När detta skrivs stöder tjänsten Microsoft Translator mer än fem 12 språk för textöversättning. Ett mindre antal språk stöds för översättning för tal. Dessa språk kräver stöd för både skrivfel (Taligenkänning) och för sammanfattande-text till tal-utdata.

Med andra ord för taligenkänning översättning måste källa språket vara en stöds för skrivfel. Utdata-språket kan vara någon av de språk som stöds för textöversättning, förutsatt att du vill använda ett text-resultat. Om du vill tal-utdata, kan du endast översätta till ett språk som stöds för text till tal.

Microsoft kan lägga till stöd för nya språk då. Därför bör du inte hårdkoda kännedom om språk som stöds i ditt program. I stället översättare tal-API som tillhandahåller en slutpunkt för språk som gör att du kan hämta språk som stöds vid körning. Du kan välja att ta emot en eller flera listor språk: 

| | |
|-|-|
|`speech`|De språk som stöds för tal skrivfel. Kan vara källan språk för översättning för tal.|
|`text`|De språk som stöds för översättning till text. Kan vara mål språk för taligenkänning översättning när du använder text-utdata.|
|`tts`|Röster som stöds för tal sammanfattande, var och en associerad med ett visst språk. Kan vara mål språk för taligenkänning översättning när text till tal används. Ett visst språk kan stödjas av mer än en röst.|

Språk slutpunkten kräver inte en nyckel för prenumeration och dess användning räknas inte mot din kvot. URI är `https://dev.microsofttranslator.com/languages` och returnerar resultaten i JSON-format.

Metoden `UpdateLanguageSettingsAsync()` i `MainWindow.xaml.cs`, visas här, anropar språk slutpunkten för att hämta en lista över språk som stöds. 

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

Språk-slutpunkten använder begäran `Accept-Languages` huvudet för att avgöra vilket språk som namnen på språk som representeras. Till exempel visar det språk som är kända för engelskspråkiga användare som ”tyska” kallas ”tyska” i tyska och ”Alemán” på spanska och en lista med språk skillnaderna. Systemets standardspråk används för det här sidhuvudet.

När begäran har skickats och de JSON-svar togs emot, svaret parsas till interna datastrukturer. Dessa strukturer används sedan för att konstruera menyerna från språk och till språk. 

Eftersom de tillgängliga rösterna beror på att språk som valts av användaren, det går inte att ställa in menyn röst ännu. I stället lagras rösterna för varje språk för senare användning. Den `ToLanguage_SelectionChanged` hanterare (i samma källfil) senare uppdaterar röst-menyn genom att anropa `UpdateVoiceComboBox()` när användaren väljer att språk. 

För skojs skull väljs slumpmässigt till språk som användaren inte har kört programmet innan. (Menyn inställningar lagras mellan sessioner.)

## <a name="authenticating-requests"></a>Autentisera förfrågningar

Att autentisera till tjänsten Microsoft översättare tal som du måste skicka din nyckel för Azure-prenumeration i huvudet som värde för `Ocp-Apim-Subscription-Key` i anslutningsbegäran.

## <a name="translation-overview"></a>Översättning: översikt

Översätta-API (WebSockets endpoint `wss://dev.microsofttranslator.com/speech/translate`) accepterar ljud översättas i monophonic, 16 kHz, 16-bitars signerade WAVE-format. Tjänsten returnerar en eller flera JSON-svar som innehåller både tolkade och översatt text. Om text till tal har begärts, skickas en ljudfil.

Användaren väljer den ljud datakälla med hjälp av menyn mikrofon/filen indata. Ljuduppspelningen kan komma från en ljudenhet (till exempel en mikrofon) eller från en `.WAV` fil.

Metoden `StartListening_Click` anropas när användaren klickar på Start. Den här händelsehanteraren anropar i sin tur `Connect()` att starta processen med att skicka ljud till API-tjänsteslutpunkt. Den `Connect()` metoden utför följande uppgifter:


> [!div class="checklist"]
> * Hämtning av användarinställningar från huvudfönstret och validera dem.
> * Initierar ljud indata- och utdataströmmar
> * Anropar `ConnectAsync()` sköta resten av arbetet

`ConnectAsync()`, i sin tur hanterar följande rutiner:

> [!div class="checklist"]
> * Autentisera med Azure-prenumeration nyckel i huvudet `Ocp-Apim-Subscription-Key`
> * Skapa en `SpeechClient` instansen (finns i `SpeechClient.cs`) kan kommunicera med tjänsten
> * Initierar `TextMessageDecoder` och `BinaryMessageDecoder` instanser (se `SpeechResponseDecoder.cs`) ska hantera svar
> * Skicka ljud via den `SpeechClient` instans till tjänsten översättare tal
> * Ta emot och bearbeta resultaten av översättningen

Ansvaret för `SpeechClient` är färre:

> [!div class="checklist"]
> * Upprätta en WebSocket-anslutning till tjänsten översättare tal
> * Skicka ljuddata och ta emot svar via socket

## <a name="a-closer-look"></a>En närmare titt

Det bör vara tydligare nu hur delar av programmet arbetar tillsammans för att utföra begäran om översättning. Låt oss ta en titt på kod, fokuserar på de relevanta delarna.

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

En betydande del av `Connect()` skapa en `SpeechClientOptions` instans (se `SpeechClientOptions.cs`) ska innehålla alternativ för översättning. Till exempel information som behövs för att ansluta till tjänsten (till exempel autentiseringsnyckeln och värdnamn) och funktionerna för översättning. De här fälten mappas till huvudfält och HTTP-parametrar som exponeras av [översättare Speech API](http://docs.microsofttranslator.com/speech-translate.html).

`Connect()` Dessutom skapar och initierar enhet för ljudinspelning (variabeln `sampleProvider`) som fungerar som källa för tal översättas. Den här enheten är antingen en inkommande maskinvaruenhet, till exempel en mikrofon eller en fil som innehåller WAVE ljuddata.

Här är den `ConnectAsync()` metod som instantierar den `speechClient` klass och hook in anonyma funktioner för att hantera text och binära svar från tjänsten.

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

Efter autentisering, metoden skapar den `SpeechClient` instans. Den `SpeechClient` klass (i `SpeechClient.cs`) anropar händelsehanterare på data för binära och text. Ytterligare hanterare anropas när anslutningen misslyckas eller kopplas från.

Binära data är ljud (text till tal-utdata) som skickas av tjänsten när TTS är aktiverad. Textdata är en partiell eller fullständig översättning av talade texten. Så efter att, metoden skapar in funktioner för att hantera dessa meddelanden: ljud genom att lagra det senare spelas upp och text som visas i fönstret.

## <a name="next-steps"></a>Nästa steg

Det här kodexemplet är ett program för funktioner som beskriver användning av översättare tal-API. Det finns därför ett verkligt antal rörliga delar att förstå. Du har gått igenom de viktigaste bitarna. För övriga, kan det vara nyttigt att ange några brytpunkter i Visual Studio och går igenom processen översättning. När du förstår exempelprogrammet använder du utrustade att använda tjänsten översättare tal i dina program.

> [!div class="nextstepaction"]
> [Microsoft översättare tal-API-referens](http://docs.microsofttranslator.com/speech-translate.html)
