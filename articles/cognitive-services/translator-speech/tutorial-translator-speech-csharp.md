---
title: 'Självstudier: Translator Speech API C#'
titleSuffix: Azure Cognitive Services
description: Använda Translator Speech API för att översätta text i realtid.
services: cognitive-services
author: v-jerkin
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: v-jerkin
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a3ed13cfe764c4f94dfa50fd096cfc7a8ac7656d
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673759"
---
# <a name="tutorial-translator-speech-application-in-c"></a>Självstudier: Translator Speech-program i C#

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Den här självstudien är en genomgång av ett interaktivt talöversättningsverktyg som använder Translator Speech API, som är en del av Azure Cognitive Services. Du lär dig hur du:

> [!div class="checklist"]
> * Begär en lista över språk som stöds av tjänsten
> * Spelar in ett ljud och överför det till tjänsten
> * Ta emot och visar översättningar av talet som text
> * Om du vill kan du spela upp en talad version (text till tal) av översättningen

En Visual Studio-lösningsfil för det här programmet [finns på GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här kursen behöver du valfri utgåva av Visual Studio 2017 (detta innefattar även Community Edition).

Visual Studio-lösningen skapar också ett installationsprogram för programmet. Du behöver [WiX Toolset](http://wixtoolset.org/) och [WiX Toolset Visual Studio Extension](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) för att kunna använda den här funktionen.

Du behöver också en prenumerationsnyckel för Translator Speech-tjänsten. Du kan hämta den via Microsoft Azure-instrumentpanelen. Det finns en kostnadsfri prisnivå. Med den kan du översätta upp till 10 timmars tal per månad utan kostnad. Den nivån räcker för den här kursen.

[JSON.Net-biblioteket](https://www.newtonsoft.com/json) (från Newtonsoft) krävs också. Den här sammansättningen installeras automatiskt av NuGet om båda kryssrutorna för Package Restore (paketåterställning) är markerade i Visual Studio-alternativen.

## <a name="trying-the-translation-app"></a>Prova översättningsappen

När du har öppnat Speech Translator (`SpeechTranslator.sln`) i Visual Studio trycker du på F5 för att bygga och starta programmet.  Programmets huvudfönster visas.

![[Huvudfönstret i Speech Translator]](media/speech-translator-main-window.png)

Den första gången du kör programmet väljer du **Account Settings** (Kontoinställningar) på menyn **Settings** (Inställningar) för att öppna fönstret som visas här.

![[Huvudfönstret i Speech Translator]](media/speech-translator-settings-window.png)

Klistra in prenumerationsnyckeln för Translator Speech i det här fönstret och klicka sedan på **Spara.** Nyckeln sparas mellan körningar.

Tillbaka i huvudfönstret väljer du enhet för ljudinspelning och ljuduppspelning samt vilka språk du vill översätta från och till. För att kunna höra ljudet från översättningen måste alternativet **TTS** (text till tal) vara markerat. Om du vill se spekulativa partiella översättningar medan du pratar kan du aktivera **Partial Results** (partiellt resultat).

Klicka slutligen på **Start** att påbörja översättningen. Säg något som du vill få översatt. Därmed visas texten som identifierats och översättningen i fönstret. Om du har aktiverat TTS kan du också höra översättningen.

## <a name="obtaining-supported-languages"></a>Hämta språk som stöds

I skrivande stund stöder tjänsten Translator Speech textöversättning till mer än femtio språk. Ett mindre antal språk stöds för talöversättning. För dessa språk krävs stöd för både transkription (taligenkänning) och syntes för text till tal.

För talöversättning måste alltså källspråket ha stöd för transkription. Utdataspråket kan vara något av de språk som har stöd för textöversättning, förutsatt att du vill ha ett textresultat. Om du vill ha talutdata kan du endast översätta till ett språk som kan användas för text till tal.

Microsoft lägger då och då till stöd för nya språk. Därför bör du inte hårdkoda information om vilka språk som stöds i ditt program. I stället har Translator Speech API:et en språkslutpunkt där du kan hämta information om vilka språk som stöds under körning. Du kan välja om du vill få en eller flera listor med språk:

| | |
|-|-|
|`speech`|De språk som du kan använda taltranskription på. Kan vara källspråk för talöversättning.|
|`text`|Språk som stöder text-till-text-översättning. Kan vara målspråk för talöversättning vid användning av textutdata.|
|`tts`|Rösterna som används för talsyntes. Varje röst är associerad med ett visst språk. Kan vara målspråk för talöversättning vid användning av text-till-tal. Ett språk kan ha mer än en röst.|

Det krävs inte någon prenumerationsnyckel för språkslutpunkten och användningen räknas inte mot din kvot. URI:n är `https://dev.microsofttranslator.com/languages` och resultatet returneras i JSON-format.

Metoden `UpdateLanguageSettingsAsync()` i `MainWindow.xaml.cs` visas här. Språkslutpunkten anropas för att hämta listan över språk som stöds.

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

Med den här metoden skapas först en HTTP-begäran till språkslutpunkten för att begära alla tre listor med språk (`text`, `speech` och `tts`).

Språkslutpunkten använder huvudet `Accept-Languages` i begäran för att bestämma på vilket språk språknamnen ska visas. Vi säger till exempel ”Tyska” på svenska medan det heter ”Deutsch” på tyska och ”Alemán” på spanska. Dessa skillnader återspeglas i listan med språk. Standardspråket för systemet används för det här huvudet.

När begäran har skickats och JSON-svaret tas emot parsas svaret till interna datastrukturer. Dessa strukturer används sedan för att bygga menyerna för Från-språk och Till-språk.

Eftersom vilka röster som är tillgängliga beror på vilket Till-språk som användaren väljer, går det inte att konfigurera röstmenyn ännu. I stället lagras rösterna för varje språk för senare användning. Hanteraren `ToLanguage_SelectionChanged` (i samma källfil) uppdaterar senare röstmenyn genom att anropa `UpdateVoiceComboBox()`  när användaren väljer ett Till-språk.

För skojs skull väljs ett Till-språk slumpmässigt om användaren inte har kört programmet tidigare. (Menyinställningarna sparas mellan sessioner.)

## <a name="authenticating-requests"></a>Autentisera förfrågningar

För att kunna autentisera mot Microsoft Translator Speech-tjänsten måste du skicka din Azure-prenumerationsnyckel i huvudet som värde för `Ocp-Apim-Subscription-Key` i anslutningsbegäran.

## <a name="translation-overview"></a>Översättningsöversikt

Översättnings-API:et (WebSockets-slutpunkten `wss://dev.microsofttranslator.com/speech/translate`) stöder översättning av ljud i monofoniskt, 16 kHz, 16-bitars signerat WAVE-format. Tjänsten returnerar ett eller flera JSON-svar som innehåller både den identifierade och den översatta texten. Om text till tal har begärts skickas en ljudfil.

Användaren väljer den ljudkälla som använder mikrofon/filingångsmenyn. Ljudet kan komma från en ljudenhet (till exempel en mikrofon) eller från en `.WAV`-fil.

Metoden `StartListening_Click` anropas när användaren klickar på Start-knappen. Den här händelsehanteraren anropar i sin tur `Connect()` för att börja skicka ljud till API-slutpunkten. Metoden `Connect()` utför följande aktiviteter:


> [!div class="checklist"]
> * Hämtar användarinställningarna från huvudfönstret och verifierar dem
> * Initierar ljudindata och utdataströmmar
> * Anropar `ConnectAsync()` för att hantera det resterande arbetet

`ConnectAsync()` hanterar i sin tur följande:

> [!div class="checklist"]
> * Autentiserar med Azure-prenumerationsnyckeln i huvudet `Ocp-Apim-Subscription-Key`
> * Skapar en `SpeechClient`-instans (finns i `SpeechClient.cs`) för att kommunicera med tjänsten
> * Initierar instanserna `TextMessageDecoder` och `BinaryMessageDecoder` (se `SpeechResponseDecoder.cs`) för att hantera svar
> * Skickar ljud via instansen `SpeechClient` till Translator Speech-tjänsten
> * Tar emot och bearbetar resultatet av översättningen

`SpeechClient` har färre ansvarsområden:

> [!div class="checklist"]
> * Upprättar en WebSocket-anslutning till Translator Speech-tjänsten
> * Skickar ljuddata och tar emot svar via socketen

## <a name="a-closer-look"></a>En närmare titt

Nu bör du ha en tydligare bild av hur programmets olika delar fungerar tillsammans för att utföra en översättningsbegäran. Låt oss titta på lite kod och fokusera på de relevanta delarna.

Här är en partiell version av `Connect()` som visar hur du konfigurerar ljudströmmar:

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

En stor del av `Connect()` handlar om att skapa en `SpeechClientOptions`-instans (se `SpeechClientOptions.cs`) som ska innehålla alternativen för översättningen. Alternativen är till exempel den information som behövs för att ansluta till tjänsten (till exempel autentiseringsnyckeln och värdnamnet) och funktionerna som används för översättningen. De här fälten mappar till huvudfälten och HTTP-parametrarna som exponeras av [Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

`Connect()` skapar även och initierar den enhet för ljudinspelning (variabeln `sampleProvider`) som fungerar som källa för talet som ska översättas. Den här enheten kan vara en maskinvaruenhet, till exempel en mikrofon, eller en fil som innehåller WAVE-ljuddata.

Här är `ConnectAsync()`-metoden som instansierar klassen `speechClient` och kopplar anonyma funktioner för att hantera svar (binära data eller textdata) från tjänsten.

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

Efter autentiseringen skapar metoden `SpeechClient`-instansen. Klassen `SpeechClient` (i `SpeechClient.cs`) anropar händelsehanterare vid mottagning av binära data och textdata. Ytterligare hanterare anropas om anslutningen misslyckas eller kopplas från.

Binära data är ljud (text till tal-utdata) som skickas av tjänsten när TTS är aktiverat. Textdata är antingen en partiell eller en fullständig översättning av den talade texten. Efter instansieringen kopplar alltså metoden upp funktioner för att hantera dessa meddelanden: ljud sparas för senare uppspelning, och text visas i fönstret.

## <a name="next-steps"></a>Nästa steg

Det här kodexemplet är ett funktionsrikt program som visar hur du använder Translator Speech API:et. Det finns därför ganska många delar att lära sig. Vi har gått igenom de viktigaste delarna. Du kan lära dig resten genom att lägga in ett par brytpunkter i Visual Studio och gå igenom översättningsprocessen. När du förstår exempelprogrammet har du de kunskaper du behöver för att använda Translator Speech-tjänsten i dina egna program.

> [!div class="nextstepaction"]
> [Referens för Microsoft Translator Speech API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
