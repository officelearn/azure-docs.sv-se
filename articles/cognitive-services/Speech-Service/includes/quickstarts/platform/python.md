---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 327000173b4c5c378f60c76eb1a24bd155901671
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666921"
---
Den här guiden visar hur du installerar [Tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för Python. Om du bara vill att paketnamnet ska `pip install azure-cognitiveservices-speech`komma igång på egen hand kör du .

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Operativsystem som stöds

- Python Speech SDK-paketet är tillgängligt för dessa operativsystem:
  - Windows: x64 och x86
  - Mac: macOS X version 10.12 eller senare
  - Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 på x64

## <a name="prerequisites"></a>Krav

- Linux-plattformar som stöds kräver`libssl` vissa installerade bibliotek (för stöd för säkert socketlager och `libasound2` för ljudstöd). Se din distribution nedan för de kommandon som behövs för att installera rätt versioner av dessa bibliotek.

  - På Ubuntu kör du följande kommandon för att installera de paket som krävs:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - På Debian 9 kör du följande kommandon för att installera de paket som krävs:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - På RHEL/CentOS 8 kör du följande kommandon för att installera de paket som krävs:

        ```sh
        sudo yum update
        sudo yum install alsa-lib openssl python3
        ```

> [!NOTE]
> På RHEL/CentOS 8 följer du instruktionerna för [hur du konfigurerar OpenSSL för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- I Windows behöver du [Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) för din plattform. Observera att om du installerar detta för första gången kan det krävas att du startar om Windows innan du fortsätter med den här guiden.
- Och slutligen behöver du [Python 3,5 till 3,8](https://www.python.org/downloads/). Om du vill kontrollera installationen öppnar du `python --version` en kommandotolk och skriver kommandot och kontrollerar resultatet. Om den är korrekt installerad får du svaret "Python 3.5.1" eller liknande.

## <a name="install-the-speech-sdk-from-pypi"></a>Installera Tal-SDK från PyPI

Om du använder din egen miljö eller byggverktyg kör du följande kommando för att installera Tal SDK från [PyPI](https://pypi.org/). För användare av Visual Studio-kod går du vidare till nästa underavsnitt för guidad installation.

```sh
pip install azure-cognitiveservices-speech
```

Om du använder macOS kan du behöva köra följande `pip` kommando för att få kommandot ovan att fungera:

```sh
python3 -m pip install --upgrade pip
```

När du har använt `pip` för `azure-cognitiveservices-speech`att installera kan du använda Tal-SDK genom att importera namnområdet till python-projekten.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>Installera Tal-SDK med Visual Studio-kod

1. Ladda ned och installera den senaste versionen av [Python](https://www.python.org/downloads/) som stöds för din plattform, 3,5 till 3,8.
   - Windows-användare ser till att välja "Lägg till Python i din PATH" under installationsprocessen.
1. Ladda ned och installera [Visual Studio Code](https://code.visualstudio.com/Download).
1. Öppna Visual Studio Code och installera Python-tillägget. Välj **File** > **Filinställningar** > **Tillägg** på menyn. Sök efter **Python** och klicka på **Installera**.

   ![Installera Python-tillägget](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Installera även Speech SDK Python-paketet från den integrerade kommandoraden:
   1. Öppna en terminal (från rullgardinsmenyerna, **Terminal** > **New Terminal**)
   1. I terminalen som öppnas anger du kommandot`python -m pip install azure-cognitiveservices-speech`

Om du inte har någon ny visual studiokod kan du läsa den mer omfattande [Visual Studio-koddokumentationen](https://code.visualstudio.com/docs). Mer information om Visual Studio Code och Python finns i [självstudiekursen Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="support-and-updates"></a>Support och uppdateringar

Uppdateringar av Python Speech SDK-paketet distribueras via PyPI och visas i [Viktig information](~/articles/cognitive-services/speech-service/releasenotes.md).
Om en ny version är tillgänglig kan du uppdatera till den med kommandot `pip install --upgrade azure-cognitiveservices-speech`.
Kontrollera vilken version som är installerad genom att granska variabeln `azure.cognitiveservices.speech.__version__`.

Om du har problem eller saknar en funktion kan du ta en titt på [alternativen för support och hjälp](~/articles/cognitive-services/speech-service/support.md).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]
