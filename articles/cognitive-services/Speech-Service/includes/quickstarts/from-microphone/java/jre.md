---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: bcf353fa0783429702143b2bf0bdf2114dd4bed6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503663"
---
## <a name="prerequisites"></a>Förutsättningar

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Du lägger till en ny tom klass i Java-projektet genom att välja **Arkiv** > **Nytt** > **Klass**.

1. I fönstret **New Java Class** (Ny Java-klass) anger du **speechsdk.quickstart** i fältet **Paket** och anger **Main** i fältet **Namn**.

   ![Skärmbild av fönstret New Java Class (Ny Java-klass)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Ersätt all kod i `Main.java` med följande kodavsnitt:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Tryck på F11 eller välj **Kör** > **Felsök**.
Följande 15 sekunder av talindata från mikrofonen identifieras och loggas i konsolfönstret.

![Skärmbild av konsolutdata efter lyckad taligenkänning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
