---
title: 'Snabbstart: Översätt tal, C# (.NET Framework Windows) – tal service'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten skapar du ett .NET Framework-program för att avbilda användar tal, översätter det till ett annat språk och skriva ut texten på kommando raden. Den här guiden är utformad för Windows-användare.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: erhopf
ms.openlocfilehash: bb710a3e3adb13aa3999c13043c8bb93f6b885f1
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327348"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework-windows"></a>Snabbstart: Översätt tal med talet SDK för .NET Framework (Windows)

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-csharp-dotnet-windows.md) och [tal syntes](quickstart-text-to-speech-dotnet-windows.md).

I den här snabb starten skapar du ett .NET Framework-program som fångar upp användar tal från datorns mikrofon, översätter talet och omvandlar den översatta texten till kommando raden i real tid. Det här programmet kan köras på 32-bitars eller 64-bitars Windows, och det har skapats med [NuGet-paketet för tal-SDK](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2019.

En fullständig lista över språk som är tillgängliga för talöversättning finns i [språkstöd](language-support.md).

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna **program.cs**och ersätt all kod i den med följande.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Hitta strängen `YourSubscriptionKey` och ersätt den med din prenumerations nyckel.

1. Hitta strängen `YourServiceRegion` och ersätt den med den [region](regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnads fria utvärderings prenumerationen är regionen `westus`.

1. Välj **arkiv** > **Spara alla**på Meny raden.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet **HelloWorld** genom att välja **Felsök** > **Starta fel sökning** (eller Välj **F5**).

1. Tala om en engelsk fras eller mening i enhetens mikrofon. Programmet skickar din röst till tal tjänsten som översätter talet till text på ett annat språk (i det här fallet tyska). Tal tjänsten skickar den översatta texten tillbaka till programmet, som visar översättningen i fönstret.

   ![Användar gränssnitt för tal Översättning](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png)

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel att läsa tal från en ljudfil och mata ut översatt text som syntetiserat tal, finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Träna en modell för Custom Speech](how-to-custom-speech-train-model.md)
