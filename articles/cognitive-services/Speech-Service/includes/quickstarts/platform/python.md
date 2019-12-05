---
title: 'Snabb start: Speech SDK för python Platform setup – tal service'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform för att använda python med Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/09/2019
ms.author: erhopf
ms.openlocfilehash: 095c591f63cb228b48681ffc67499820631bbaf5
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816463"
---
Den här guiden visar hur du installerar [tal-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för python.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Operativsystem som stöds

- Python Speech SDK-paketet är tillgängligt för dessa operativsystem:
  - Windows: x64 och x86
  - Mac: macOS X version 10,12 eller senare
  - Linux: Ubuntu 16,04, Ubuntu 18,04, Debian 9 på x64

## <a name="prerequisites"></a>Krav

- Linux-plattformar som stöds kräver vissa bibliotek installerade (`libssl` för Secure Sockets Layer-support och `libasound2` för ljud support). Se distributionen nedan för de kommandon som behövs för att installera rätt versioner av dessa bibliotek.

  - På Ubuntu kör du följande kommandon för att installera de nödvändiga paketen:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - På Debian 9 kör du följande kommandon för att installera de nödvändiga paketen:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- I Windows behöver du [Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) för din plattform. Observera att installationen för första gången kan kräva att du startar om Windows innan du fortsätter med den här guiden.
- Slutligen behöver du [Python 3,5 eller senare](https://www.python.org/downloads/). Kontrol lera installationen genom att öppna en kommando tolk och skriva kommandot `python --version` och kontrol lera resultatet. Om den är korrekt installerad får du svaret "python 3.5.1" eller liknande.

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Installera tal-SDK med Visual Studio Code

1. Hämta och installera den senaste versionen av [python](https://www.python.org/downloads/) som stöds för din plattform, 3,5 eller senare.
   - Windows-användare ser till att välja "Lägg till python i sökvägen" under installations processen.
1. Ladda ned och installera [Visual Studio Code](https://code.visualstudio.com/Download).
1. Öppna Visual Studio Code och installera Python-tillägget. Välj **Arkiv** > **Inställningar** > **Tillägg** på menyn. Sök efter **python** och klicka på **Installera**.

   ![Installera Python-tillägget](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. I Visual Studio Code ska du också installera python-paketet för tal-SDK från den integrerade kommando raden:
   1. Öppna en Terminal (från de nedrullningsbara menyerna, **terminal** > **ny terminal**)
   1. I terminalen som öppnas anger du kommandot `python -m pip install azure-cognitiveservices-speech`

Då är det dags att börja koda till tal-SDK i python och kan gå vidare till [Nästa steg](#next-steps) nedan. Om du är nybörjare på Visual Studio Code kan du läsa mer i [dokumentationen för Visual Studio Code](https://code.visualstudio.com/docs). Mer information om Visual Studio Code och python finns i [själv studie kursen om Visual Studio Code python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="install-the-speech-sdk-using-the-command-line"></a>Installera tal-SDK med hjälp av kommando raden

Om du inte använder Visual Studio Code installerar följande kommando python-paketet från [pypi](https://pypi.org/) för tal-SDK: n. För användare av Visual Studio Code går du vidare till nästa underordnade avsnitt.

```sh
pip install azure-cognitiveservices-speech
```

Om du använder macOS kan du behöva köra följande kommando för att få `pip` kommandot ovan att fungera:

```sh
python3 -m pip install --upgrade pip
```

När du har använt `pip` för att installera `azure-cognitiveservices-speech`kan du använda tal-SDK: n genom att importera namn området till dina python-projekt. Exempel:

```py
import azure.cognitiveservices.speech as speechsdk
```

Detta visas mer detaljerat i kod exemplen som visas i [Nästa steg](#next-steps) nedan.

## <a name="support-and-updates"></a>Support och uppdateringar

Uppdateringar av Python Speech SDK-paketet distribueras via PyPI och visas i [Viktig information](~/articles/cognitive-services/speech-service/releasenotes.md).
Om en ny version är tillgänglig kan du uppdatera till den med kommandot `pip install --upgrade azure-cognitiveservices-speech`.
Kontrollera vilken version som är installerad genom att granska variabeln `azure.cognitiveservices.speech.__version__`.

Om du har problem eller saknar en funktion kan du ta en titt på [alternativen för support och hjälp](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]