---
title: 'Snabbstart: Känna igen tal, Java (Windows, Linux) – Speech Services'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten får lära du dig att skapa ett enkelt Java-program som samlar in och transkriberar användaren tal från datorns mikrofon.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: fmegen
ms.openlocfilehash: fe565d63e72b5ec2798dde03ba4f4bd9ff4f48a7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009407"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Snabbstart: Känna igen tal med Speech Service SDK för Java

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln ska du skapa ett Java-konsolprogram med hjälp av [Speech SDK](speech-sdk.md). Du transkriberar tal till text i realtid från datorns mikrofon. Du har skapat programmet med tal SDK Maven-paketet och Eclipse Java IDE (v4.8) på 64-bitars Windows 64-bitars Ubuntu Linux 16.04 / 18.04 eller i macOS 10.13 eller senare. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här snabbstarten krävs:

* Operativsystem: Windows (64-bitars), Ubuntu Linux 16.04/18.04 (64-bitars) och macOS 10.13 eller senare
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* En Azure-prenumerationsnyckel för taltjänsten. [Skaffa en kostnadsfritt](get-started.md).

Om du kör Ubuntu 16.04/18.04 ska dessa beroenden vara installerade innan du startar Eclipse.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Om du kör Windows (64-bitars) se till att du har installerat Microsoft Visual C++ Redistributable för din plattform.
* [Ladda ned Microsoft Visual C++ Redistributable for Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)


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
