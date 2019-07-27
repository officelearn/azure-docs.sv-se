---
title: 'Snabbstart: Identifiera tal-, Java-(Windows-, Linux) – tal-service'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten får du lära dig att skapa ett enkelt Java-program som fångar upp och omtilldelar användar tal från datorns mikrofon.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 498e41b08133113be9789ef49291b8e2bb0f3705
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68554116"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Snabbstart: Känna igen tal med Speech Service SDK för Java

Snabb Starter är också tillgängliga för [tal-till-tal-översättning](quickstart-translate-speech-java-jre.md) och [röst för första virtuella assistent](quickstart-virtual-assistant-java-jre.md).

Om du vill kan du välja ett annat programmeringsspråk och/eller miljö:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln ska du skapa ett Java-konsolprogram med hjälp av [Speech SDK](speech-sdk.md). Du transkriberar tal till text i realtid från datorns mikrofon. Programmet har skapats med tal SDK maven-paketet och Sol förmörkelse Java IDE (v 4.8) på 64-bitars Windows, 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) eller på macOS 10,13 eller senare. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* Operativsystem: 64-bitars Windows, 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) eller macOS 10,13 eller senare
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* En Azure-prenumerationsnyckel för taltjänsten. [Skaffa en kostnadsfritt](get-started.md).

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

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Du lägger till en ny tom klass i Java-projektet genom att välja **Arkiv** > **Nytt** > **Klass**.

1. I fönstret **New Java Class** (Ny Java-klass) anger du **speechsdk.quickstart** i fältet **Paket** och anger **Main** i fältet **Namn**.

   ![Skärmbild av fönstret New Java Class (Ny Java-klass)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Ersätt all kod i `Main.java` med följande kodavsnitt:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Tryck på F11 eller välj **Kör** > **Felsök**.
Följande 15 sekunder av talindata från mikrofonen identifieras och loggas i konsolfönstret.

![Skärmbild av konsolutdata efter lyckad taligenkänning](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel att läsa tal från en ljudfil, finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska Java-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Snabbstart: Översätta tal, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
