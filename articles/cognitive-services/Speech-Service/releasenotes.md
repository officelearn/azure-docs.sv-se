---
title: Viktig information - Taltjänst
titleSuffix: Azure Cognitive Services
description: En logg som körs med speech service-funktionen släpper, förbättringar, buggfixar och kända problem.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 7bab0a28ba2b75903b6bdf4708e6aa0a98bdc9e5
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607408"
---
# <a name="release-notes"></a>Viktig information
## <a name="speech-sdk-1110-2020-march-release"></a>Tal SDK 1.11.0: 2020-mars release

**Nya funktioner**

- Linux: Lagt till stöd för Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 med instruktioner om hur du [konfigurerar](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7) systemet för Tal SDK.
- Linux: Lagt till stöd för .NET Core C# på Linux ARM32 och ARM64. Läs mer [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux). 
- C#, C++: `UtteranceId` `ConversationTranscriptionResult`Tillagt i , ett konsekvent ID över alla intermediärer och slutligt taligenkänningsresultat. Detaljer för [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: Lagt `Language ID`till stöd för . Se speech_sample.py i [GitHub repo](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- Windows: Lade till stöd för komprimerat ljudinmatningsformat på Windows-plattformen för alla win32-konsolprogram. Detaljer [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams). 
- JavaScript: Stöd för talsyntes (text-till-tal) i NodeJS. Läs mer [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript: Lägg till nya API:er för att aktivera inspektion av alla skicka och mottagna meddelanden. Läs mer [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Felkorrigeringar**

- C#, C++: Åtgärdade `SendMessageAsync` ett problem så skickar nu binära meddelanden som binär text. Detaljer för [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection).
- C#, C++: Åtgärdade `Connection MessageReceived` ett problem där `Recognizer` användning av `Connection` händelse kan orsaka krasch om det tas bort före objektet. Detaljer för [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived).
- Android: Ljudbuffertstorlek från mikrofon minskade från 800ms till 100ms för att förbättra svarstiden.
- Android: Fixade ett [problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) med x86 Android-emulatorn i Android Studio.
- JavaScript: Lagt till stöd för `fromSubscription` regioner i Kina med API: et. Detaljer [här](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#fromsubscription-string--string-). 
- JavaScript: Lägg till mer felinformation för anslutningsfel från NodeJS.
        
**Exempel**

- Enhet: Offentligt exempel på avsiktsigenkänning har åtgärdats, där LUIS json-importen misslyckades. Detaljer [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369).
- Python: Exempel `Language ID`tillagt för . Detaljer [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).
    
**Covid19 förkortad testning**

På grund av att arbeta på distans under de senaste veckorna kunde vi inte göra så mycket manuell enhetskontroll testning som vi normalt gör. Ett exempel på detta är att testa mikrofoningång och högtalarutgång på Linux, iOS och macOS. Vi har inte gjort några ändringar som vi tror kunde ha brutit något på dessa plattformar, och våra automatiserade tester alla passerade. I den osannolika händelsen att vi missade något, vänligen meddela oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen).<br> Tack för ert fortsatta stöd. Som alltid, skicka frågor eller feedback på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?page=2&q=is%3Aissue+is%3Aopen) eller [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Håll dig frisk!

## <a name="speech-sdk-1100-2020-february-release"></a>Tal SDK 1.10.0: 2020-februari release

**Nya funktioner**

 - Lade till Python-paket för att stödja den nya 3.8-versionen av Python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 support (C++, C#, Java, Python).
   > [!NOTE] 
   > Kunder måste konfigurera OpenSSL enligt [dessa instruktioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
 - Linux ARM32-stöd för Debian och Ubuntu.
 - DialogServiceConnector stöder nu en valfri "bot ID"-parameter på BotFrameworkConfig. Den här parametern tillåter användning av flera Direct Line Speech-robotar med en enda Azure-talresurs. Utan den angivna parametern används standardroboten (som bestäms av konfigurationssidan för direkt radtalskanal).
 - DialogServiceConnector har nu egenskapen SpeechActivityTemplate. Innehållet i den här JSON-strängen används av Direkt radtal för att förfylla en mängd olika fält som stöds i alla aktiviteter som når en direktlinjetalsrobot, inklusive aktiviteter som genereras automatiskt som svar på händelser som taligenkänning.
 - TTS använder nu prenumerationsnyckel för autentisering, vilket minskar den första byte latensen för det första syntesresultatet efter att ha skapat en synthesizer.
 - Uppdaterade taligenkänningsmodeller för 19 språk för en genomsnittlig ordfelsminskning på 18,6 % (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). De nya modellerna medför betydande förbättringar på flera domäner, inklusive diktering, Call Center Transkription och videoindexering scenarier.

**Felkorrigeringar**

 - Fast bugg där Konversation Transkriberare inte väntade ordentligt i JAVA API: er 
 - Android x86 emulator fix för Xamarin [GitHub fråga](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Lägg till saknas (Hämta| Ange)Egenskapsmetoder till AudioConfig
 - Åtgärda ett TTS-fel där audioDataStream inte kunde stoppas när anslutningen misslyckas
 - Om du använder en slutpunkt utan region skulle USP-fel orsaka konversationsöversättare
 - ID-generering i Universella Windows-program använder nu en lämpligt unik GUID-algoritm. Det tidigare och oavsiktligt standard till en stubbed genomförande som ofta producerade kollisioner över stora uppsättningar av interaktioner.
 
 **Exempel**
 
 - Unity-exempel för att använda Tal SDK med [Unity-mikrofon och direktuppspelning av tryckläge](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Andra ändringar**

 - [OpenSSL-konfigurationsdokumentation uppdaterad för Linux](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Tal SDK 1.9.0: 2020-januari release

**Nya funktioner**

- Konversation med flera enheter: anslut flera enheter till samma tal- eller textbaserade konversation och kan eventuellt översätta meddelanden som skickas mellan dem. Läs mer i [den här artikeln](multi-device-conversation.md). 
- Stöd för sökordsigenkänning har lagts till för Android .aar-paketet och lagt till stöd för x86- och x64-smaker. 
- Objective-C: `SendMessage` `SetMessageProperty` och metoder `Connection` som lagts till i syfte. Se dokumentation [här](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection).
- TTS C ++ `std::wstring` api stöder nu som syntes textinmatning, ta bort behovet av att konvertera en wstring till sträng innan du skickar den till SDK. Se detaljer [här](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [Språk-ID](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) och [källspråk config](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) är nu tillgängliga.
- JavaScript: Lade till `Connection` en funktion för att objekt för att `receivedServiceMessage`passera anpassade meddelanden från taltjänsten som motringning .
- JavaScript: Lagt `FromHost API` till stöd för att underlätta användning med on-prem behållare och suveräna moln. Se dokumentation [här](speech-container-howto.md).
- JavaScript: Vi `NODE_TLS_REJECT_UNAUTHORIZED` hedrar nu tack vare ett bidrag från [orgads](https://github.com/orgads). Se detaljer [här](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Bryta förändringar**

- `OpenSSL`har uppdaterats till version 1.1.1b och är statiskt länkad till Speech SDK-kärnbiblioteket för Linux. Detta kan orsaka en paus `OpenSSL` om inkorgen `/usr/lib/ssl` inte har installerats i katalogen i systemet. Läs [vår dokumentation](how-to-configure-openssl-linux.md) under Tal SDK-dokument för att komma runt problemet.
- Vi har ändrat datatypen som `WordLevelTimingResult.Offset` `int` returneras för C# från för att `long` möjliggöra åtkomst till `WordLevelTimingResults` när taldata är längre än 2 minuter.
- `PushAudioInputStream`och `PullAudioInputStream` nu skicka wav header information `AudioStreamFormat`till taltjänsten baserat på , eventuellt anges när de skapades. Kunderna måste nu använda [ljudinmatningsformatet som stöds](how-to-use-audio-input-streams.md). Alla andra format får suboptimala igenkänningsresultat eller kan orsaka andra problem. 

**Felkorrigeringar**

- Se `OpenSSL` uppdateringen under Breaking changes ovan. Vi fixade både en intermittent krasch och ett prestandaproblem (lås påstående under hög belastning) i Linux och Java. 
- Java: Gjort förbättringar för att objekt stängning i hög samtidighet scenarier.
- Omstrukturerade vårt NuGet-paket. Vi tog bort `Microsoft.CognitiveServices.Speech.core.dll` de `Microsoft.CognitiveServices.Speech.extension.kws.dll` tre kopiorna av och under lib-mappar, vilket gör NuGet-paketet mindre och snabbare att ladda ner, och vi har lagt till rubriker som behövs för att kompilera några C++-interna appar.
- Fast snabbstart prover [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Dessa var spännande utan att visa "mikrofon hittades inte" undantag på Linux, MacOS, Windows.
- Fast SDK-krasch med långa taligenkänningsresultat på vissa kodsökvägar som [det här exemplet](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Fast SDK-distributionsfel i Azure Web App-miljön för att lösa [det här kundproblemet](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Åtgärdade ett TTS-fel när du använde flera `<voice>` taggar eller `<audio>` taggar för att lösa det här [kundproblemet](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Fixade ett TTS 401-fel när SDK återställs från pausad.
- JavaScript: Fast en cirkulär import av ljuddata tack vare ett bidrag från [euirim](https://github.com/euirim). 
- JavaScript: lagt till stöd för att ange tjänstegenskaper, som lagts till i 1.7.
- JavaScript: åtgärdade ett problem där ett anslutningsfel kan resultera i kontinuerliga, misslyckade websocket-återanslutningsförsök.

**Exempel**

- Lade till exempel på sökordsigenkänning för Android [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- Lagt till TTS-exempel för serverscenariot [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Lade till snabbstarter för konversationsstarter med flera enheter för C# och C++ [här](quickstarts/multi-device-conversation.md).

**Andra ändringar**

- Optimerad SDK-kärnbiblioteksstorlek på Android.
- SDK i 1.9.0 och `int` framåt `string` stöder båda och typerna i röstsignaturversionsfältet för Konversationsrekriberare.

## <a name="speech-sdk-180-2019-november-release"></a>Tal SDK 1.8.0: 2019-November release

**Nya funktioner**

- Lade `FromHost()` till ett API, för att underlätta användningen med behållare på prem och suveräna moln.
- Lade till automatisk källspråksidentifiering för taligenkänning (i Java och C++)
- Lade `SourceLanguageConfig` till objekt för taligenkänning, som används för att ange förväntade källspråk (i Java och C++)
- Lagt `KeywordRecognizer` till stöd för Windows (UWP), Android och iOS via NuGet- och Unity-paketen
- Lade till Java-API för fjärrkonversation för att göra konversationsavskrifter i asynkrona batchar.

**Bryta förändringar**

- Konversationsrekriberingsfunktioner `Microsoft.CognitiveServices.Speech.Transcription`som flyttats under namnområdet .
- En del av metoderna för konversationsrekribering flyttas till en ny `Conversation` klass.
- Minskat stöd för 32-bitars (ARMv7 och x86) iOS

**Felkorrigeringar**

- Åtgärda för krasch `KeywordRecognizer` om lokal används utan en giltig taltjänstprenumerationsnyckel

**Exempel**

- Xamarinprov för`KeywordRecognizer`
- Unity-urval för`KeywordRecognizer`
- C++ och Java-exempel för automatisk källspråksidentifiering.

## <a name="speech-sdk-170-2019-september-release"></a>Tal SDK 1.7.0: 2019-September release

**Nya funktioner**

- Lagt till betastöd för Xamarin på UWP (Universal Windows Platform), Android och iOS
- Lagt till iOS-stöd för Unity
- Lagt `Compressed` till inmatningsstöd för ALaw, Mulaw, FLAC på Android, iOS och Linux
- Tillagd `SendMessageAsync` i `Connection` klassen för att skicka ett meddelande till tjänsten
- Tillagd `SetMessageProperty` i `Connection` klassen för att ange egenskap för ett meddelande
- TTS lade till bindningar för Java (Jre och Android), Python, Swift och Objective-C
- TTS har lagt till uppspelningsstöd för macOS, iOS och Android.
- Lade till "word gräns"-information för TTS.

**Felkorrigeringar**

- Fast IL2CPP bygga fråga om Unity 2019 för Android
- Åtgärdat problem med felformaterade rubriker i wav-filinmatning som bearbetas felaktigt
- Åtgärdat problem med UUIDs inte är unik i vissa anslutningsegenskaper
- Fixade några varningar om nullability-specificerare i Swift-bindningarna (kan kräva små kodändringar)
- Fixade ett fel som gjorde att websocketanslutningar stängdes oriktigt under nätverksbelastning
- Åtgärdade ett problem på Android som ibland resulterar i dubbla visnings-ID:er som används av`DialogServiceConnector`
- Förbättringar av stabiliteten i anslutningar över multi-turn interaktioner och rapportering `Canceled` av fel (via händelser) när de inträffar med`DialogServiceConnector`
- `DialogServiceConnector`sessionen startar kommer nu att ge `ListenOnceAsync()` händelser på rätt sätt, även när du ringer under en aktiv`StartKeywordRecognitionAsync()`
- Åtgärdat en krasch `DialogServiceConnector` i samband med att aktiviteter tas emot

**Exempel**

- Snabbstart för Xamarin
- Uppdaterad CPP Quickstart med Linux ARM64-information
- Snabbstart för Unity har uppdaterats med iOS-information

## <a name="speech-sdk-160-2019-june-release"></a>Tal SDK 1.6.0: 2019-Juni release

**Exempel**

- Snabbstartsexempel för text till tal om UWP och Unity
- Snabbstartsprov för Swift på iOS
- Unity-exempel för tal & intentigenkänning och översättning
- Uppdaterade snabbstartsexempel för`DialogServiceConnector`

**Förbättringar / Förändringar**

- Namnområde för dialog:
  - `SpeechBotConnector` har bytt namn till `DialogServiceConnector`
  - `BotConfig` har bytt namn till `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`har mappats om till`DialogServiceConfig::FromBotSecret()`
  - Alla befintliga Direktlinjetalklienter fortsätter att stödjas efter att du bytt namn
- Uppdatera TTS REST-kort för att stödja proxy, beständig anslutning
- Förbättra felmeddelandet när en ogiltig region skickas
- Snabb/mål-C:
  - Förbättrad felrapportering: Metoder som kan resultera i ett fel finns `NSError` nu i två versioner: En som exponerar ett objekt för felhantering och en som väcker ett undantag. De förstnämnda är utsatta för Swift. Denna ändring kräver anpassningar till befintlig Swift-kod.
  - Förbättrad händelsehantering

**Felkorrigeringar**

- Fix för TTS: där `SpeakTextAsync` framtiden returneras utan att vänta tills ljudet har slutförts rendering
- Åtgärdat för omstring av strängar i C# för att aktivera fullständigt språkstöd
- Åtgärdat för .NET core app problem att ladda kärnbibliotek med net461 målramverk i exempel
- Åtgärda för tillfälliga problem med att distribuera inbyggda bibliotek till utdatamappen i exempel
- Fix för stängning av webbuttag på ett tillförlitligt sätt
- Fix för eventuell krasch när du öppnar en anslutning under mycket tung belastning på Linux
- Åtgärdat för metadata som saknas i rampaketet för macOS
- Åtgärda problem `pip install --user` med i Windows

## <a name="speech-sdk-151"></a>Tal SDK 1.5.1

Detta är en felkorrigeringsversion och påverkar endast den inbyggda/hanterade SDK.This is a bug fix release and only affecting the native/managed SDK. Det påverkar inte JavaScript-versionen av SDK.

**Felkorrigeringar**

- Åtgärda FromSubscription när den används med konversationsavskrift.
- Fix bugg i sökord spotting för röstassistenter.

## <a name="speech-sdk-150-2019-may-release"></a>Tal SDK 1.5.0: 2019-maj release

**Nya funktioner**

- Keyword spotting (KWS) är nu tillgänglig för Windows och Linux. KWS-funktionen kan fungera med alla mikrofontyper, officiella KWS-stöd är dock för närvarande begränsad till de mikrofonmatriser som finns i Azure Kinect DK-maskinvaran eller Speech Devices SDK.
- Funktionen För frastips är tillgänglig via SDK. Mer information finns [här](how-to-phrase-lists.md).
- Konversationstranskriptionsfunktionen är tillgänglig via SDK. Se [här](conversation-transcription-service.md).
- Lägg till stöd för röstassistenter med direktlinjetalkanalen.

**Exempel**

- Lade till exempel för nya funktioner eller nya tjänster som stöds av SDK.

**Förbättringar / Förändringar**

- Lade till olika egenskaper för att justera tjänstens beteende eller serviceresultat (som att maskera svordomar och andra).
- Du kan nu konfigurera identifieraren via standardkonfigurationsegenskaperna, `FromEndpoint`även om du har skapat identifieraren .
- Mål-C: `OutputFormat` egendom lades `SPXSpeechConfiguration`till .
- SDK stöder nu Debian 9 som linuxdistribution.

**Felkorrigeringar**

- Åtgärdade ett problem där högtalarresursen var fördredad för tidigt i text-till-tal.

## <a name="speech-sdk-142"></a>Tal SDK 1.4.2

Detta är en felkorrigeringsversion och påverkar endast den inbyggda/hanterade SDK.This is a bug fix release and only affecting the native/managed SDK. Det påverkar inte JavaScript-versionen av SDK.

## <a name="speech-sdk-141"></a>Tal SDK 1.4.1

Detta är en JavaScript-release. Inga funktioner har lagts till. Följande korrigeringar gjordes:

- Förhindra att webbpaketet läses in https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Tal SDK 1.4.0: 2019-April release

**Nya funktioner**

- SDK stöder nu text-till-tal-tjänsten som en betaversion. Det stöds på Windows och Linux Desktop från C ++ och C #. Mer information finns i [översikten text-till-tal](text-to-speech.md#get-started).
- SDK stöder nu MP3- och Opus/OGG-ljudfiler som indatafiler för ström. Den här funktionen är endast tillgänglig på Linux från C++ och C# och är för närvarande i beta (mer information [här).](how-to-use-codec-compressed-audio-input-streams.md)
- Speech SDK för Java, .NET core, C++ och Objective-C har fått macOS-stöd. Objective-C-stödet för macOS är för närvarande i beta.
- iOS: Tal SDK för iOS (Objective-C) publiceras nu också som en CocoaPod.
- JavaScript: Stöd för mikrofon som inte är standard som indataenhet.
- JavaScript: Proxystöd för Node.js.

**Exempel**

- Prover för att använda Tal-SDK med C++ och med Objective-C på macOS har lagts till.
- Exempel som visar användningen av text-till-tal-tjänsten har lagts till.

**Förbättringar / Förändringar**

- Python: Ytterligare egenskaper för igenkänningsresultat exponeras nu via egenskapen. `properties`
- För ytterligare utvecklings- och felsökningsstöd kan du omdirigera SDK-loggnings- och diagnostikinformation till en loggfil (mer information [här).](how-to-use-logging.md)
- JavaScript: Förbättra ljudbehandlingsprestanda.

**Felkorrigeringar**

- Mac/iOS: Ett fel som ledde till en lång väntan när en anslutning till taltjänsten inte kunde upprättas har åtgärdats.
- Python: förbättra felhanteringen för argument i Python-motringningar.
- JavaScript: Fast fel tillståndsrapportering för tal som avslutades på RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Tal SDK 1.3.1: 2019-Februari uppdatering

Detta är en felkorrigeringsversion och påverkar endast den inbyggda/hanterade SDK.This is a bug fix release and only affecting the native/managed SDK. Det påverkar inte JavaScript-versionen av SDK.

**Bugg fix**

- Fixade en minnesläcka när mikrofoningången användes. Strömma baserad eller filindata påverkas inte.

## <a name="speech-sdk-130-2019-february-release"></a>Tal SDK 1.3.0: 2019-Februari release

**Nya funktioner**

- Tal-SDK stöder val av ingångsmikrofon genom `AudioConfig` klassen. På så sätt kan du strömma ljuddata till taltjänsten från en mikrofon som inte är standard. Mer information finns i dokumentationen som beskriver [val av ljudinmatningsenhet](how-to-select-audio-input-devices.md). Den här funktionen är ännu inte tillgänglig från JavaScript.
- Speech SDK stöder nu Unity i en betaversion. Ge feedback via ärendeavsnittet i [GitHub-exempeldatabasen](https://aka.ms/csspeech/samples). Den här versionen stöder Unity på Windows x86 och x64 (skrivbords- eller universella Windows-plattformsprogram) och Android (ARM32/64, x86). Mer information finns i vår [Unity snabbstart.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)
- Filen `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (som levererades i tidigare versioner) behövs inte längre. Funktionaliteten är nu integrerad i kärnan SDK.

**Exempel**

Följande nya innehåll finns i vårt [exempeldatabas:](https://aka.ms/csspeech/samples)

- Ytterligare prover `AudioConfig.FromMicrophoneInput`för .
- Ytterligare Python-exempel för avsiktsigenkänning och översättning.
- Ytterligare exempel för `Connection` att använda objektet i iOS.
- Ytterligare Java-exempel för översättning med ljudutgång.
- Nytt prov för användning av [BATCH Transkription REST API](batch-transcription.md).

**Förbättringar / Förändringar**

- Python
  - Förbättrad parameterverifiering och felmeddelanden i `SpeechConfig`.
  - Lägg till `Connection` stöd för objektet.
  - Stöd för 32-bitars Python (x86) i Windows.
  - Tal SDK för Python är ur beta.
- iOS
  - SDK är nu byggd mot iOS SDK version 12.1.
  - SDK stöder nu iOS-versioner 9.2 och senare.
  - Förbättra referensdokumentationen och åtgärda flera egenskapsnamn.
- JavaScript
  - Lägg till `Connection` stöd för objektet.
  - Lägga till typdefinitionsfiler för medföljande JavaScript
  - Inledande stöd och implementering för frastips.
  - Returegenskaper samling med tjänsten JSON för erkännande
- Windows DLL innehåller nu en versionsresurs.
- Om du skapar `FromEndpoint` en identifierare kan du lägga till parametrar direkt i slutpunkts-URL:en. Med `FromEndpoint` hjälp kan du inte konfigurera identifieraren via standardkonfigurationsegenskaperna.

**Felkorrigeringar**

- Tomt proxyanvändarnamn och proxylösenord hanterades inte korrekt. Med den här versionen, om du ställer proxy användarnamn och proxy lösenord till en tom sträng, kommer de inte att skickas när du ansluter till proxy.
- SessionId är skapade av SDK var inte alltid&nbsp;riktigt slumpmässigt för vissa språk / miljöer. Lade till slumpmässig generatorinittering för att åtgärda problemet.
- Förbättra hanteringen av auktoriseringstoken. Om du vill använda en auktoriseringstoken anger du i `SpeechConfig` och lämnar prenumerationsnyckeln tom. Skapa sedan igenkännaren som vanligt.
- I vissa `Connection` fall släpptes inte objektet korrekt. Det här problemet har åtgärdats.
- JavaScript-exemplet har åtgärdats för att stödja ljudutdata för översättningssyntes även på Safari.

## <a name="speech-sdk-121"></a>Tal SDK 1.2.1

Detta är en JavaScript-release. Inga funktioner har lagts till. Följande korrigeringar gjordes:

- Fire slutet av strömmen vid turn.end, inte på speech.end.
- Åtgärda fel i ljudpump som inte schemalade nästa skicka om den aktuella sändningen misslyckades.
- Åtgärda kontinuerlig igenkänning med auth-token.
- Buggfix för olika kännare/slutpunkter.
- Förbättringar av dokumentationen.

## <a name="speech-sdk-120-2018-december-release"></a>Tal SDK 1.2.0: 2018-December release

**Nya funktioner**

- Python
  - Betaversionen av Python-stöd (3.5 och högre) är tillgänglig med den här versionen. Mer information finns här](snabbstart-python.md).
- JavaScript
  - Tal-SDK för JavaScript har öppen källkod. Källkoden är tillgänglig på [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Vi stöder nu Node.js, mer info finns [här](quickstart-js-node.md).
  - Längdbegränsningen för ljudsessioner har tagits bort, återanslutning sker automatiskt under locket.
- `Connection`Objekt
  - Från `Recognizer`kan du komma `Connection` åt ett objekt. Med det här objektet kan du uttryckligen initiera tjänstanslutningen och prenumerera för att ansluta och koppla från händelser.
    (Den här funktionen är ännu inte tillgänglig från JavaScript och Python.)
- Stöd för Ubuntu 18.04.
- Android
  - Aktiverad ProGuard-support under APK-generering.

**Förbättringar**

- Förbättringar i den interna trådanvändningen, vilket minskar antalet trådar, lås, mutexes.
- Förbättrad felrapportering/information. I flera fall har felmeddelanden inte spridits ut hela vägen ut.
- Uppdaterade utvecklingsberoenden i JavaScript för att använda uppdaterade moduler.

**Felkorrigeringar**

- Fasta minnesläckor på grund `RecognizeAsync`av en typ som inte stämmer överens i .
- I vissa fall läckte undantag ut.
- Åtgärda minnesläcka i argument för översättningshändelse.
- Fixade ett låsproblem vid återanslutning i tidskrävande sessioner.
- Åtgärdade ett problem som kan leda till att slutresultatet saknades för misslyckade översättningar.
- C#: Om `async` en åtgärd inte var väntad i huvudtråden, var det möjligt att igenkänningspersonen kunde avyttras innan async-uppgiften slutfördes.
- Java: Åtgärdade ett problem som resulterade i en krasch av Java-VM.
- Mål-C: Fast uppräkningskartläggning. RecognizedIntent returnerades i `RecognizingIntent`stället för .
- JavaScript: Ställ in standardutdataformatet på "enkel" i `SpeechConfig`.
- JavaScript: Ta bort inkonsekvens mellan egenskaper för config-objektet i JavaScript och andra språk.

**Exempel**

- Uppdaterade och fixade flera prover (t.ex. utdataröster för översättning, etc.).
- Lade till nod.js-exempel i [exempeldatabasen](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Tal SDK 1.1.0

**Nya funktioner**

- Stöd för Android x86/x64.
- Proxysupport: I `SpeechConfig` objektet kan du nu anropa en funktion för att ange proxyinformation (värdnamn, port, användarnamn och lösenord). Den här funktionen är ännu inte tillgänglig på iOS.
- Förbättrad felkod och meddelanden. Om ett erkännande returnerade ett fel `Reason` har detta redan `CancellationDetails` angetts (i `Error`avbruten händelse) eller (i igenkänningsresultat) till . Den avbrutna händelsen innehåller nu `ErrorCode` ytterligare `ErrorDetails`två medlemmar och . Om servern returnerade ytterligare felinformation med det rapporterade felet är den nu tillgänglig i de nya medlemmarna.

**Förbättringar**

- Lade till ytterligare verifiering i identifierarkonfigurationen och lade till ytterligare felmeddelande.
- Förbättrad hantering av långvarig tystnad mitt i en ljudfil.
- NuGet-paketet: för .NET Framework-projekt förhindrar det att bygga med AnyCPU-konfiguration.

**Felkorrigeringar**

- Fixade flera undantag som finns i identifierare. Dessutom fångas undantag och omvandlas `Canceled` till händelse.
- Åtgärda en minnesläcka i egenskapshanteringen.
- Fast bugg där en ljudinmatningsfil kan krascha igenkännaren.
- Fixade ett fel där händelser kunde tas emot efter en session stop-händelse.
- Fixade vissa tävlingsförhållanden i gängning.
- Fixade ett iOS-kompatibilitetsproblem som kunde leda till en krasch.
- Stabilitetsförbättringar för Stöd för Android-mikrofon.
- Fixade ett fel där en identifierare i JavaScript skulle ignorera tolkningsspråket.
- Fixade ett fel `EndpointId` som hindrade inställningen (i vissa fall) i JavaScript.
- Ändrade parameterordning i AddIntent i JavaScript och lade till javascript-signatur som saknades. `AddIntent`

**Exempel**

- Lade till C++ och C#-exempel för pull- och pushstream-användning i [exempeldatabasen](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Tal SDK 1.0.1

Tillförlitlighetsförbättringar och buggfixar:

- Fast potentiellt allvarligt fel på grund av ras tillstånd i avyttra recognizer
- Fast potentiellt allvarligt fel vid urkopplade egenskaper.
- Lade till ytterligare fel- och parameterkontroll.
- Objective-C: Fast möjligt allvarligt fel som orsakas av namn åsidosättande i NSString.
- Mål-C: Justerad synlighet för API
- JavaScript: Fast när det gäller händelser och deras nyttolaster.
- Förbättringar av dokumentationen.

I vårt [exempel i databasen](https://aka.ms/csspeech/samples)lades ett nytt exempel på JavaScript till.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Tal SDK 1.0.0: 2018-September release

**Nya funktioner**

- Stöd för Objective-C på iOS. Kolla in vår [Objective-C snabbstart för iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Stöd för JavaScript i webbläsaren. Kolla in vår [JavaScript snabbstart](quickstart-js-browser.md).

**Bryta förändringar**

- Med den här versionen introduceras ett antal brytande ändringar.
  Mer information finns på [den här sidan.](https://aka.ms/csspeech/breakingchanges_1_0_0)

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018-Augusti release

**Nya funktioner**

- UWP-appar som skapats med Tal-SDK kan nu passera Windows App Certification Kit (WACK).
  Kolla in [UWP snabbstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Stöd för .NET Standard 2.0 på Linux (Ubuntu 16.04 x64).
- Experimentell: Stöd Java 8 på Windows (64-bitars) och Linux (Ubuntu 16,04 x64).
  Kolla in [snabbstarten java runtime miljö](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Funktionell förändring**

- Visa ytterligare information om felinformation om anslutningsfel.

**Bryta förändringar**

- På Java (Android) kräver `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` funktionen inte längre en sökvägsparameter. Nu identifieras sökvägen automatiskt på alla plattformar som stöds.
- Den get-accessor av `EndpointUrl` fastigheten i Java och C # togs bort.

**Felkorrigeringar**

- I Java implementeras ljudsyntesresultatet på översättningsrecensen nu.
- Fixade ett fel som kunde orsaka inaktiva trådar och ett ökat antal öppna och oanvända sockets.
- Fixade ett problem, där ett långvarigt erkännande kunde avslutas mitt i överföringen.
- Fixade ett tävlingstillstånd i avslutningsavstängningen.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Tal SDK 0.5.0: 2018-Juli release

**Nya funktioner**

- Stöd Android-plattformen (API 23: Android 6.0 Marshmallow eller senare). Kolla in [Android snabbstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Stöd för .NET Standard 2.0 i Windows. Kolla in [snabbstarten .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Experimentell: Stöd UWP i Windows (version 1709 eller senare).
  - Kolla in [UWP snabbstart](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - UWP-appar som skapats med Tal-SDK passerar ännu inte Windows App Certification Kit (WACK).
- Stöd för tidskrävande igenkänning med automatisk återanslutning.

**Funktionella förändringar**

- `StartContinuousRecognitionAsync()`stöder långvarig igenkänning.
- Igenkänningsresultatet innehåller fler fält. De förskjuts från ljudstart och varaktighet (både i fästingar) för den tolkade `InitialSilenceTimeout` texten `InitialBabbleTimeout`och ytterligare värden som representerar igenkänningsstatus, till exempel, och .
- Support AuthorizationToken för att skapa fabriksinstanser.

**Bryta förändringar**

- Igenkänningshändelser: `NoMatch` händelsetypen slogs samman till händelsen. `Error`
- SpeechOutputFormat i C# döptes om till `OutputFormat` för att hålla sig i linje med C++.
- Returtypen för vissa `AudioInputStream` metoder i gränssnittet förändrats något:
  - I Java `read` returneras `long` metoden `int`nu i stället för .
  - I C#returneras `Read` `uint` metoden nu `int`i stället för .
  - I `Read` C++returneras `GetFormat` `size_t` metoderna och `int`nu i stället för .
- C++: Instanser av ljudingångsströmmar kan `shared_ptr`nu endast skickas som en .

**Felkorrigeringar**

- Fast felaktiga returvärden i `RecognizeAsync()` resultatet när time out.
- Beroendet av mediastiftelsebibliotek i Windows har tagits bort. SDK använder nu Core Audio API:er.
- Dokumentationskorrigering: Lade till en [regionssida](regions.md) för att beskriva de regioner som stöds.

**Känt problem**

- Tal-SDK för Android rapporterar inte talsyntesresultat för översättning. Det här problemet kommer att åtgärdas i nästa utgåva.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Tal SDK 0.4.0: 2018-Juni release

**Funktionella förändringar**

- AudioInputStream

  En identifierare kan nu förbruka en ström som ljudkälla. Mer information finns i den relaterade [vägledningen](how-to-use-audio-input-streams.md).

- Detaljerat utdataformat

  När du `SpeechRecognizer`skapar ett `Detailed` kan `Simple` du begära eller utdataformat. Den `DetailedSpeechRecognitionResult` innehåller en konfidenspoäng, erkänd text, rå lexikal form, normaliserad form och normaliserad form med maskerad svordomar.

**Bryta förändring**

- Ändrad `SpeechRecognitionResult.Text` `SpeechRecognitionResult.RecognizedText` till från i C#.

**Felkorrigeringar**

- Åtgärdade ett eventuellt motringningsproblem i USP-lagret under avstängning.
- Om en identifierare förbrukade en ljudinmatningsfil höll den kvar i filhandtaget längre än nödvändigt.
- Tog bort flera dödlägen mellan meddelandepumpen och kännaren.
- Avfyra `NoMatch` ett resultat när responsen från servicen är tidsutstrut.
- Mediastiftelsens bibliotek i Windows har försenats. Det här biblioteket krävs endast för mikrofoningång.
- Uppladdningshastigheten för ljuddata är begränsad till ungefär dubbelt så hög som den ursprungliga ljudhastigheten.
- På Windows är C# .NET-sammansättningar nu starka namngivna.
- Dokumentationskorrigering: `Region` krävs information för att skapa en identifierare.

Fler exempel har lagts till och uppdateras ständigt. Den senaste uppsättningen exempel finns i [GitHub-databasen för Tal SDK](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018-May release

Den här versionen är den första offentliga förhandsversionen av Cognitive Services Speech SDK.
