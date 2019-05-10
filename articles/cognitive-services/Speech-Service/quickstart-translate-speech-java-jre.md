---
title: 'Snabbstart: Översätta tal, Java (Windows, Linux) – Taltjänster'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten skapar du ett enkelt Java-program för att samla in användarens tal, översätta det till ett annat språk och mata ut texten till kommandoraden. Den här guiden är utformad för Windows- och Linux-användare.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 69cb48df4e765250247605fb5c4e1f421554fd20
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467020"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Snabbstart: Omvandla tal med Speech-SDK för Java

Snabbstarter kan också användas för [tal till text](quickstart-java-jre.md) och [röst första virtuella assistenter](quickstart-virtual-assistant-java-jre.md).

I den här snabbstarten skapar du ett enkelt Java-program som samlar in användarens tal från din dators mikrofon, översätter talet och transkriberar den översatta texten till kommandoraden i realtid. Det här programmet är avsedd att köras på Windows 64-bitars eller 64-bitars Linux (Ubuntu 16.04, 18.04 Ubuntu, Debian 9) eller på macOS 10.13 eller senare. Den är byggd med tal SDK Maven-paketet och Eclipse IDE för Java.

En fullständig lista över språk som är tillgängliga för talöversättning finns i [språkstöd](language-support.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här snabbstarten krävs:

* Operativsystem: 64-bitars Windows 64-bitars Linux (Ubuntu 16.04, 18.04 Ubuntu, Debian 9) eller macOS 10.13 eller senare
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* En Azure-prenumerationsnyckel för taltjänsten. [Skaffa en kostnadsfritt](get-started.md).

Om du kör Linux måste du kontrollera att dessa beroenden är installerade innan du startar Eclipse.

 * I Ubuntu:

   ```sh
   sudo apt-get update
   sudo apt-get install libssl1.0.0 libasound2
   ```

 * On Debian 9:

   ```sh
   sudo apt-get update
   sudo apt-get install libssl1.0.2 libasound2
   ```

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="create-and-configure-project"></a>Skapa och konfigurera projektet

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Du lägger till en ny tom klass i Java-projektet genom att välja **Arkiv** > **Nytt** > **Klass**.

1. I fönstret **New Java Class** (Ny Java-klass) anger du **speechsdk.quickstart** i fältet **Paket** och anger **Main** i fältet **Namn**.

   ![Skärmbild av fönstret New Java Class (Ny Java-klass)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Ersätt all kod i `Main.java` med följande kodavsnitt:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Tryck på F11 eller välj **Kör** > **Felsök**.

Talindatan från din mikrofon transkriberas till tyska och loggas i konsolfönstret. Tryck på ”Retur” för att sluta samla in tal.

![Skärmbild av konsolutdata efter lyckad taligenkänning](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel att läsa tal från en ljudfil och mata ut översatt text som syntetiserat tal finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska Java-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Snabbstart: Känna igen tal, Java (Windows, Linux)](quickstart-java-jre.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
