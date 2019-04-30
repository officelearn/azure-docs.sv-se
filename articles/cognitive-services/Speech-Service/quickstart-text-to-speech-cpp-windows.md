---
title: 'Snabbstart: Syntetisera tal, C++ (Windows) – Speech Services'
titleSuffix: Azure Cognitive Services
description: Lär dig att syntetisera tal i C++ Windows-skrivbordet med hjälp av tal-SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: d818f8a8fc813d3da5eea4d8ef9cd2f4fd18bb53
ms.sourcegitcommit: 1aab8b764d767d86fbf262bda34996189d00c7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "64402651"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Snabbstart: Syntetisera tal i C++ i Windows med hjälp av tal-SDK

I den här artikeln får skapa du ett C++-konsolprogram för Windows. Du använder Cognitive Services [tal SDK](speech-sdk.md) att syntetisera tal från text i realtid och spela upp tal på datorns högtalare. Programmet har skapats med [Speech SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017 (alla versioner).

Funktionen som beskrivs i den här artikeln är tillgänglig från [tal SDK 1.4.0](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.4.0).

En fullständig lista över språk/röster som är tillgängliga för talsyntes Se [språkstöd](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Speech Services-prenumeration för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Se [testa Speech Services kostnadsfritt](get-started.md) mer information.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna källfilen *helloworld.cpp*. Ersätt all kod under den första include-instruktionen (`#include "stdafx.h"` eller `#include "pch.h"`) med följande:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. I menyraden väljer du **Skapa** > **Skapa lösning**. Koden ska kompileras utan fel.

   ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-cpp-windows-06-build.png)

1. Starta programmet. I menyraden väljer du **Felsök** > **Starta felsökning**, eller tryck på **F5**.

   ![Skärmbild av Visual Studio-programmet med Starta felsökning markerat](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Ett konsolfönster visas där du uppmanas att ange lite text. Skriv några ord eller en mening. Den text som du har angett skickas till Speech Services och syntetiskt till tal som spelar upp på högtalaren.

   ![Skärmbild av konsolens utdata efter lyckad syntes](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel hur du sparar tal i en ljudfil, är tillgängliga på GitHub.

> [!div class="nextstepaction"]
> [Utforska C++-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa rösttyper](how-to-customize-voice-font.md)
- [Post voice-exempel](record-custom-voice-samples.md)
