---
title: 'Snabbstart: Syntetisera tal, Java (Windows, Linux, macOS) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten får du lära dig att skapa ett enkelt Java-program som fångar och syntetiserar tal från text och spelar upp det med standardhögtalaren.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: a49fadee598d1fa87c6dddbbf6ad388243ea6fb8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924821"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=jre)

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Om du vill lägga till en ny tom klass i Java-projektet väljer du **Fil** > **ny** > **klass**.

1. I fönstret **New Java Class** (Ny Java-klass) anger du **speechsdk.quickstart** i fältet **Paket** och anger **Main** i fältet **Namn**.

   ![Skärmbild av fönstret New Java Class (Ny Java-klass)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Ersätt all kod i `Main.java` med följande kodavsnitt:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/text-to-speech/src/speechsdk/quickstart/Main.java#code)]

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Tryck på F11 eller välj **Kör** > **felsökning**.
Mata in en text när du uppmanas att ange den syntetiserade ljud som spelas upp från standardhögtalaren.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röstexempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
