---
title: 'Snabbstart: Känna igen tal från en mikrofon, Python - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett tal till text-konsolprogram med hjälp av Speech SDK för Python. När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: a2d5614aeeedb88cd9b2c41baed317761443d17c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924720"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md)
> * Se till att du har tillgång till en mikrofon för ljudinspelning

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

Eller så kan du ladda ned den här snabbstartssjälvstudien som en [Jupyter](https://jupyter.org) Notebook från [exempellagringsplatsen för Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) och köra den som en notebook-fil.

### <a name="sample-code"></a>Exempelkod

> [!NOTE]
> Tal-SDK kommer som standard att känna igen med hjälp av en-us för språket, se [Ange källspråk för tal till text](../../../../how-to-specify-source-language.md) för information om hur du väljer källspråk.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installera och använda Speech SDK med Visual Studio Code

1. Hämta och installera en 64-bitarsversion av [Python](https://www.python.org/downloads/), 3,5 till 3,8, på datorn.
1. Ladda ned och installera [Visual Studio Code](https://code.visualstudio.com/Download).
1. Öppna Visual Studio Code och installera Python-tillägget. Välj **File** > **Filinställningar** > **Tillägg** på menyn. Sök efter **Python**.

   ![Installera Python-tillägget](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Skapa en mapp att lagra projektet i. Ett exempel är med hjälp av Windows Explorer.
1. Från Visual Studio Code väljer du ikonen **Arkiv**. Öppna sedan den mapp du skapade.

   ![Öppna en mapp](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Skapa en ny Python-källfil `speechsdk.py` genom att välja den nya filikonen.

   ![Skapa en fil](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Kopiera, klistra in och spara [Python-koden](#sample-code) till den nyskapade filen.
1. Infoga prenumerationsinformationen för taltjänsten.
1. Om en Python-tolk har valts visas den på statusfältets vänstra sida, längst ner i fönstret.
   Annars kan du ta fram en lista över tillgängliga Python-tolkar. Öppna kommandopaletten (Ctrl+Skift+P) och ange **Python: Välj tolk**. Välj en som passar.
1. Du kan installera Speech SDK Python-paketet i Visual Studio Code. Gör det om det inte är installerat än för Python-tolken som du valt.
   Öppna en terminal för att installera Speech SDK-paketet. Ta fram kommandopaletten igen (Ctrl+Skift+P) och ange **Terminal: Skapa ny integrerad terminal**.
   I terminalen som öppnas anger du kommandot `python -m pip install azure-cognitiveservices-speech`, eller lämpligt kommando för ditt system.
1. Kör exempelkoden genom att högerklicka någonstans i redigeraren. Välj **Kör Python-fil i Terminal**.
   Säg några få ord när du uppmanas. Den transkriberade texten visas strax därefter.

   ![Köra ett exempel](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run.png)

Om det finns problem med att följa dessa anvisningar finns den mer omfattande [självstudien Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
