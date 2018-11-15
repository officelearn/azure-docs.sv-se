---
title: 'Snabbstart: Identifiera och transkribera tal, .NET Framework (Windows) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett tal till text-konsolprogram med hjälp av .NET-ramverket för Windows och Speech SDK. När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/05/2018
ms.author: wolfma
ms.openlocfilehash: 502f59c88808480f26e6ea5a6d2c5b362c78869b
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683511"
---
# <a name="quickstart-recognize-and-transcribe-speech-using-the-speech-sdk-and-net-framework-windows"></a>Snabbstart: Identifiera och transkribera tal med hjälp av Speech SDK och .NET Framework (Windows)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Använd den här guiden för att skapa ett tal till text-konsolprogram med hjälp av .NET-ramverket för Windows och Speech SDK. När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.

Den här snabbstarten kräver ett [Azure Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med Microsoft Speech aktiverat. Om du inte har ett konto kan du använda den [kostnadsfria utvärderingsversionen](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) för att hämta en prenumerationsnyckel.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra det här projektet behöver du:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* En prenumerationsnyckel för Speech Service
* Åtkomst till datorns mikrofon

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna `Program.cs` och ersätt den automatiskt genererade koden med det här exemplet:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Leta upp och ersätt strängen `YourSubscriptionKey` med din Speech Service-prenumerationsnyckel.

1. Leta upp och ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnadsfria utvärderingsversionen är regionen `westus`.

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. På menyraden väljer du **Skapa** > **Skapa lösning**. Koden bör nu kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-dotnet-windows-08-build.png "Slutförd byggprocess")

1. På menyraden väljer du **Felsök** > **Starta felsökning** eller trycker på **F5** för att starta programmet.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Starta appen i felsökningsläge")

1. Ett konsolfönster visas, där du uppmanas att tala. Säg nu något på engelska. Ditt tal överförs till Speech Service och transkriberas till text i realtid. Resultatet skrivs till konsolen.

    ![Skärmbild av konsolens utdata efter lyckad taligenkänning](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Konsolens utdata efter lyckad taligenkänning")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Koden är tillgänglig i mappen `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera avsikter i tal med hjälp av Speech SDK för C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Se även

- [Översätta tal](how-to-translate-speech-csharp.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
