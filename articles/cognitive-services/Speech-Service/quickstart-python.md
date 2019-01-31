---
title: 'Snabbstart: Taligenkänning, Python – Speech Services'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett tal till text-konsolprogram med hjälp av Speech SDK för Python. När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: chlandsi
ms.openlocfilehash: 1c7287b919c46ead4f961aff769da5c8bf68cc6f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226578"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Snabbstart: Taligenkänning med Speech SDK för Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Den här artikeln visar hur du använder taltjänsten via Speech SDK för Python. Den visar hur du kan känna igen tal från mikrofonens indata.

## <a name="prerequisites"></a>Nödvändiga komponenter

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En Azure-prenumerationsnyckel för taltjänsten. [Skaffa en kostnadsfritt](get-started.md).
* [Python 3.5 (64-bitars)](https://www.python.org/downloads/) eller senare.
* Python Speech SDK-paketet finns tillgängligt för Windows (x64), Mac (macOS X version 10.12 eller senare) och Linux (Ubuntu 16.04 eller 18.04 på x64).
* I Ubuntu kör du följande kommandon för att installera nödvändiga paket:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* I Windows behöver du även [Microsoft Visual C++ Redistributable för Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) för din plattform.

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Python Speech SDK-paketet för Cognitive Services kan installeras från [PyPI](https://pypi.org/) med följande kommando i kommandoraden:

```sh
pip install azure-cognitiveservices-speech
```

Den aktuella versionen av Cognitive Services Speech SDK är `1.2.0`.

## <a name="support-and-updates"></a>Support och uppdateringar

Uppdateringar av Python Speech SDK-paketet kommer att distribueras via PyPI och visas på sidan [Viktig information](./releasenotes.md).
Om en ny version är tillgänglig kan du uppdatera till den med kommandot `pip install --upgrade azure-cognitiveservices-speech`.
Du kan kontrollera vilken version som är installerad genom att granska variabeln `azure.cognitiveservices.speech.__version__`.

Om du har problem eller saknar en funktion kan du titta på [supportsidan](./support.md).

## <a name="create-a-python-application-using-the-speech-sdk"></a>Skapa ett Python-program med hjälp av Speech SDK

### <a name="run-the-sample"></a>Kör exemplet

Du kan antingen kopiera [koden](#quickstart-code) till en källfil från den här snabbstarten `quickstart.py` och köra den i din IDE eller i konsolen

```sh
python quickstart.py
```

eller så kan du ladda ned den här snabbsjälvstudien som en [Jupyter](https://jupyter.org) Notebook från [lagringsplatsen för Cognitive Services talexempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) och köra den som en notebook-fil.

### <a name="sample-code"></a>Exempelkod

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installera och använda Speech SDK med Visual Studio Code

1. [Ladda ned](https://www.python.org/downloads/) och installera en 64-bitarsversion (3.5 eller senare) av Python på din dator.
1. [Ladda ned](https://code.visualstudio.com/Download) och installera Visual Studio Code.
1. Öppna Visual Studio Code och installera Python-tillägget genom att välja **Fil** > **Inställningar** > **Tillägg** från menyn och söka efter ”Python”.
   ![Installera Python-tillägg](media/sdk/qs-python-vscode-python-extension.png)
1. Skapa en mapp att lagra projektet i, till exempel med hjälp av Windows Explorer.
1. I Visual Studio Code klickar du på **Fil**-ikonen och öppnar sedan mappen som du skapade.
   ![Öppna mapp](media/sdk/qs-python-vscode-python-open-folder.png)
1. Skapa en ny Python-källfil `speechsdk.py` genom att klicka på den nya filikonen.
   ![Skapa fil](media/sdk/qs-python-vscode-python-newfile.png)
1. Kopiera, klistra in och spara [Python-koden](#quickstart-code) till den nyskapade filen.
1. Infoga prenumerationsinformationen för taltjänsten.
1. Om en Python-tolk redan har valts kommer den att visas på statusfältets vänstra sida, längst ner i fönstret.
   Annars kan du ta fram en lista över tillgängliga Python-tolkar genom att öppna **Kommandopaletten** (`Ctrl+Shift+P`) och skriva **Python: Välj tolk** och därefter ett lämpligt alternativ.
1. Om Python Speech SDK-paketet inte är installerat för Python-tolken du valde är det enkelt att göra detta från Visual Studio Code.
   Öppna en terminal för att installera Speech SDK-paketet genom att ta fram Kommandopaletten igen (`Ctrl+Shift+P`) och skriva **Terminal: Skapa en ny integrerad terminal**.
   I terminalen som öppnas anger du kommandot `python -m pip install azure-cognitiveservices-speech`, eller lämpligt kommando för ditt system.
1. Kör exempelkoden genom att högerklicka någonstans i redigeraren och välja **Kör Python-fil i Terminal**.
   Säg några ord när du ombeds göra så. Den transkriberade texten ska visas kort därefter.
   ![Kör exemplet](media/sdk/qs-python-vscode-python-run.png)

Om det finns problem med att följa dessa anvisningar finns den mer omfattande [självstudien Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Python-exempel på GitHub](https://aka.ms/csspeech/samples)
