---
title: 'Snabb start: tal SDK C++ (macOS) plattforms konfiguration – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform för C++ på macOS med Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: d0f9bc44145f75d7c3b503fffe43042c650025f1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "75467699"
---
Den här guiden visar hur du installerar [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för C++ på MacOS 10,13 och senare.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systemkrav

macOS 10,13 och senare

## <a name="install-speech-sdk"></a>Installera Speech SDK

1. Välj en katalog som Speech SDK-filerna ska extraheras till och ange miljövariabeln `SPEECHSDK_ROOT` så att den pekar på den katalogen. Den här variabeln gör det enkelt att referera till katalogen i framtida kommandon. Exempel: Om du vill använda katalogen `speechsdk` i arbetskatalogen använder du ett kommando som liknar följande:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Skapa katalogen om den inte redan finns.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Hämta och extrahera `.zip` arkivet som innehåller talet SDK Framework:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Validera innehållet i toppnivåkatalogen i det extraherade paketet:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Katalog listan ska innehålla meddelanden från tredje part och licensfiler, samt en `MicrosoftCognitiveServicesSpeech.framework` katalog.

Nu kan du gå vidare till [Nästa steg](#next-steps) nedan.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]
