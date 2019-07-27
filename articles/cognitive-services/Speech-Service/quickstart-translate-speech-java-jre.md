---
title: 'Snabbstart: Översätta tal-, Java-(Windows-, Linux)-tal-service'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten skapar du ett enkelt Java-program för att samla in användarens tal, översätta det till ett annat språk och mata ut texten till kommandoraden. Den här guiden är utformad för Windows- och Linux-användare.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: cbb86dd4b24cb325b8ea6708ebc2ffc89a697757
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553388"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>Snabbstart: Omvandla tal med Speech-SDK för Java

Snabb Starter är också tillgängliga för [tal-till-text](quickstart-java-jre.md) och [röst-första virtuella assistent](quickstart-virtual-assistant-java-jre.md).

I den här snabbstarten skapar du ett enkelt Java-program som samlar in användarens tal från din dators mikrofon, översätter talet och transkriberar den översatta texten till kommandoraden i realtid. Det här programmet har utformats för att köras på 64-bitars Windows eller 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9) eller på macOS 10,13 eller senare. Den är byggd med talet SDK maven-paketet och Sol förmörkelse Java IDE.

En fullständig lista över språk som är tillgängliga för talöversättning finns i [språkstöd](language-support.md).

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
