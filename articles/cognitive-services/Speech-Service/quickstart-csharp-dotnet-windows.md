---
title: 'Snabbstart: Identifiera tal-, .NET Framework-(Windows)-tal-service'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett tal till text-konsolprogram med hjälp av .NET-ramverket för Windows och Speech SDK. När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327055"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Snabbstart: Taligenkänning med Speech SDK för .NET Framework (Windows)

Snabb Starter är också tillgängliga för [tal syntes](quickstart-text-to-speech-dotnet-windows.md) och [tal översättning](quickstart-translate-speech-dotnetframework-windows.md).

Om du vill kan du välja ett annat programmeringsspråk och/eller miljö:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Använd den här guiden för att skapa ett tal till text-konsolprogram med hjälp av .NET-ramverket för Windows och Speech SDK. När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.

För en snabb demonstration (utan att skapa Visual Studio-projektet själv, enligt beskrivningen i den här artikeln), hämtar du de senaste [Cognitive Servicesa tal SDK-exemplen](https://github.com/Azure-Samples/cognitive-services-speech-sdk) från GitHub.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra det här projektet behöver du:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* En prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).
* Åtkomst till datorns mikrofon.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna **program.cs** och ersätt den automatiskt genererade koden med det här exemplet:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Hitta strängen `YourSubscriptionKey` och ersätt den med din prenumerations nyckel för tal tjänster.

1. Hitta strängen `YourServiceRegion` och ersätt den med den [region](regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnads fria utvärderings prenumerationen är regionen `westus`.

1. Välj **arkiv** > **Spara alla**på Meny raden.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet **HelloWorld** genom att välja **Felsök** > **Starta fel sökning** (eller Välj **F5**).

1. Tala om en engelsk fras eller mening i enhetens mikrofon. Programmet skickar ditt tal till tal tjänsterna, som skickar den uppskrivna texten tillbaka till programmet för visning.

   ![Användar gränssnitt för tal igenkänning](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Träna en modell för Custom Speech](how-to-custom-speech-train-model.md)
