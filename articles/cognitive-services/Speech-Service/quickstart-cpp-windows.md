---
title: 'Snabbstart: Identifiera tal- C++ , (Windows)-tal-service'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du identifierar tal i C++ på Windows Desktop med hjälp av Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: a9c43e1d27a396a2c3e9123ce5ce538296c6870c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381927"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Snabbstart: Taligenkänning i C++ på Windows med hjälp av Speech SDK

Snabb Starter är också tillgängliga för [tal syntes](quickstart-text-to-speech-cpp-windows.md) och [tal översättning](quickstart-translate-speech-cpp-windows.md).

Om du vill kan du välja ett annat programmeringsspråk och en annan miljö:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln får skapa du ett C++-konsolprogram för Windows. Du använder Cognitive Services [Speech SDK](speech-sdk.md) för att transkribera tal till text i realtid från datorns mikrofon. Programmet har skapats med [tal SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2019 (alla versioner).

## <a name="prerequisites"></a>Förutsättningar

Du behöver en prenumerations nyckel för tal tjänster för att slutföra den här snabb starten. Du kan skaffa en utan kostnad. Mer information finns i [testa tal tjänsterna kostnads fritt](get-started.md) .

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna källfilen **helloworld.cpp**.

1. Ersätt all kod med följande kodfragment:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Välj **Arkiv** > **Spara alla**på Meny raden.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet **HelloWorld** genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**).

1. Säg en engelsk fras eller en mening. Programmet skickar ditt tal till tal tjänsterna, som överförs till text och skickas tillbaka till programmet för visning.

   ![Konsol utdata efter lyckad igenkänning](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel hur man läser tal från en ljudfil, finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska C++-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Träna en modell för Custom Speech](how-to-custom-speech-train-model.md)
