---
title: 'Snabb start: syntetisera tal, Java (Windows, Linux, macOS) – tal service'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten lär du dig att skapa ett enkelt Java-program som fångar in och syntetiserar tal från text och spelar upp det med standard talare.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: c66e321618b46d52f791f95dab570e3721e806a9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299221"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-java"></a>Snabb start: syntetisera tal med tal-SDK för Java

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-java-jre.md), [tal-till-tal-översättning](quickstart-translate-speech-java-jre.md)och [röst-och första virtuella assistent](quickstart-virtual-assistant-java-jre.md).

I den här artikeln ska du skapa ett Java-konsolprogram med hjälp av [Speech SDK](speech-sdk.md). Du syntetiserar tal från text och spelar upp det med DATORns standard högtalare. Programmet har skapats med tal SDK maven-paketet och Sol förmörkelse Java IDE (v 4.8) på 64-bitars Windows, 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) eller på macOS 10,13 eller senare. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* Operativ system: 64-bitars Windows, 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) eller macOS 10,13 eller senare
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en utan kostnad](get-started.md).

Om du kör Linux kontrollerar du att dessa beroenden är installerade innan du startar Sol förmörkelse.

* I Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* På Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Om du kör Windows (64-bitars) bör du kontrol lera att du har installerat C++ Microsoft Visual Redistributable för din plattform.
* [Hämta Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>Skapa och konfigurera projektet

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Du lägger till en ny tom klass i Java-projektet genom att välja **Arkiv** > **Nytt** > **Klass**.

1. I fönstret **New Java Class** (Ny Java-klass) anger du **speechsdk.quickstart** i fältet **Paket** och anger **Main** i fältet **Namn**.

   ![Skärmbild av fönstret New Java Class (Ny Java-klass)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Ersätt all kod i `Main.java` med följande kodavsnitt:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Tryck på F11 eller välj **Kör** > **Felsök**.
Ange en text när du uppmanas att lyssna på det syntetiserade ljudet som spelas upp från standard talare.

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel hur man läser tal från en ljudfil, finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska Java-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Snabb start: identifiera tal, Java (Windows, Linux, macOS)](quickstart-java-jre.md)
- [Snabb start: Översätt tal, Java (Windows, Linux, macOS)](quickstart-translate-speech-java-jre.md)
- [Anpassa röst teckensnitt](how-to-customize-voice-font.md)
- [Spela in röst exempel](record-custom-voice-samples.md)
