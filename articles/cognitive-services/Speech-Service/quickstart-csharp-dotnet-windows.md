---
title: 'Snabbstart: Känna igen tal i C# under .NET Framework i Windows med hjälp av Speech Service SDK'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du känner igen tal i C# under .NET Framework i Windows med hjälp av Speech Service SDK
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 06d4a41a4a67d077f8d79eee68938dff65adf0e8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468525"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-by-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i C# under .NET Framework i Windows med hjälp av Speech-SDK:n

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln skapar du ett C#-konsolprogram för .NET Framework i Windows med hjälp av [Speech-SDK:n](speech-sdk.md). Du transkriberar tal till text i realtid från datorns mikrofon. Programmet har skapats med [Speech SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017 (alla versioner).

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en prenumerationsnyckel för Speech-tjänsten för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Mer information finns i [Prova Speech-tjänsten kostnadsfritt](get-started.md).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna `Program.cs` och ersätt all kod i den med nedanstående.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel för Speech-tjänsten.

1. Ersätt även strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri utvärderingsversion).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. På menyraden väljer du **Skapa** > **Skapa lösning**. Koden bör nu kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-dotnet-windows-08-build.png "Slutförd byggprocess")

1. Starta programmet. På menyraden väljer du **Felsök** > **Starta felsökning**, eller så trycker du på **F5**.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Starta appen i felsökningsläge")

1. Ett konsolfönster öppnas där du uppmanas att säga något. Säg en engelsk fras eller en mening. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i samma fönster.

    ![Skärmbild av konsolens utdata efter lyckad taligenkänning](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Konsolens utdata efter lyckad taligenkänning")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i mappen `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera avsikter i tal med hjälp av Speech SDK för C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Se även

- [Översätta tal](how-to-translate-speech-csharp.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
