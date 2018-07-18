---
title: Cognitive Services tal SDK-dokumentation | Microsoft Docs
description: Viktig information – vad som har ändrats i de senaste versionerna
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: wolfma
ms.openlocfilehash: 50a8c183bd7f2711847ce6d0acade4cb498ef2fc
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116103"
---
# <a name="release-notes"></a>Viktig information

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services tal SDK 0.5.0: 2018-juliversionen

**Nya funktioner**

* Stöd för Android-plattformen (API-23: Android 6.0 Marshmallow eller senare).
  Kolla in den [Android Snabbstart](quickstart-java-android.md).
* Stöd för .NET Standard 2.0 på Windows.
  Kolla in den [Snabbstart för .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Experimentella: Stöd för UWP på Windows (version 1709 eller senare)
  * Kolla in våra [UWP snabbstarten](quickstart-csharp-uwp.md).
  * Obs: UWP-appar som skapats med SDK: N för tal inte ännu skickar Windows App Certification Kit (WACK).
* Stöd för tidskrävande erkännande med automatisk återanslutning.

**Funktionella ändringar**

* `StartContinuousRecognitionAsync()` har stöd för tidskrävande taligenkänning
* Att igenkänningsresultatet innehåller fler fält: förskjutning från ljud början och varaktigheten (både i ticken) för den tolkade texten ytterligare värden som representerar igenkänning av status, t.ex. `InitialSilenceTimeout`, `InitialBabbleTimeout`.
* Stöd AuthorizationToken för att skapa factory-instanser.

**Större ändringar**

* Igenkänning av händelser: NoMatch händelsetyp sammanfogas i felhändelsen.
* SpeechOutputFormat i C# har bytt namn till OutputFormat att hålla justerade med C++.
* Returtypen för vissa metoder för den `AudioInputStream` gränssnittet något ändrade:
   * I Java, den `read` metoden nu returnerar `long` i stället för `int`.
   * I C#, den `Read` metoden nu returnerar `uint` i stället för `int`.
   * I C++ kan den `Read` och `GetFormat` metoder returnerar nu `size_t` i stället för `int`.
* C++: instanser av inkommande ljudströmmar kan nu endast skickas som en `shared_ptr`.

**Felkorrigeringar**

* Fast felaktig returvärden i resultatet när `RecognizeAsync()` når sin tidsgräns.
* Media foundation-bibliotek på Windows-beroendet tas bort. SDK: N använder Core ljud API: er.
* Dokumentationskorrigering: lagt till en region sida för att beskriva vad är regionerna som stöds.

**Kända problem**

* Tal-SDK för Android rapporterar inte tal syntes resultat för översättning.
  Detta kommer att åtgärdas i nästa version.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services tal SDK 0.4.0: 2018-juniversionen för

**Funktionella ändringar**

- AudioInputStream

  En identifierare kan nu använda en dataström som ljudkälla. Detaljerad information finns i de relaterade [instruktionsguide](how-to-use-audio-input-streams.md).

- Detaljerad utdataformat

  När du skapar en `SpeechRecognizer`, kan du begära `Detailed` eller `Simple` utdataformat. Den `DetailedSpeechRecognitionResult` innehåller en förtroendepoäng, tolkade texten, råa lexikal format, normaliserade form och normaliserade formuläret med maskerade svordomar.

**Icke-bakåtkompatibel ändring**

- Ändra till `SpeechRecognitionResult.Text` från `SpeechRecognitionResult.RecognizedText` i C#.

**Felkorrigeringar**

- Åtgärda ett problem med möjliga återanrop i USP layer under avstängning.

- Om en identifierare används en inkommande ljudfil, den håller att filreferens som är längre än nödvändigt.

- Ta bort flera låsningar mellan meddelande pump och Igenkännande.

- Startar en `NoMatch` uppnås när tidsgränsen har nåtts för svaret från tjänsten.

- Media foundation-bibliotek på Windows är lästes in. Det här biblioteket är endast krävs för mikrofon indata.

- Ladda upp hastigheten för ljuddata är begränsad till om två gånger den ursprungliga ljud hastigheten.

- På Windows, C# .NET-sammansättningar är nu starkt krypterat namn.

- Dokumentationskorrigering: `Region` är nödvändig information för att skapa en identifierare.

Fler exempel har lagts till och uppdateras kontinuerligt. Den senaste uppsättningen exempel, finns det [tal SDK exempel GitHub-lagringsplatsen](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services tal SDK 0.2.12733: maj 2018 versionen

Den första offentliga förhandsversionen av Cognitive Services tal SDK.
