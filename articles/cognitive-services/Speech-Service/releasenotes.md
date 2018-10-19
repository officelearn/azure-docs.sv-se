---
title: Cognitive Services tal SDK-dokumentation | Microsoft Docs
description: Viktig information – vad som har ändrats i de senaste versionerna
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: 7f637014c5f3e246d67ae051f9304e2f2536d725
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427390"
---
# <a name="release-notes"></a>Viktig information

## <a name="cognitive-services-speech-sdk-101"></a>Cognitive Services tal SDK 1.0.1

Förbättrad tillförlitlighet och felkorrigeringar:

* Fast potentiella allvarligt fel på grund av konkurrenstillstånd i tar bort Igenkännande
* Fast potentiella allvarligt fel vid Odefinierad egenskaper.
* Har lagts till ytterligare fel och kontroll av parametern.
* Mål-C: fast möjliga allvarligt fel på grund av att namnet åsidosätta i NSString.
* Mål-C: justeras synligheten för API: et
* JavaScript: Fasta om händelser och sina nyttolaster.
* Dokumentation om förbättringar.

I vår [exempellagringsplatsen](https://aka.ms/csspeech/samples), ett nytt stickprov för JavaScript har lagts till.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services tal SDK 1.0.0: September 2018 versionen

**Nya funktioner**

* Stöd för Objective-C på iOS. Kolla in våra [Objective-C-Snabbstart för iOS](quickstart-objectivec-ios.md).
* Stöd för JavaScript i webbläsaren. Kolla in våra [JavaScript Snabbstart](quickstart-js-browser.md).

**Större ändringar**

* Med den här versionen införs ett antal icke-bakåtkompatibla ändringar.
  Kontrollera [den här sidan](https://aka.ms/csspeech/breakingchanges_1_0_0) mer information.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services tal SDK 0.6.0: augusti 2018 versionen

**Nya funktioner**

* UWP-appar som skapats med SDK: N för tal nu kan skicka Windows App Certification Kit (WACK).
  Kolla in den [UWP snabbstarten](quickstart-csharp-uwp.md).
* Stöd för .NET Standard 2.0 på Linux (Ubuntu 16.04 x 64).
* Experimentella: Stöd för Java 8 i Windows (64-bitars) och Linux (Ubuntu 16.04 x 64).
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
* Experimentella: Stöd för UWP på Windows (version 1709 eller senare).
  * Kolla in den [UWP snabbstarten](quickstart-csharp-uwp.md).
  * Obs: UWP-appar som skapats med SDK: N för tal inte ännu skickar Windows App Certification Kit (WACK).
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
* Dokumentationskorrigering: lagt till en [regioner](regions.md) sidan att beskriva regionerna som stöds.

**Kända problem**

* Tal-SDK för Android rapportera inte tal syntes resultat för översättning. Det här problemet korrigeras i nästa version.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services tal SDK 0.4.0: 2018-juniversionen för

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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services tal SDK 0.2.12733: maj 2018 versionen

Den här versionen är den första offentliga förhandsversionen av Cognitive Services tal SDK.
