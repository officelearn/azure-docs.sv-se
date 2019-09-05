---
title: 'Snabbstart: Syntetisera tal, C++ (Windows) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig att syntetisera tal i C++ på Windows-skrivbordet med hjälp av tal-SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 686b21d3e02266af77687778c32f0d1ca6d55154
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383087"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Snabbstart: Syntetisera tal in i C++ Windows med hjälp av tal-SDK

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-cpp-windows.md) och [tal översättning](quickstart-translate-speech-cpp-windows.md).

I den här artikeln får skapa du ett C++-konsolprogram för Windows. Du använder Cognitive Services [tal-SDK](speech-sdk.md) för att syntetisera tal från text i real tid och spela upp talet på din dators högtalare. Programmet har skapats med [tal SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2019 (alla versioner).

En fullständig lista över språk/röster som är tillgängliga för tal syntes finns i [språk stöd](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Förutsättningar

Du behöver en prenumerations nyckel för tal tjänster för att slutföra den här snabb starten. Du kan skaffa en utan kostnad. Mer information finns i [testa tal tjänsterna kostnads fritt](get-started.md) .

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna källfilen **helloworld.cpp**.

1. Ersätt all kod med följande kodfragment:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Välj **Arkiv** > **Spara alla**på Meny raden.

## <a name="build-and-run-the-application"></a>Skapa och kör appen

1. Välj **bygge** > **build-lösning** i meny raden för att bygga programmet. Koden bör nu kompileras utan fel.

1. Starta programmet **HelloWorld** genom att välja **Felsök** > **Starta fel sökning** (eller tryck på **F5**).

1. Ange en engelsk fras eller mening. Programmet skickar texten till tal tjänsterna, som skickar syntetiskt tal till programmet som spelas upp på din högtalare.

   ![Konsol utdata efter lyckad tal syntes](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel hur du sparar tal till en ljudfil, finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska C++-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Skapa en anpassad röst](how-to-custom-voice-create-voice.md)
- [Spela in anpassade röst exempel](record-custom-voice-samples.md)
