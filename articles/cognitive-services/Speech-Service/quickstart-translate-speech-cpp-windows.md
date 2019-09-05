---
title: 'Snabbstart: Översätt tal- C++ , (Windows)-tal-service'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten skapar du ett C++ program för att avbilda användar tal, översätter det till ett annat språk och skriver texten på kommando raden. Den här guiden är utformad för Windows-användare.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: erhopf
ms.openlocfilehash: 6ba4e44efc7ff24aa48f9f16840b2248423f7241
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382683"
---
# <a name="quickstart-translate-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Snabbstart: Översätta tal i C++ på Windows med hjälp av tal-SDK

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-cpp-windows.md) och [tal syntes](quickstart-text-to-speech-cpp-windows.md).

I den här snabb starten skapar du ett C++ program som fångar in användarens tal från datorns mikrofon, översätter talet och omvandlar den översatta texten till kommando raden i real tid. Det här programmet har skapats med [Speech SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2019 (alla versioner).

En fullständig lista över språk som är tillgängliga för talöversättning finns i [språkstöd](language-support.md).

## <a name="prerequisites"></a>Förutsättningar

Du behöver en prenumerations nyckel för tal tjänster för att slutföra den här snabb starten. Du kan skaffa en utan kostnad. Mer information finns i [testa tal tjänsterna kostnads fritt](get-started.md) .

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna källfilen **helloworld.cpp**.

1. Ersätt all kod med följande kodfragment:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp?range=2-#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Välj **Arkiv** > **Spara alla**på Meny raden.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet **HelloWorld** genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**).

1. Säg en engelsk fras eller en mening. Programmet skickar ditt tal till tal tjänsterna, som översätter och omvandlar till text (i det här fallet till franska och tyska). Tal tjänsterna skickar sedan tillbaka texten till programmet för visning.

   ![Konsol utdata efter lyckad tal Översättning](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel hur du läser tal från en ljudfil eller omvandlar översatt text till syntetiskt tal, finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska C++-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Träna en modell för Custom Speech](how-to-custom-speech-train-model.md)
