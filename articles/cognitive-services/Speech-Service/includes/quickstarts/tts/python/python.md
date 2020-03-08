---
title: 'Snabb start: syntetisera tal, python-tal-tjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att syntetisera tal i python med hjälp av tal-SDK
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 19/14/2020
ms.author: dapine
ms.openlocfilehash: 58f2b3693b924197dda1e396ae71192776ca276e
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78924789"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md)

## <a name="support-and-updates"></a>Support och uppdateringar

Uppdateringar av Python Speech SDK-paketet distribueras via PyPI och visas i [Viktig information](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Om en ny version är tillgänglig kan du uppdatera till den med kommandot `pip install --upgrade azure-cognitiveservices-speech`.
Kontrollera vilken version som är installerad genom att granska variabeln `azure.cognitiveservices.speech.__version__`.

Om du har problem eller saknar en funktion kan du ta en titt på [alternativen för support och hjälp](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Skapa ett Python-program som använder Speech SDK

### <a name="run-the-sample"></a>Kör exemplet

Du kan kopiera [exempelkoden](#sample-code) till en källfil från den här snabbstarten `quickstart.py` och köra den i din IDE eller i konsolen:

```sh
python quickstart.py
```

Eller så kan du ladda ned den här snabbstartssjälvstudien som en [Jupyter](https://jupyter.org) Notebook från [exempellagringsplatsen för Speech SDK](https://aka.ms/csspeech/samples) och köra den som en notebook-fil.

### <a name="sample-code"></a>Exempelkod

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installera och använda Speech SDK med Visual Studio Code

1. Hämta och installera en 64-bitars version av [python](https://www.python.org/downloads/), 3,5 till 3,8, på din dator.
1. Ladda ned och installera [Visual Studio Code](https://code.visualstudio.com/Download).
1. Öppna Visual Studio Code och installera Python-tillägget. Välj **Arkiv** > **Inställningar** > **Tillägg** på menyn. Sök efter **Python**.

   ![Installera Python-tillägget](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Skapa en mapp att lagra projektet i. Ett exempel är med hjälp av Windows Explorer.
1. Från Visual Studio Code väljer du ikonen **Arkiv**. Öppna sedan den mapp du skapade.

   ![Öppna en mapp](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Skapa en ny Python-källfil `speechsdk.py` genom att välja den nya filikonen.

   ![Skapa en fil](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Kopiera, klistra in och spara [Python-koden](#sample-code) till den nyskapade filen.
1. Infoga prenumerationsinformationen för taltjänsten.
1. Om en Python-tolk har valts visas den på statusfältets vänstra sida, längst ner i fönstret.
   Annars kan du ta fram en lista över tillgängliga Python-tolkar. Öppna kommando paletten (Ctrl + Shift + P) och ange **python: Välj tolk**. Välj en som passar.
1. Du kan installera Speech SDK Python-paketet i Visual Studio Code. Gör det om det inte är installerat än för Python-tolken som du valt.
   Öppna en terminal för att installera Speech SDK-paketet. Öppna kommando rads verktyget igen (Ctrl + Shift + P) och ange **Terminal: skapa ny integrerad Terminal**.
   I terminalen som öppnas anger du kommandot `python -m pip install azure-cognitiveservices-speech`, eller lämpligt kommando för ditt system.
1. Kör exempelkoden genom att högerklicka någonstans i redigeraren. Välj **Kör Python-fil i Terminal**.
   Skriv lite text när du uppmanas till det. Det syntetiserade ljudet spelas upp strax efteråt.

   ![Köra ett exempel](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

Om det finns problem med att följa dessa anvisningar finns den mer omfattande [självstudien Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röst exempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
