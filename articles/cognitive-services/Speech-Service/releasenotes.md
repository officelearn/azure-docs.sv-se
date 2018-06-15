---
title: Kognitiva Services tal SDK-dokumentationen | Microsoft Docs
description: Viktig information - ändringar i de senaste versionerna
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0b1559d288380cf3d0c180a225278cc13d22a5d0
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356302"
---
# <a name="release-notes"></a>Viktig information

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Kognitiva Services tal SDK 0.4.0: 2018-juniversionen för

**Funktionsändringar**

- AudioInputStream

  En identifierare kan nu använda en dataström som ljud källa. Detaljerad information finns i den relaterade [instruktioner](how-to-use-audio-input-streams.md).

- Detaljerad utdataformat

  När du skapar en `SpeechRecognizer`, kan du begära `Detailed` eller `Simple` utdataformat. Den `DetailedSpeechRecognitionResult` innehåller ett förtroende poäng, tolkade texten, raw lexikala formuläret, normaliserade form och normaliserade form med maskerade svordomar.

**Att ändra**

- Ändra till `SpeechRecognitionResult.Text` från `SpeechRecognitionResult.RecognizedText` i C#.

**Felkorrigeringar**

- Åtgärda ett återanrop för möjliga problem i USP lager vid avstängningen.

- Om en identifierare används en inkommande ljudfil, den håller att filreferens som är längre än nödvändigt.

- Ta bort flera deadlocks mellan meddelandet pump och tolken.

- Startar en `NoMatch` när tidsgränsen har nåtts för svaret från tjänsten.

- Media foundation-bibliotek i Windows är lästes in. Det här biblioteket är bara krävs för mikrofon indata.

- Överför hastighet för ljuddata är begränsad till två gånger det med ursprungliga ljud hastigheten.

- I Windows, C# .NET-sammansättningar är nu starkt krypterat namn.

- Dokumentationen korrigering: `Region` är nödvändig information för att skapa en identifierare.

Flera exempel har lagts till och uppdateras kontinuerligt. Senaste uppsättning exempel, finns det [tal SDK exempel GitHub-lagringsplatsen](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Kognitiva Services tal SDK 0.2.12733: 2018 kan versionen

Första offentliga förhandsversionen av kognitiva Services tal SDK.
