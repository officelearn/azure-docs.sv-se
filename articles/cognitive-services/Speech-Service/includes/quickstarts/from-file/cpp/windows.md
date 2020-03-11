---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 2f029725ff53c6bf01481f7a188e070cf84550c9
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082286"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=windows)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna källfilen **helloworld.cpp**.

1. Ersätt all kod med följande kodfragment:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med **regions-ID** : t från den [region](https://aka.ms/speech/sdkregion) som är associerad med din prenumeration (till exempel `westus` för den kostnads fria utvärderings prenumerationen).

1. Ersätt strängen `whatstheweatherlike.wav` med ditt eget fil namn.

1. Välj **arkiv** > **Spara alla**på Meny raden.

> [!NOTE]
> Tal-SDK: n kommer att känna igen med en-US för språket, se [Ange käll språk för tal till text](../../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet **HelloWorld** genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**).

1. Ljud filen skickas till tal tjänsten och den första uttryck i filen skrivs till text, som visas i samma fönster.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]