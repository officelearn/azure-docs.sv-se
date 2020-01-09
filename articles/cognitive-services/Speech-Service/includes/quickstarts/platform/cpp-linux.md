---
title: 'Snabb start: tal C++ SDK (Linux) plattforms konfiguration – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform C++ för Linux med Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 6bb647273467a07786413ff4ea30cda836b7cb1b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468425"
---
Den här guiden visar hur du installerar [talet SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systemkrav

Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)

## <a name="prerequisites"></a>Krav

För att slutföra den här snabb starten behöver du:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* Linux-plattformar som stöds kräver vissa bibliotek installerade (`libssl` för Secure Sockets Layer-support och `libasound2` för ljud support). Se distributionen nedan för de kommandon som behövs för att installera rätt versioner av dessa bibliotek.

   * I Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * På Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

## <a name="install-speech-sdk"></a>Installera Speech SDK

Speech SDK för Linux kan användas för att bygga både 64-bitars och 32-bitars program. De nödvändiga biblioteken och huvudfilerna kan laddas ned som en tar-fil från https://aka.ms/csspeech/linuxbinary.

Ladda ned och installera SDK enligt följande:

1. Välj en katalog som Speech SDK-filerna ska extraheras till och ange miljövariabeln `SPEECHSDK_ROOT` så att den pekar på den katalogen. Den här variabeln gör det enkelt att referera till katalogen i framtida kommandon. Exempel: Om du vill använda katalogen `speechsdk` i arbetskatalogen använder du ett kommando som liknar följande:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Skapa katalogen om den inte redan finns.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Ladda ned och extrahera arkivet `.tar.gz` som innehåller Speech SDK-binärfilerna:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Validera innehållet i toppnivåkatalogen i det extraherade paketet:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Kataloglistan ska innehålla meddelande från tredje part och licensfiler, samt en `include`-katalog som innehåller huvudfiler (`.h`) och en `lib`-katalog som innehåller bibliotek.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

Nu kan du gå vidare till [Nästa steg](#next-steps) nedan.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]
