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
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 12eecc4998153cbeedeb907ecad33c56141a50e6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559128"
---
# <a name="release-notes"></a>Viktig information

## <a name="speech-sdk-160-2019-june-release"></a>1\.6.0 för tal-SDK: 2019 – utgåva från juni

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
    * Förbättrad fel rapportering: De metoder som kan resultera i ett fel finns nu i två versioner: En som visar ett `NSError` objekt för fel hantering och ett som aktiverar ett undantag. Den tidigare visas för Swift. Den här ändringen kräver anpassningar av befintlig SWIFT-kod.
    * Förbättrad händelse hantering

**Felkorrigeringar**
*   Korrigering för TTS: där SpeakTextAsync framtida returneras utan att vänta tills ljudet har Render ATS
*   Korrigering för konvertering av strängar i C# för att aktivera stöd för fullständigt språk
*   Åtgärda problem med .NET Core-appen för att läsa in kärn bibliotek med net461 Target Framework i exempel
*   Åtgärda tillfälliga problem för att distribuera interna bibliotek till mappen utdata i exempel
*   Korrigering för att webb uttag ska avslutas på ett tillförlitligt sätt
*   Åtgärda möjliga krascher när du öppnar en anslutning under mycket tung belastning på Linux
*   Korrigering för saknade metadata i Ramverks paketet för macOS
*   Åtgärda problem med `pip install --user` Windows


## <a name="speech-sdk-151"></a>1\.5.1 för tal-SDK

Detta är en fel korrigerings version och påverkar endast det inbyggda/hanterade SDK: n. Den påverkar inte JavaScript-versionen av SDK: n.

**Felkorrigeringar**

* Åtgärda FromSubscription när den används med konversations avskriftering.
* Åtgärda felet i Keyword upptäcka för den första virtuella assistenten för rösten.


## <a name="speech-sdk-150-2019-may-release"></a>1\.5.0 för tal-SDK: 2019 – maj-utgåva

**Nya funktioner**

* Funktionen Wake Word (nyckelord upptäcka/KWS) är nu tillgänglig för Windows och Linux. KWS-funktionen kan fungera med valfri mikrofon typ, officiellt KWS-support, men är för närvarande begränsad till de mikrofoner som finns i Azure Kinect DK-maskinvara eller i tal enheter SDK.
* Funktionen för fras tips är tillgänglig via SDK. Mer information finns [här](how-to-phrase-lists.md).
* Konversations avskrifts funktionerna är tillgängliga via SDK: n. Se [här](conversation-transcription-service.md).
* Lägg till stöd för röst-första virtuella assistenter med hjälp av kanalen för direkt linje tal.

**Exempel**

* Exempel för nya funktioner eller nya tjänster som stöds av SDK har lagts till.

**Förbättringar/ändringar**

* Har lagt till olika igenkännings egenskaper för att justera tjänstens beteende eller tjänst resultat (t. ex. maskering av svordomar och andra).
* Nu kan du konfigurera tolken via standard konfigurations egenskaperna, även om du har skapat tolken `FromEndpoint`.
* Mål-C: `OutputFormat` egenskapen har lagts till i SPXSpeechConfiguration.
* SDK stöder nu Debian 9 som en Linux-distribution.

**Felkorrigeringar**

* Ett problem har åtgärd ATS där högtalar resursen var destructed för tidigt i text-till-tal.
## <a name="speech-sdk-142"></a>1\.4.2 för tal-SDK

Detta är en fel korrigerings version och påverkar endast det inbyggda/hanterade SDK: n. Den påverkar inte JavaScript-versionen av SDK: n.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Detta är endast en JavaScript-version. Inga funktioner har lagts till. Följande korrigeringar har gjorts:

* Förhindra att webb paketet läser in https-proxy-agenten.

## <a name="speech-sdk-140-2019-april-release"></a>1\.4.0 för tal-SDK: 2019 – april-version

**Nya funktioner** 

* SDK stöder nu text till tal-tjänsten som beta version. Den stöds på Windows-och Linux Desktop från C++ och C#. Mer information hittar du i [text till tal-översikten](text-to-speech.md#get-started-with-text-to-speech).
* SDK stöder nu MP3-och Opus/OGG-ljudfiler som strömmande indatafiler. Den här funktionen är endast tillgänglig i Linux C++ från C# och och är för närvarande i beta version (mer information [här](how-to-use-codec-compressed-audio-input-streams.md)).
* Tal-SDK för Java, .NET Core C++ och mål-C har fått stöd för MacOS. Mål-C-supporten för macOS är för närvarande beta version.
* iOS: Talet SDK för iOS (mål-C) har nu också publicerats som en CocoaPod.
* JavaScript: Stöd för mikrofon som inte är standard som en inmatad enhet.
* JavaScript: Proxy-stöd för Node. js.

**Exempel**

* Exempel på att använda tal-SDK C++ med och med mål-C på MacOS har lagts till.
* Exempel som demonstrerar användningen av text till tal-tjänsten har lagts till.

**Förbättringar/ändringar**

* Python: Ytterligare egenskaper för igenkännings resultat exponeras nu via `properties` egenskapen.
* Om du vill ha ytterligare stöd för utveckling och fel sökning kan du omdirigera information om SDK-loggning och-diagnostik i en loggfil (mer information [här](how-to-use-logging.md)).
* JavaScript: Förbättra prestanda för ljud bearbetning.

**Felkorrigeringar**

* Mac/iOS: Ett fel som ledde till en lång väntan på att det inte gick att upprätta en anslutning till röst tjänsten har åtgärd ATS.
* Python: förbättra fel hanteringen för argument i python-motanrop.
* JavaScript: Korrigerad felaktig tillstånds rapportering för tal slutar på RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>1\.3.1 för tal-SDK: 2019 – uppdatering för februari

Detta är en fel korrigerings version och påverkar endast det inbyggda/hanterade SDK: n. Den påverkar inte JavaScript-versionen av SDK: n.

**Fel korrigering**

* En minnes läcka har åtgärd ATS när mikrofonen används. Stream-eller File-indata påverkas inte.

## <a name="speech-sdk-130-2019-february-release"></a>1\.3.0 för tal-SDK: 2019 – februari utgåva

**Nya funktioner**

* Tal-SDK: n stöder val av mikrofon för ingångs ljud via klassen AudioConfig. På så sätt kan du strömma ljud data till tal tjänsterna från en mikrofon som inte är standard. Mer information finns i dokumentationen som beskriver valet av [enhet för ljud inspelning](how-to-select-audio-input-devices.md). Den här funktionen är inte tillgänglig ännu från Java Script.
* Talet SDK stöder nu Unity i en beta version. Ge feedback via avsnittet ärende i GitHub- [exempel lagrings plats](https://aka.ms/csspeech/samples). Den här versionen stöder Unity på Windows x86 och x64 (Skriv bords-eller Universell Windows-plattform program) och Android (ARM32/64, x86). Mer information finns i vår [snabb start](quickstart-csharp-unity.md).
* Filen `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (levererades i tidigare versioner) behövs inte längre. Funktionerna är nu integrerade i kärn-SDK: n.


**Exempel**

Följande nya innehåll är tillgängligt i vår [exempel lagrings plats](https://aka.ms/csspeech/samples):

* Ytterligare exempel för AudioConfig. FromMicrophoneInput.
* Ytterligare python-exempel för avsikts igenkänning och översättning.
* Ytterligare exempel för att använda anslutningsobjektet i iOS.
* Ytterligare Java-exempel för översättning med ljud uppspelning.
* Nytt exempel för användning av batch-avskrifter [REST API](batch-transcription.md).

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
* Om du skapar en igenkännings `FromEndpoint` verktyg kan du lägga till parametrar direkt i slut punkts-URL: en. Du `FromEndpoint` kan inte konfigurera tolken genom att använda standard konfigurations egenskaperna.

**Felkorrigeringar**

* Tomt användar namn för proxy och proxy-lösenord hanterades inte korrekt. Om du ställer in proxy-användarnamn och proxy-lösenord till en tom sträng i den här versionen skickas de inte vid anslutning till proxyservern.
* SessionID som skapats av SDK var inte alltid helt slumpmässigt för vissa språk&nbsp;/miljöer. Har lagt till en slumpmässig Generator initiering för att åtgärda problemet.
* Förbättra hanteringen av autentiseringstoken. Om du vill använda en autentiseringstoken anger du i SpeechConfig och lämnar prenumerations nyckeln tom. Skapa sedan tolken som vanligt.
* I vissa fall släpptes inte anslutningsobjektet korrekt. Det här problemet har åtgärdats.
* JavaScript-exemplet har åtgärd ATS för att stödja ljud utdata för översättnings syntes också i Safari.

## <a name="speech-sdk-121"></a>Tal-SDK 1.2.1

Detta är endast en JavaScript-version. Inga funktioner har lagts till. Följande korrigeringar har gjorts:

* Brand i slutet av strömmen vid turn. End, inte i tal. End.
* Åtgärda fel i ljud pumpen som inte schemalades nästa sändning om den aktuella sändningen misslyckades.
* Åtgärda kontinuerlig igenkänning med auth-token.
* Fel korrigering för olika tolkningar/slut punkter.
* Dokumentation om förbättringar.

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

**Förbättringar**

* Förbättringar i den interna tråd användningen, vilket minskar antalet trådar, lås, mutexer.
* Förbättrad fel rapportering/information. I flera fall har fel meddelanden inte spridits ut hela vägen.
* Uppdaterade utvecklings beroenden i Java Script för att använda aktuella moduler.

**Felkorrigeringar**

* Fasta minnes läckor på grund av typ matchnings fel i RecognizeAsync.
* I vissa fall läcker undantag.
* Korrigerar minnes läckor i översättnings händelse argument.
* Åtgärdat låsnings problem vid åter anslutning vid tids krävande sessioner.
* Ett problem som kan leda till saknat slut resultat för misslyckade översättningar har åtgärd ATS.
* C#: Om en asynkron åtgärd inte förväntades i huvud tråden var det möjligt att identifieraren kunde tas bort innan den asynkrona uppgiften slutfördes.
* Java: Ett problem har åtgärd ATS som resulterade i en krasch i Java VM.
* Mål-C: Fast Enum-mappning; RecognizedIntent returnerades i stället för RecognizingIntent.
* JavaScript: Ange Standardutdataformatet till "enkel" i SpeechConfig.
* JavaScript: Ta bort inkonsekvens mellan egenskaper i konfigurationsobjektet i Java Script och andra språk.

**Exempel**

* Uppdaterade och korrigerade flera exempel (till exempel utmatnings röster för översättning osv.).
* Node. js-exempel har lagts till i [exempel lagrings platsen](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>1\.1.0 för tal-SDK

**Nya funktioner**

* Stöd för Android x86/x64.
* Stöd för proxy: I SpeechConfig-objektet kan du nu anropa en funktion för att ange proxyinformation (värdnamn, port, användar namn och lösen ord). Den här funktionen är ännu inte tillgänglig på iOS.
* Förbättrad felkod och meddelanden. Om ett erkännande returneras ett fel detta redan inställt `Reason` (i avbrutna händelse) eller `CancellationDetails` (i igenkänningsresultatet) till `Error`. Avbrutna händelsen innehåller nu två ytterligare medlemmar `ErrorCode` och `ErrorDetails`. Om servern returnerade ytterligare felinformation med rapporterade fel, nu blir tillgängliga i de nya medlemmarna.

**Förbättringar**

* Lägga till ytterligare verifiering i Igenkännande konfigurationen och har lagts till ytterligare ett felmeddelande.
* Förbättrad hantering av sedan länge tystnad i mitten av en ljudfil.
* NuGet-paketet: för .NET Framework-projekt, förhindras att bygga med Platform konfiguration.

**Felkorrigeringar**

* Fasta flera undantag finns i identifierare. Dessutom fångas och konverteras undantag till avbrutna händelser.
* Åtgärda en minnesläcka i egenskapen management.
* En bugg har åtgärdats där en inkommande ljudfil kan krascha identifieraren.
* Ett fel har åtgärdats där händelser gick att ta emot när en session stop-händelse.
* Fasta vissa konkurrenstillstånd i threading.
* Fast en iOS kompatibilitetsproblem som kan resultera i en krasch.
* Stabilitetsförbättringar för Android mikrofon support.
* Ett fel har åtgärdats där en identifierare i JavaScript kan ignorera språket.
* Åtgärdat en bugg som förhindrar att EndpointId (i vissa fall) i JavaScript.
* Ändrade Parametrarnas ordning i AddIntent i JavaScript och har lagts till saknas AddIntent JavaScript-signaturen.

**Exempel**

* Lagt till C++ och C# samplea för pull och push stream-användning i den [exempellagringsplatsen](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>1\.0.1 för tal-SDK

Förbättrad tillförlitlighet och felkorrigeringar:

* Fast potentiella allvarligt fel på grund av konkurrenstillstånd i tar bort Igenkännande
* Fast potentiella allvarligt fel vid Odefinierad egenskaper.
* Har lagts till ytterligare fel och kontroll av parametern.
* Mål-C: Ett allvarligt fel har åtgärd ATS som orsakats av att namnet åsidosätts i NSString.
* Mål-C: Anpassad synlighet för API
* JavaScript: Åtgärdat avseende händelser och deras nytto laster.
* Dokumentation om förbättringar.

I vår [exempellagringsplatsen](https://aka.ms/csspeech/samples), ett nytt stickprov för JavaScript har lagts till.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018-version för september

**Nya funktioner**

* Stöd för Objective-C på iOS. Kolla in våra [Objective-C-Snabbstart för iOS](quickstart-objectivec-ios.md).
* Stöd för JavaScript i webbläsaren. Kolla in våra [JavaScript Snabbstart](quickstart-js-browser.md).

**Större ändringar**

* I den här versionen introduceras ett antal avbrytande ändringar.
  Mer information finns på [den här sidan](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018 – augusti-utgåva

**Nya funktioner**

* UWP-appar som skapats med SDK: N för tal nu kan skicka Windows App Certification Kit (WACK).
  Kolla in den [UWP snabbstarten](quickstart-csharp-uwp.md).
* Stöd för .NET Standard 2.0 på Linux (Ubuntu 16.04 x 64).
* Försök Stöd för Java 8 på Windows (64-bitars) och Linux (Ubuntu 16,04 x64).
  Kolla in den [Java Runtime Environment Snabbstart](quickstart-java-jre.md).

**Funktionella ändring**

* Exponera ytterligare detaljerad information om hur anslutningsfel.

**Större ändringar**

* Java (Android), den `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` funktionen kräver inte längre en sökvägsparameter. Sökvägen identifieras nu automatiskt på alla plattformar som stöds.
* Läsaccessor för egenskapen `EndpointUrl` i Java och C# har tagits bort.

**Felkorrigeringar**

* I Java implementeras ljud syntes resultatet på translation Igenkännande nu.
* Ett fel som kan orsaka inaktiva trådar och ett ökat antal öppna och oanvända sockets har åtgärdats.
* Åtgärdat problem, där en tidskrävande Taligenkänning kunde avsluta mitt överföringen.
* Fast ett konkurrenstillstånd i Igenkännande avstängning.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018 – juli utgåva

**Nya funktioner**

* Stöd för Android-plattform (API 23: Android 6,0 Marshmallow eller högre). Kolla in den [Android Snabbstart](quickstart-java-android.md).
* Stöd för .NET Standard 2.0 på Windows. Kolla in den [Snabbstart för .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Försök Stöd för UWP i Windows (version 1709 eller senare).
  * Kolla in den [UWP snabbstarten](quickstart-csharp-uwp.md).
  * Anteckning: UWP-appar som skapats med tal-SDK: n har inte lyckats med Windows app Certificate Kit (WACK).
* Stöd för tidskrävande erkännande med automatisk återanslutning.

**Funktionella ändringar**

* `StartContinuousRecognitionAsync()` stöd för tidskrävande identifiering.
* Att igenkänningsresultatet innehåller fler fält. De är förskjutning från ljud början och varaktigheten (både i ticken) för den tolkade texten och ytterligare värden som representerar igenkänning av status, till exempel `InitialSilenceTimeout` och `InitialBabbleTimeout`.
* Stöd AuthorizationToken för att skapa factory-instanser.

**Större ändringar**

* Igenkännings händelser: Omatchad händelse typ slogs samman till fel händelsen.
* SpeechOutputFormat i C# har bytt namn till OutputFormat vara justerade med C++.
* Returtypen för vissa metoder för den `AudioInputStream` gränssnittet ändrats något:
   * I Java, den `read` metoden nu returnerar `long` i stället för `int`.
   * I C#, den `Read` metoden nu returnerar `uint` i stället för `int`.
   * I C++ kan den `Read` och `GetFormat` metoder returnerar nu `size_t` i stället för `int`.
* C++: Instanser av ljud inspelnings strömmar kan nu bara skickas som en `shared_ptr`.

**Felkorrigeringar**

* Fast felaktig returvärden i resultatet när `RecognizeAsync()` når sin tidsgräns.
* Media foundation-bibliotek på Windows-beroendet har tagits bort. SDK: N använder nu Core ljud API: er.
* Dokumentations korrigering: Har lagt till en [region](regions.md) sida för att beskriva de regioner som stöds.

**Kända problem**

* Tal-SDK för Android rapportera inte tal syntes resultat för översättning. Det här problemet korrigeras i nästa version.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018 – utgåva från juni

**Funktionella ändringar**

- AudioInputStream

  En identifierare kan nu använda en dataström som ljudkälla. Mer information finns i de relaterade [instruktionsguide](how-to-use-audio-input-streams.md).

- Detaljerad utdataformat

  När du skapar en `SpeechRecognizer`, kan du begära `Detailed` eller `Simple` utdataformat. Den `DetailedSpeechRecognitionResult` innehåller en förtroendepoäng, tolkade texten, råa lexikal format, normaliserade form och normaliserade formuläret med maskerade svordomar.

**Icke-bakåtkompatibel ändring**

- Ändras till `SpeechRecognitionResult.Text` från `SpeechRecognitionResult.RecognizedText` i C#.

**Felkorrigeringar**

- Ett möjligt återanrop ett problem har åtgärdats i lagret USP under avstängning.

- Om en identifierare används en inkommande ljudfil, den håller att filreferens som är längre än nödvändigt.

- Ta bort flera låsningar mellan meddelande pump och identifieraren.

- Startar en `NoMatch` uppnås när tidsgränsen har nåtts för svaret från tjänsten.

- Media foundation-bibliotek på Windows är fördröjning som lästs in. Det här biblioteket krävs för mikrofon endast indata.

- Ladda upp hastigheten för ljuddata är begränsad till om två gånger den ursprungliga ljud hastigheten.

- På Windows, C# .NET-sammansättningar nu är starkt krypterat namn.

- Dokumentationskorrigering: `Region` är nödvändig information för att skapa en identifierare.

Fler exempel har lagts till och uppdateras kontinuerligt. Den senaste uppsättningen exempel, finns det [tal SDK GitHub-lagringsplats med exempel](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018 – maj-utgåva

Den här versionen är den första offentliga förhandsversionen av Cognitive Services tal SDK.
