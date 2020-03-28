---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 2f029725ff53c6bf01481f7a188e070cf84550c9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "79082286"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure Speech-resurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=windows)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna källfilen **helloworld.cpp**.

1. Ersätt all kod med följande utdrag:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med **regionidentifieraren** från [den region](https://aka.ms/speech/sdkregion) `westus` som är associerad med din prenumeration (till exempel för den kostnadsfria utvärderingsprenumerationen).

1. Ersätt strängen `whatstheweatherlike.wav` med ditt eget filnamn.

1. Välj Spara**alla** **på** > menyraden .

> [!NOTE]
> Tal-SDK kommer som standard att känna igen med hjälp av en-us för språket, se [Ange källspråk för tal till text](../../../../how-to-specify-source-language.md) för information om hur du väljer källspråk.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **Bygg** > **bygglösning på** menyraden för att skapa programmet. Koden bör nu kompileras utan fel.

1. Välj **Felsökning** > **Starta felsökning** (eller tryck på **F5)** för att starta **helloworld-programmet.**

1. Ljudfilen överförs till taltjänsten och det första uttrycket i filen transkriberas till text, som visas i samma fönster.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]