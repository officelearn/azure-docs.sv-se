---
title: Viktig information – Speech Services
titlesuffix: Azure Cognitive Services
description: Se en pågående logg över nya funktioner, förbättringar, felkorrigeringar och kända problem för Azure Speech Services.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 4/5/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: e40d9a2ac0e95ae72aed927f8a527eb1e092079d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480477"
---
# <a name="release-notes"></a>Viktig information

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Det här är en version med endast JavaScript. Inga funktioner har lagts till. Följande har gjorts:

* Förhindra att webpack laddas https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: 2019 April versionen

**Nya funktioner** 

* SDK: N stöder nu tjänsten text till tal som en beta-versionen. Den stöds på Windows och Linux-Desktop från C++ och C#. Mer information finns i [text till tal översikt](text-to-speech.md#get-started-with-text-to-speech).
* SDK stöder nu MP3- och Opus/Ogg ljudfiler som stream indatafiler. Den här funktionen är bara tillgängliga på Linux från C++ och C# och håller på att beta (Mer information om [här](how-to-use-compressed-audio-input-streams.md)).
* Tal-SDK för Java, .NET core, C++ och Objective-C fått macOS support. Objective-C-stöd för macOS är för närvarande i beta.
* iOS: Tal-SDK för iOS (Objective-C) är nu också att publiceras som en CocoaPod.
* JavaScript: Stöd för icke-standard mikrofon som en enhet.
* JavaScript: Stöd för proxy för Node.js.

**Exempel**

* Exempel för att använda tal-SDK med C++ och med Objective-C på macOS har lagts till.
* Exempel som visar användningen av text till tal-tjänsten har lagts till.

**Förbättringar av / ändras**

* Python: Ytterligare egenskaper för resultat visas nu den `properties` egenskapen.
* För ytterligare support för utveckling och felsökning kan du omdirigera SDK loggning och diagnostik information till en loggfil (Mer information om [här](how-to-use-logging.md)).
* JavaScript: Förbättra bearbetningsprestanda för ljud.

**Felkorrigeringar**

* Mac/iOS: En bugg som ledde till långa väntetider när inte gick att upprätta en anslutning till tal-tjänst har åtgärdats.
* Python: förbättra felhantering för argument i Python-återanrop.
* JavaScript: Fast fel status rapportering för tal upphörde RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: 2019 februari uppdatering

Detta är en felkorrigering version och påverkar endast intern/hanteras SDK. Det påverkar inte JavaScript-versionen av SDK.

**Felkorrigering**

* Fast en minnesläcka när med mikrofon som indata. Stream baserat eller fil som indata påverkas inte.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: 2019 februariversionen

**Nya funktioner**

* Tal SDK stöder valet av inkommande mikrofonen via AudioConfig-klassen. Detta kan du strömma ljud data till Speech Services från en icke-standard mikrofon. Mer information finns i dokumentationen som beskriver [ingående ljud enhet](how-to-select-audio-input-devices.md). Detta är inte ännu tillgängliga från JavaScript.
* Tal-SDK: N har nu stöd för Unity i en betaversion. Ge feedback via avsnittet problemet i den [GitHub-exempellagringsplats](https://aka.ms/csspeech/samples). Den här versionen stöder Unity på Windows x86 och x64 (desktop eller Universal Windows Platform-program) och Android (ARM32/64, x86). Mer information finns i vår [Unity Snabbstart](quickstart-csharp-unity.md).
* Filen `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (levereras i tidigare versioner) behövs inte längre. Funktionerna är nu integrerade i core SDK.


**Exempel**

Följande nya innehåll finns i vår [exempellagringsplatsen](https://aka.ms/csspeech/samples):

* Ytterligare exempel för AudioConfig.FromMicrophoneInput.
* Ytterligare Python-exempel för taligenkänning och översättning.
* Ytterligare exempel för att använda anslutningsobjektet i iOS.
* Ytterligare Java-exempel för översättning med ljuduppspelning.
* Nya exempel för användning av den [Batch avskrift REST API](batch-transcription.md).

**Förbättringar av / ändras**

* Python
  * Förbättrad parametern verifiering och felmeddelanden i SpeechConfig.
  * Lägg till stöd för anslutningsobjektet.
  * Stöd för 32-bitarsversionen av Python (x86) på Windows.
  * Tal-SDK för Python ligger utanför beta.
* iOS
  * SDK är nu byggt mot iOS SDK version 12.1.
  * SDK: N har nu stöd för iOS version 9.2 och senare.
  * Förbättra referensdokumentation och åtgärda flera egenskapsnamn.
* JavaScript
  * Lägg till stöd för anslutningsobjektet.
  * Lägg till typen definitionsfiler för tillsammans JavaScript
  * Inledande stöd och implementering av frasen tips.
  * Returnera egenskapssamlingen med tjänst-JSON för taligenkänning
* Windows-dll: er innehåller nu en versionsresurs.
* Om du skapar en identifierare `FromEndpoint` du kan lägga till parametrar direkt till slutpunkts-URL. Med hjälp av `FromEndpoint` du kan inte konfigurera Igenkännande via standardkonfigurationen egenskaper.

**Felkorrigeringar**

* Tom proxy användarnamn och lösenord för proxy hanterades inte korrekt. Med den här versionen om du ställer in proxy-användarnamn och lösenord för proxy till en tom sträng, skickas de inte när du ansluter till proxyn.
* Sessions-ID är skapat av SDK kan inte alltid slumpvis för vissa språk&nbsp;/ miljöer. Lägga till slumpmässiga generator initieringen för att åtgärda detta.
* Förbättra hanteringen av autentiseringstoken. Om du vill använda en autentiseringstoken anger i SpeechConfig och låter prenumerationsnyckeln tom. Skapa sedan identifieraren som vanligt.
* I vissa fall anslutningen inte var objektet är korrekt. Problemet har åtgärdats.
* JavaScript-exemplet åtgärdades för ljuduppspelning för översättning syntes också på Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Det här är en version med endast JavaScript. Inga funktioner har lagts till. Följande har gjorts:

* Utlöses slut på strömmen vid turn.end, inte på speech.end.
* Åtgärda fel i ljud ingenjör att inte schemat bredvid skickade om aktuellt utskicket misslyckades.
* Åtgärda kontinuerlig taligenkänning med auth-token.
* Buggfix för olika Igenkännande / slutpunkter.
* Dokumentation om förbättringar.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: 2018-December versionen

**Nya funktioner**

* Python
  * Beta-versionen av Python-stöd (3.5 och senare) är tillgänglig i den här versionen. Mer information finns i here](quickstart-python.md).
* JavaScript
  * Tal-SDK för JavaScript har varit öppen källkod. Källkoden finns på [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Vi har nu stöd för Node.js, mer information finns [här](quickstart-js-node.md).
  * Begränsning av lösenordslängd för ljud sessioner har tagits bort återanslutning sker automatiskt under omslaget.
* Anslutningsobjektet
  * Du kan komma åt ett anslutningsobjekt från Igenkännande. Det här objektet kan du uttryckligen initiera tjänstanslutning och prenumerera för att ansluta och koppla från händelser.
    (Detta är inte ännu tillgängliga från JavaScript- och Python.)
* Stöd för Ubuntu 18.04.
* Android
  * Aktiverade ProGuard support under APK generation.

**Förbättringar**

* Förbättringar i användningen av interna tråd och minska antalet trådar, lås, mutexer.
* Förbättrad felrapportering / information. I flera fall har felmeddelanden inte spridits ut.
* Uppdatera utveckling beroenden i JavaScript för att använda uppdaterade moduler.

**Felkorrigeringar**

* Fast minnesläckor på grund av ett typmatchningsfel i RecognizeAsync.
* I vissa fall har undantag hamnar.
* Åtgärda minnesläcka i translation händelse argument.
* Fast låsningsproblem på återansluta i långa kör sessioner.
* Ett problem som kan leda till saknade slutresultatet för misslyckade översättningar har åtgärdats.
* C#: Om en asynkron åtgärd inte har slutförts i huvudtråden, var det möjligt identifieraren kan tas bort innan den asynkron uppgiften slutfördes.
* Java: Åtgärdat problem vilket resulterar i en krasch på Java-VM.
* Objective-C: Fast enum mappningen; RecognizedIntent returnerades i stället för RecognizingIntent.
* JavaScript: Ange Standardutdataformatet till enkel i SpeechConfig.
* JavaScript: Tar bort inkonsekvens mellan egenskaper för konfigurationsobjekt i JavaScript och andra språk.

**Exempel**

* Uppdaterade och fasta flera exempel (till exempel utdata röster för översättning osv.).
* Lagt till Node.js-exempel i den [exempellagringsplatsen](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nya funktioner**

* Stöd för Android x86/x64.
* Stöd för proxy: Du kan nu anropa en funktion för att ställa in proxyinformation (värdnamn, port, användarnamn och lösenord) i SpeechConfig-objektet. Den här funktionen är ännu inte tillgänglig på iOS.
* Förbättrad felkod och meddelanden. Om ett erkännande returneras ett fel detta redan inställt `Reason` (i avbrutna händelse) eller `CancellationDetails` (i igenkänningsresultatet) till `Error`. Avbrutna händelsen innehåller nu två ytterligare medlemmar `ErrorCode` och `ErrorDetails`. Om servern returnerade ytterligare felinformation med rapporterade fel, nu blir tillgängliga i de nya medlemmarna.

**Förbättringar**

* Lägga till ytterligare verifiering i Igenkännande konfigurationen och har lagts till ytterligare ett felmeddelande.
* Förbättrad hantering av sedan länge tystnad i mitten av en ljudfil.
* NuGet-paketet: för .NET Framework-projekt, förhindras att bygga med Platform konfiguration.

**Felkorrigeringar**

* Fasta flera undantag finns i identifierare. Dessutom kan undantag påträffades och konverteras till avbruten händelse.
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

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Förbättrad tillförlitlighet och felkorrigeringar:

* Fast potentiella allvarligt fel på grund av konkurrenstillstånd i tar bort Igenkännande
* Fast potentiella allvarligt fel vid Odefinierad egenskaper.
* Har lagts till ytterligare fel och kontroll av parametern.
* Objective-C: Fast möjliga allvarligt fel på grund av att namnet åsidosätta i NSString.
* Objective-C: Justerade synligheten för API: et
* JavaScript: Fasta om händelser och sina nyttolaster.
* Dokumentation om förbättringar.

I vår [exempellagringsplatsen](https://aka.ms/csspeech/samples), ett nytt stickprov för JavaScript har lagts till.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services tal SDK 1.0.0: Versionen av September 2018

**Nya funktioner**

* Stöd för Objective-C på iOS. Kolla in våra [Objective-C-Snabbstart för iOS](quickstart-objectivec-ios.md).
* Stöd för JavaScript i webbläsaren. Kolla in våra [JavaScript Snabbstart](quickstart-js-browser.md).

**Större ändringar**

* Den här versionen erbjuder införs ett antal icke-bakåtkompatibla ändringar.
  Kontrollera [den här sidan](https://aka.ms/csspeech/breakingchanges_1_0_0) mer information.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services tal SDK 0.6.0: Versionen av augusti 2018

**Nya funktioner**

* UWP-appar som skapats med SDK: N för tal nu kan skicka Windows App Certification Kit (WACK).
  Kolla in den [UWP snabbstarten](quickstart-csharp-uwp.md).
* Stöd för .NET Standard 2.0 på Linux (Ubuntu 16.04 x 64).
* Experimentella: Stöd Java 8 i Windows (64-bitars) och Linux (Ubuntu 16.04 x 64).
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

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services tal SDK 0.5.0: 2018-juliversionen

**Nya funktioner**

* Stöd för Android-plattformen (API-23: Android 6.0 Marshmallow eller senare). Kolla in den [Android Snabbstart](quickstart-java-android.md).
* Stöd för .NET Standard 2.0 på Windows. Kolla in den [Snabbstart för .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Experimentella: Stöder UWP på Windows (version 1709 eller senare).
  * Kolla in den [UWP snabbstarten](quickstart-csharp-uwp.md).
  * Obs! UWP-appar som skapats med SDK: N för tal ännu skickar inte Windows App Certification Kit (WACK).
* Stöd för tidskrävande erkännande med automatisk återanslutning.

**Funktionella ändringar**

* `StartContinuousRecognitionAsync()` stöd för tidskrävande identifiering.
* Att igenkänningsresultatet innehåller fler fält. De är förskjutning från ljud början och varaktigheten (både i ticken) för den tolkade texten och ytterligare värden som representerar igenkänning av status, till exempel `InitialSilenceTimeout` och `InitialBabbleTimeout`.
* Stöd AuthorizationToken för att skapa factory-instanser.

**Större ändringar**

* Igenkänning av händelser: NoMatch händelsetyp skulle samman i felhändelsen.
* SpeechOutputFormat i C# har bytt namn till OutputFormat vara justerade med C++.
* Returtypen för vissa metoder för den `AudioInputStream` gränssnittet ändrats något:
   * I Java, den `read` metoden nu returnerar `long` i stället för `int`.
   * I C#, den `Read` metoden nu returnerar `uint` i stället för `int`.
   * I C++ kan den `Read` och `GetFormat` metoder returnerar nu `size_t` i stället för `int`.
* C++: Instanser av inkommande ljudströmmar nu kan skickas endast som en `shared_ptr`.

**Felkorrigeringar**

* Fast felaktig returvärden i resultatet när `RecognizeAsync()` når sin tidsgräns.
* Media foundation-bibliotek på Windows-beroendet har tagits bort. SDK: N använder nu Core ljud API: er.
* Dokumentationskorrigering: Lagt till en [regioner](regions.md) sidan att beskriva regionerna som stöds.

**Kända problem**

* Tal-SDK för Android rapportera inte tal syntes resultat för översättning. Det här problemet korrigeras i nästa version.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services tal SDK 0.4.0: Juniversionen för 2018

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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018-maj versionen

Den här versionen är den första offentliga förhandsversionen av Cognitive Services tal SDK.
