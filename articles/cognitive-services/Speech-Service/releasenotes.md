---
title: Viktig information – tal tjänst
titleSuffix: Azure Cognitive Services
description: Se en körnings logg med funktions versioner, förbättringar, fel korrigeringar och kända problem för tal tjänsten.
services: cognitive-services
author: BrianMouncer
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: 353e0478172ed03cde848d8c5127d2ee41724963
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481239"
---
# <a name="release-notes"></a>Viktig information
## <a name="speech-sdk-170-2019-september-release"></a>1\.7.0 för tal-SDK: 2019 – september version

**Nya funktioner**
*   Beta stöd för Xamarin på Universell Windows-plattform (UWP), Android och iOS har lagts till
*   IOS-stöd har lagts till för Unity
*   Har lagt till stöd för komprimerade instöd för ALaw, Mulaw, FLAC på Android, iOS och Linux
*   Lade till SendMessageAsync i anslutnings klassen för att skicka ett meddelande till tjänsten
*   Lade till SetMessageProperty i anslutnings klassen för att ange egenskapen för ett meddelande
*   TTS-tillagda bindningar för Java (JRE och Android), python, Swift och mål-C
*   TTS-stöd för uppspelning av OS för macOS, iOS och Android.
*   Information om Word-gränser för TTS har lagts till.

**Fel korrigeringar**
*   Fast IL2CPP build-problem på Unity 2019 för Android
*   Ett åtgärds fel med felaktigt utformade huvuden i WAV-filen har bearbetats felaktigt
*   Ett fast problem med UUID-nummer är inte unikt i vissa anslutnings egenskaper
*   Åtgärdade några varningar om specificerare för null-värden i Swift-bindningar (kan kräva små kod ändringar)
*   En bugg har åtgärd ATS som gjorde att WebSocket-anslutningar stängdes på ett smidigt sätt under nätverks belastning
*   iOS: stöd har släppts för arm7s-arkitekturen
*   Ett problem har åtgärd ATS på Android som ibland resulterar i dubbla exponerings-ID: n som används av DialogServiceConnector
*   Förbättringar av anslutningarnas stabilitet i flera olika interaktioner och rapportering av fel (via avbrutna händelser) när de inträffar med DialogServiceConnector
*   DialogServiceConnector-sessionen börjar nu att tillhandahålla händelser, inklusive när ListenOnceAsync anropas () under en aktiv StartKeywordRecognitionAsync ()
*   Har åtgärdat en krasch som är associerad med DialogServiceConnector-aktiviteter som tas emot 

**Exempel**
*   Snabb start för Xamarin
*   Uppdaterad CPP-snabb start med information om Linux-ARM64
*   Uppdaterad enhets snabb start med iOS-information

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK-1.6.0:2019 – juni version

**Exempel**
*   Snabb starts exempel för text till tal på UWP och Unity
*   Snabb starts exempel för SWIFT på iOS
*   Unity-exempel för tal & Avsiktsigenkänning och översättning
*   Uppdaterade snabb starts exempel för DialogServiceConnector

**Förbättringar/ändringar**
* Namn område för dialog ruta:
    * SpeechBotConnector har bytt namn till DialogServiceConnector
    * BotConfig har bytt namn till DialogServiceConfig
    * BotConfig:: FromChannelSecret () har mappats om till DialogServiceConfig:: FromBotSecret ()
    * Alla befintliga direkt linje tal klienter fortsätter att stödjas efter namnbytet
* Uppdatera TTS-kortet till stöd för proxy, beständig anslutning
* Förbättra fel meddelandet när en ogiltig region skickas
* SWIFT/mål-C:
    * Förbättrad fel rapportering: metoder som kan resultera i ett fel finns nu i två versioner: en som visar ett `NSError`-objekt för fel hantering och ett som aktiverar ett undantag. Den tidigare visas för Swift. Den här ändringen kräver anpassningar av befintlig SWIFT-kod.
    * Förbättrad händelse hantering

**Fel korrigeringar**
*   Korrigering för TTS: där SpeakTextAsync framtida returneras utan att vänta tills ljudet har Render ATS
*   Korrigering för konvertering av strängar i C# för att aktivera stöd för fullständigt språk
*   Åtgärda problem med .NET Core-appen för att läsa in kärn bibliotek med net461 Target Framework i exempel
*   Åtgärda tillfälliga problem för att distribuera interna bibliotek till mappen utdata i exempel
*   Korrigering för att webb uttag ska avslutas på ett tillförlitligt sätt
*   Åtgärda möjliga krascher när du öppnar en anslutning under mycket tung belastning på Linux
*   Korrigering för saknade metadata i Ramverks paketet för macOS
*   Åtgärda problem med `pip install --user` i Windows


## <a name="speech-sdk-151"></a>1\.5.1 för tal-SDK

Detta är en fel korrigerings version och påverkar endast det inbyggda/hanterade SDK: n. Den påverkar inte JavaScript-versionen av SDK: n.

**Fel korrigeringar**

* Åtgärda FromSubscription när den används med konversations avskriftering.
* Åtgärda felet i Keyword upptäcka för röst assistenter.


## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK-1.5.0:2019 – maj-version

**Nya funktioner**

* Nyckelordet upptäcka (KWS) är nu tillgängligt för Windows och Linux. KWS-funktionen kan fungera med valfri mikrofon typ, officiellt KWS-support, men är för närvarande begränsad till de mikrofoner som finns i Azure Kinect DK-maskinvara eller i tal enheter SDK.
* Funktionen för fras tips är tillgänglig via SDK. Mer information finns [här](how-to-phrase-lists.md).
* Konversations avskrifts funktionerna är tillgängliga via SDK: n. Se [här](conversation-transcription-service.md).
* Lägg till stöd för röst assistenter med hjälp av kanalen för direkt linje tal.

**Exempel**

* Exempel för nya funktioner eller nya tjänster som stöds av SDK har lagts till.

**Förbättringar/ändringar**

* Har lagt till olika igenkännings egenskaper för att justera tjänstens beteende eller tjänst resultat (t. ex. maskering av svordomar och andra).
* Nu kan du konfigurera tolken via standard konfigurations egenskaperna, även om du har skapat tolken `FromEndpoint`.
* Mål-C: `OutputFormat`-egenskapen har lagts till i SPXSpeechConfiguration.
* SDK stöder nu Debian 9 som en Linux-distribution.

**Fel korrigeringar**

* Ett problem har åtgärd ATS där högtalar resursen var destructed för tidigt i text-till-tal.
## <a name="speech-sdk-142"></a>1\.4.2 för tal-SDK

Detta är en fel korrigerings version och påverkar endast det inbyggda/hanterade SDK: n. Den påverkar inte JavaScript-versionen av SDK: n.

## <a name="speech-sdk-141"></a>Tal-SDK 1.4.1

Detta är endast en JavaScript-version. Inga funktioner har lagts till. Följande korrigeringar har gjorts:

* Förhindra att webb paketet läser in https-proxy-agenten.

## <a name="speech-sdk-140-2019-april-release"></a>Tal-SDK 1.4.0:2019 – april version

**Nya funktioner** 

* SDK stöder nu text till tal-tjänsten som beta version. Den stöds på Windows-och Linux Desktop från C++ och C#. Mer information hittar du i [text till tal-översikten](text-to-speech.md#get-started-with-text-to-speech).
* SDK stöder nu MP3-och Opus/OGG-ljudfiler som strömmande indatafiler. Den här funktionen är endast tillgänglig i Linux C++ från C# och och är för närvarande i beta version (mer information [här](how-to-use-codec-compressed-audio-input-streams.md)).
* Tal-SDK för Java, .NET Core C++ och mål-C har fått stöd för MacOS. Mål-C-supporten för macOS är för närvarande beta version.
* iOS: talet SDK för iOS (mål-C) har nu också publicerats som en CocoaPod.
* Java Script: stöd för mikrofon som inte är standard som en inmatad enhet.
* Java Script: proxy-stöd för Node. js.

**Exempel**

* Exempel på att använda tal-SDK C++ med och med mål-C på MacOS har lagts till.
* Exempel som demonstrerar användningen av text till tal-tjänsten har lagts till.

**Förbättringar/ändringar**

* Python: ytterligare egenskaper för igenkännings resultat exponeras nu via `properties`-egenskapen.
* Om du vill ha ytterligare stöd för utveckling och fel sökning kan du omdirigera information om SDK-loggning och-diagnostik i en loggfil (mer information [här](how-to-use-logging.md)).
* Java Script: förbättra prestanda för ljud bearbetning.

**Fel korrigeringar**

* Mac/iOS: ett fel som ledde till en lång väntan på att det inte gick att upprätta en anslutning till röst tjänsten har åtgärd ATS.
* Python: förbättra fel hanteringen för argument i python-motanrop.
* Java Script: fast felaktig tillstånds rapportering för tal slutar på RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>1\.3.1 för tal-SDK: 2019 – februari uppdatering

Detta är en fel korrigerings version och påverkar endast det inbyggda/hanterade SDK: n. Den påverkar inte JavaScript-versionen av SDK: n.

**Fel korrigering**

* En minnes läcka har åtgärd ATS när mikrofonen används. Stream-eller File-indata påverkas inte.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK-1.3.0:2019 – februari utgåva

**Nya funktioner**

* Tal-SDK: n stöder val av mikrofon för ingångs ljud via klassen AudioConfig. På så sätt kan du strömma ljud data till tal tjänsterna från en mikrofon som inte är standard. Mer information finns i dokumentationen som beskriver valet av [enhet för ljud inspelning](how-to-select-audio-input-devices.md). Den här funktionen är inte tillgänglig ännu från Java Script.
* Talet SDK stöder nu Unity i en beta version. Ge feedback via avsnittet ärende i GitHub- [exempel lagrings plats](https://aka.ms/csspeech/samples). Den här versionen stöder Unity på Windows x86 och x64 (Skriv bords-eller Universell Windows-plattform program) och Android (ARM32/64, x86). Mer information finns i vår [snabb start](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity).
* Filen `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (levererades i tidigare versioner) behövs inte längre. Funktionerna är nu integrerade i kärn-SDK: n.


**Exempel**

Följande nya innehåll är tillgängligt i vår [exempel lagrings plats](https://aka.ms/csspeech/samples):

* Ytterligare exempel för AudioConfig. FromMicrophoneInput.
* Ytterligare python-exempel för avsikts igenkänning och översättning.
* Ytterligare exempel för att använda anslutningsobjektet i iOS.
* Ytterligare Java-exempel för översättning med ljud uppspelning.
* Nytt exempel för användning av [batch-Avskrifter REST API](batch-transcription.md).

**Förbättringar/ändringar**

* Python
  * Förbättrad parameter verifiering och fel meddelanden i SpeechConfig.
  * Lägg till stöd för objektet anslutning.
  * Stöd för 32-bitars python (x86) i Windows.
  * Tal-SDK för python är av beta version.
* iOS
  * SDK är nu byggt mot iOS SDK version 12,1.
  * SDK stöder nu iOS version 9,2 och senare.
  * Förbättra referens dokumentationen och korrigera flera egenskaps namn.
* JavaScript
  * Lägg till stöd för objektet anslutning.
  * Lägga till typ definitions filer för paketerade JavaScript-skript
  * Inledande support och implementering för fras tips.
  * Hämta egenskaps samling med tjänst-JSON för igenkänning
* Windows-DLL-filer innehåller nu en versions resurs.
* Om du skapar en igenkännings `FromEndpoint` kan du lägga till parametrar direkt i slut punkts-URL: en. Med `FromEndpoint` kan du inte konfigurera tolken via standard konfigurations egenskaperna.

**Fel korrigeringar**

* Tomt användar namn för proxy och proxy-lösenord hanterades inte korrekt. Om du ställer in proxy-användarnamn och proxy-lösenord till en tom sträng i den här versionen skickas de inte vid anslutning till proxyservern.
* SessionId som skapats av SDK var inte alltid helt slumpmässigt för vissa språk&nbsp;/miljöer. Har lagt till en slumpmässig Generator initiering för att åtgärda problemet.
* Förbättra hanteringen av autentiseringstoken. Om du vill använda en autentiseringstoken anger du i SpeechConfig och lämnar prenumerations nyckeln tom. Skapa sedan tolken som vanligt.
* I vissa fall släpptes inte anslutningsobjektet korrekt. Det här problemet har åtgärd ATS.
* JavaScript-exemplet har åtgärd ATS för att stödja ljud utdata för översättnings syntes också i Safari.

## <a name="speech-sdk-121"></a>Tal-SDK 1.2.1

Detta är endast en JavaScript-version. Inga funktioner har lagts till. Följande korrigeringar har gjorts:

* Brand i slutet av strömmen vid turn. End, inte i tal. End.
* Åtgärda fel i ljud pumpen som inte schemalades nästa sändning om den aktuella sändningen misslyckades.
* Åtgärda kontinuerlig igenkänning med auth-token.
* Fel korrigering för olika tolkningar/slut punkter.
* Förbättringar av dokumentation.

## <a name="speech-sdk-120-2018-december-release"></a>1\.2.0 för tal-SDK: 2018 – december version

**Nya funktioner**

* Python
  * Beta versionen av python-supporten (3,5 och senare) är tillgänglig i den här versionen. Mer information finns här] (snabb start-python.md).
* JavaScript
  * Tal-SDK för Java Script har öppnats med öppen källkod. Käll koden finns på [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Vi har nu stöd för Node. js, mer information finns [här](quickstart-js-node.md).
  * Längd begränsningen för ljudsessioner har tagits bort, åter anslutning sker automatiskt under locket.
* Anslutnings objekt
  * Från tolken kan du komma åt ett anslutnings objekt. Med det här objektet kan du uttryckligen initiera tjänst anslutningen och prenumerera på att ansluta och koppla från händelser.
    (Den här funktionen är inte tillgänglig ännu från Java Script och python.)
* Stöd för Ubuntu 18,04.
* Android
  * Stöd för aktive rad proguard vid APK-generering.

**Bättre**

* Förbättringar i den interna tråd användningen, vilket minskar antalet trådar, lås, mutexer.
* Förbättrad fel rapportering/information. I flera fall har fel meddelanden inte spridits ut hela vägen.
* Uppdaterade utvecklings beroenden i Java Script för att använda aktuella moduler.

**Fel korrigeringar**

* Fasta minnes läckor på grund av typ matchnings fel i RecognizeAsync.
* I vissa fall läcker undantag.
* Korrigerar minnes läckor i översättnings händelse argument.
* Åtgärdat låsnings problem vid åter anslutning vid tids krävande sessioner.
* Ett problem som kan leda till saknat slut resultat för misslyckade översättningar har åtgärd ATS.
* C#: Om en asynkron åtgärd inte förväntades i huvud tråden var det möjligt att identifieraren kunde tas bort innan den asynkrona uppgiften slutfördes.
* Java: åtgärdat ett problem som resulterade i en krasch i Java VM.
* Mål-C: fast Enum-mappning; RecognizedIntent returnerades i stället för RecognizingIntent.
* Java Script: Ange Standardutdataformatet till "enkel" i SpeechConfig.
* Java Script: ta bort inkonsekvens mellan egenskaper i konfigurationsobjektet i Java Script och andra språk.

**Exempel**

* Uppdaterade och korrigerade flera exempel (till exempel utmatnings röster för översättning osv.).
* Node. js-exempel har lagts till i [exempel lagrings platsen](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>1\.1.0 för tal-SDK

**Nya funktioner**

* Stöd för Android x86/x64.
* Stöd för proxy: i SpeechConfig-objektet kan du nu anropa en funktion för att ange proxyinformation (värdnamn, port, användar namn och lösen ord). Den här funktionen är inte tillgänglig ännu på iOS.
* Förbättrad felkod och meddelanden. Om ett igenkännings fel returnerade ett fel angav detta redan `Reason` (i avbruten händelse) eller `CancellationDetails` (i resultat igenkänning) för att `Error`. Den avbrutna händelsen innehåller nu två ytterligare medlemmar, `ErrorCode` och `ErrorDetails`. Om servern returnerade ytterligare fel information med det rapporterade felet är den nu tillgänglig i de nya medlemmarna.

**Bättre**

* Ytterligare kontroll har lagts till i igenkännings konfigurationen och ytterligare fel meddelande har lagts till.
* Förbättrad hantering av lång tids tystnad i mitten av en ljudfil.
* NuGet-paket: för .NET Framework-projekt förhindrar det att du skapar med AnyCPU-konfiguration.

**Fel korrigeringar**

* Det finns flera undantag i identifierarna. Dessutom fångas och konverteras undantag till avbrutna händelser.
* Åtgärda en minnes läcka i egenskaps hantering.
* Åtgärdat fel där en ljudfil i indatafilen skulle krascha tolken.
* Ett fel har åtgärd ATS där händelser kan tas emot efter en händelse för att stoppa sessionen.
* Vissa tävlings förhållanden har åtgärd ATS i trådar.
* Åtgärdat kompatibilitetsproblem med iOS som kan resultera i en krasch.
* Stabilitets förbättringar för stöd för Android-mikrofon.
* Ett fel har åtgärd ATS där en tolk i Java Script skulle ignorera tolknings språket.
* Åtgärdade ett fel som förhindrar inställning av EndpointId (i vissa fall) i Java Script.
* Den ändrade parameter ordningen i AddIntent i Java Script och lagt till AddIntent JavaScript-signatur som saknas.

**Exempel**

* Lade C++ till C# och samplar för pull-och push-dataström-användning i [exempel lagrings platsen](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>1\.0.1 för tal-SDK

Förbättringar av tillförlitlighet och fel korrigeringar:

* Det gick inte att åtgärda allvarligt fel på grund av ett tävlings tillstånd i att identifiera igenkänning
* Åtgärdat potentiellt allvarligt fel i händelse av unset egenskaper.
* Ytterligare fel och parameter kontroll har lagts till.
* Mål-C: åtgärdat allvarligt fel som orsakats av att namnet åsidosätts i NSString.
* Mål-C: anpassad synlighet för API
* Java Script: åtgärdat avseende händelser och deras nytto laster.
* Förbättringar av dokumentation.

I vår [exempel lagring](https://aka.ms/csspeech/samples)lades ett nytt exempel för Java Script till.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0:2018 – september version

**Nya funktioner**

* Stöd för mål-C på iOS. Kolla vår [mål-C-snabb start för iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
* Stöd för Java Script i webbläsare. Kolla i vår [snabb start för Java Script](quickstart-js-browser.md).

**Bryta ändringar**

* I den här versionen introduceras ett antal avbrytande ändringar.
  Mer information finns på [den här sidan](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0:2018 – augusti utgåva

**Nya funktioner**

* UWP-appar som skapats med Speech SDK kan nu skicka Windows app Certificate Kit (WACK).
  Ta en titt på snabb starten för [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
* Stöd för .NET standard 2,0 på Linux (Ubuntu 16,04 x64).
* Experimentell: stöder Java 8 på Windows (64-bitars) och Linux (Ubuntu 16,04 x64).
  Kolla in [Java Runtime Environment snabb start](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Funktionell ändring**

* Visa ytterligare information om fel information om anslutnings fel.

**Bryta ändringar**

* I Java (Android) kräver funktionen `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` inte längre en Sök vägs parameter. Nu identifieras sökvägen automatiskt på alla plattformar som stöds.
* Get-accessorn för egenskapen `EndpointUrl` i Java och C# har tagits bort.

**Fel korrigeringar**

* I Java implementeras ljud sammanfattnings resultatet i översättnings tolken nu.
* Ett fel som kan orsaka inaktiva trådar och ett ökat antal öppna och oanvända Sockets har åtgärd ATS.
* Ett problem har åtgärd ATS, där en tids krävande igenkänning kan avslutas mitt i överföringen.
* Ett konkurrens villkor har åtgärd ATS i avstängningen av tolken.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0:2018 – juli utgåva

**Nya funktioner**

* Stöd för Android-plattform (API 23: Android 6,0 Marshmallow eller högre). Kolla in [Android-snabb](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)starten.
* Stöd för .NET standard 2,0 i Windows. Kolla in [.net Core-snabb](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)starten.
* Experimentell: stöd för UWP i Windows (version 1709 eller senare).
  * Ta en titt på snabb starten för [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  * Obs! UWP-appar som skapats med tal-SDK: n har inte överfört Windows app Certificate Kit (WACK).
* Stöd för tids krävande igenkänning med automatisk åter anslutning.

**Funktionella ändringar**

* `StartContinuousRecognitionAsync()` stöder tids krävande igenkännings igenkänning.
* Igenkännings resultatet innehåller fler fält. De är förskjutna från början och varaktigheten för ljudet (både i Ticket) för den tolkade texten och ytterligare värden som representerar igenkännings status, till exempel `InitialSilenceTimeout` och `InitialBabbleTimeout`.
* Stöd för AuthorizationToken för att skapa fabriks instanser.

**Bryta ändringar**

* Igenkännings händelser: nomatch-händelse typen slogs samman till fel händelsen.
* SpeechOutputFormat i C# har bytt namn till OutputFormat för att vara justerad mot C++.
* Retur typen för vissa metoder i `AudioInputStream`-gränssnittet ändrades något.
   * I Java returnerar metoden `read` nu `long` i stället för `int`.
   * I C#returnerar metoden `Read` nu `uint` i stället för `int`.
   * I C++returnerar `Read` och `GetFormat` metoder `size_t` i stället för `int`.
* C++: Instanser av ljud inspelnings strömmar kan nu bara skickas som en `shared_ptr`.

**Fel korrigeringar**

* Korrigerade Felaktiga retur värden i resultatet när `RecognizeAsync()` timeout.
* Beroendet på Media Foundation-bibliotek i Windows har tagits bort. SDK använder nu centrala ljud-API: er.
* Dokumentations korrigering: en [region](regions.md) sida har lagts till för att beskriva de regioner som stöds.

**Känt problem**

* Tal-SDK för Android rapporterar inte tal syntes resultat för översättning. Det här problemet kommer att åtgärdas i nästa version.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0:2018 – juni utgåva

**Funktionella ändringar**

- AudioInputStream

  En tolk kan nu använda en ström som ljud källa. Mer information finns i den närliggande [instruktions guiden](how-to-use-audio-input-streams.md).

- Detaljerat utdataformat

  När du skapar en `SpeechRecognizer`kan du begära `Detailed` eller `Simple` utdataformat. `DetailedSpeechRecognitionResult` innehåller en förtroende poäng, en tolkad text, en rå lexikal form, normaliserad form och normaliserad form med maskerade svordomar.

**Bryta ändring**

- Ändrades till `SpeechRecognitionResult.Text` från `SpeechRecognitionResult.RecognizedText` C#i.

**Fel korrigeringar**

- Ett möjligt problem med återanrop har åtgärd ATS i USP-skiktet under avstängning.

- Om en tolk förbrukar en ljud indatafil, behålls den på fil hanteringen längre än nödvändigt.

- Flera död lägen mellan meddelande pumpen och tolken har tagits bort.

- Utlös ett `NoMatch` resultat när svar från tjänsten har nått sin tids gräns.

- Media Foundation-biblioteken i Windows är fördröjda. Det här biblioteket krävs endast för mikrofon ineffekter.

- Uppladdnings hastigheten för ljuddata är begränsad till cirka två gånger den ursprungliga ljud hastigheten.

- I Windows är C# .net-sammansättningar nu starkt namngivna.

- Dokumentations korrigering: `Region` nödvändig information för att skapa en tolk.

Fler exempel har lagts till och uppdateras ständigt. Den senaste uppsättningen exempel finns i [GitHub-lagringsplatsen Speech SDK samples](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733:2018 – maj release

Den här versionen är den första offentliga för hands versionen av Cognitive Services Speech SDK.
