---
title: 'Snabbstart: Syntetisera tal, python-tal-tjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att syntetisera tal i python med hjälp av tal-SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: fed0dea1a562f5ddea05516125a38c690069f446
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803966"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-python"></a>Snabbstart: Syntetisera tal med talet SDK för python

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-python.md).

Den här artikeln visar hur du använder tal tjänsterna via tal-SDK för python. Den illustrerar hur du kan syntetisera tal från text och spela upp det med standard ljud utmatningen.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumerations nyckel för tal tjänsterna. [Skaffa en kostnadsfritt](get-started.md).
* [Python 3.5 eller senare](https://www.python.org/downloads/).
* Python Speech SDK-paketet är tillgängligt för dessa operativsystem:
    * Windows 10: x64 och x86.
    * Mac: Mac OS X version 10.12 eller senare.
    * Linux: Ubuntu 16,04, Ubuntu 18,04, Debian 9 på x64.
* Kör de här kommandona i Linux för att installera de nödvändiga paketen:

  * I Ubuntu:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * På Debian 9:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* I Windows behöver du [Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) för din plattform.

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Observera att den här självstudien inte fungerar med tidigare version av SDK än 1.7.0.

Det här kommandot installerar Python-paketet från [PyPI](https://pypi.org/) för Speech SDK:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Support och uppdateringar

Uppdateringar av Python Speech SDK-paketet distribueras via PyPI och visas i [Viktig information](./releasenotes.md).
Om en ny version är tillgänglig kan du uppdatera till den med kommandot `pip install --upgrade azure-cognitiveservices-speech`.
Kontrollera vilken version som är installerad genom att granska variabeln `azure.cognitiveservices.speech.__version__`.

Om du har problem eller saknar en funktion kan du ta en titt på [alternativen för support och hjälp](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Skapa ett Python-program som använder Speech SDK

### <a name="run-the-sample"></a>Kör exemplet

Du kan kopiera [exempelkoden](#sample-code) till en källfil från den här snabbstarten `quickstart.py` och köra den i din IDE eller i konsolen:

```sh
python quickstart.py
```

Eller så kan du ladda ned den här snabbstartssjälvstudien som en [Jupyter](https://jupyter.org) Notebook från [exempellagringsplatsen för Speech SDK](https://aka.ms/csspeech/samples) och köra den som en notebook-fil.

### <a name="sample-code"></a>Exempelkod

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installera och använda Speech SDK med Visual Studio Code

1. Ladda ned och installera en 64-bitarsversion (3.5 eller senare) av [Python](https://www.python.org/downloads/) på din dator.
1. Ladda ned och installera [Visual Studio Code](https://code.visualstudio.com/Download).
1. Öppna Visual Studio Code och installera Python-tillägget. Välj **Arkiv** > **Inställningar** > **Tillägg** på menyn. Sök efter **Python**.

   ![Installera Python-tillägget](media/sdk/qs-python-vscode-python-extension.png)

1. Skapa en mapp att lagra projektet i. Ett exempel är med hjälp av Windows Explorer.
1. Från Visual Studio Code väljer du ikonen **Arkiv**. Öppna sedan den mapp du skapade.

   ![Öppna en mapp](media/sdk/qs-python-vscode-python-open-folder.png)

1. Skapa en ny Python-källfil `speechsdk.py` genom att välja den nya filikonen.

   ![Skapa en fil](media/sdk/qs-python-vscode-python-newfile.png)

1. Kopiera, klistra in och spara [Python-koden](#sample-code) till den nyskapade filen.
1. Infoga prenumerations information för tal tjänster.
1. Om en Python-tolk har valts visas den på statusfältets vänstra sida, längst ner i fönstret.
   Annars kan du ta fram en lista över tillgängliga Python-tolkar. Öppna kommandopaletten (Ctrl + Skift + P) och ange **Python: Välj tolk**. Välj en som passar.
1. Du kan installera Speech SDK Python-paketet i Visual Studio Code. Gör det om det inte är installerat än för Python-tolken som du valt.
   Öppna en terminal för att installera Speech SDK-paketet. Öppna kommandopaletten igen (Ctrl + Skift + P) igen och ange **Terminal: Skapa en ny integrerad terminal**.
   I terminalen som öppnas anger du kommandot `python -m pip install azure-cognitiveservices-speech`, eller lämpligt kommando för ditt system.
1. Kör exempelkoden genom att högerklicka någonstans i redigeraren. Välj **Kör Python-fil i Terminal**.
   Skriv lite text när du uppmanas till det. Det syntetiserade ljudet spelas upp strax efteråt.

   ![Köra ett exempel](media/sdk/qs-python-vscode-python-run-tts.png)

Om det finns problem med att följa dessa anvisningar finns den mer omfattande [självstudien Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Python-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa röst teckensnitt](how-to-customize-voice-font.md)
- [Spela in röst exempel](record-custom-voice-samples.md)
