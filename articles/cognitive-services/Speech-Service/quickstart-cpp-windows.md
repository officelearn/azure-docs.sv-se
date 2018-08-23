---
title: 'Snabbstart: Känna igen tal i C++ Windows-skrivbordet med hjälp av Cognitive Services tal SDK'
titleSuffix: Microsoft Cognitive Services
description: Lär dig att känna igen tal i C++ Windows-skrivbordet med hjälp av Cognitive Services tal SDK
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 59c70d33d537742a5918dc1ba55ab8ac1f84e12c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "41988775"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i C++ Windows-skrivbordet med hjälp av tal-SDK

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Vi beskriver hur du skapar en C++-baserad konsol-program för Windows-skrivbord som använder SDK: N för tal.
Programmet är baserad på den [Microsoft Cognitive Services tal SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Förutsättningar

* En prenumerationsnyckel för Speech-tjänsten. Se [prova speech-tjänsten utan kostnad](get-started.md).
* En Windows-dator med en fungerande mikrofon.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition eller senare.
* Den **skrivbordsutveckling med C++** arbetsbelastningen i Visual Studio och **NuGet-Pakethanteraren** komponenten i Visual Studio.
  Du kan aktivera både i **verktyg** \> **hämta verktyg och funktioner**under den **arbetsbelastningar** och **enskilda komponenter** flikar , respektive:

  ![Aktivera skrivbordsutveckling med C++ arbetsbelastning](media/sdk/vs-enable-cpp-workload.png)

  ![Aktivera NuGet-Pakethanteraren i Visual Studio ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

Skapa en ny Visual C++ Windows Desktop Windows-konsolprogram i Visual Studio 2017. I den **nytt projekt** dialogrutan i den vänstra rutan expanderar **installerad** \> **Visual C++** \> **Windows Desktop** och välj sedan **Windows-konsolprogram**. Projektnamnet, ange *helloworld*.

![Skapa Visual C++ Windows stationära Windows-konsolprogram](media/sdk/qs-cpp-windows-01-new-console-app.png)

Om du kör på en 64-bitars Windows-installation, du kan också växla build-plattformen för att `x64`:

![Växla build-plattform till x64](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Installera och referera till tal SDK NuGet-paketet

Högerklicka på lösningen i Solution Explorer och klicka på **hantera NuGet-paket för lösningen**.

![Högerklicka på Hantera NuGet-paket för lösningen](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

I det övre högra hörnet i den **Paketkällan** , Välj ”Nuget.org”.
Från den **Bläddra** fliken, Sök efter ”Microsoft.CognitiveServices.Speech”-paketet, markera den och kontrollera den **projekt** och **helloworld** rutorna till höger och välj **Installera** att installera den till helloworld-projekt.

> [!NOTE]
> Den aktuella versionen av Cognitive Services tal SDK är `0.6.0`.

![Installera Microsoft.CognitiveServices.Speech NuGet-paketet](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

Acceptera licensvillkoren på skärmen som visas för licens:

![Acceptera licensvillkoren](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>Lägg till exempelkoden

1. Ersätt Koden standard starter med följande:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerade med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringar i projektet.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Skapa programmet. Från menyraden väljer **skapa** > **skapa lösning**. Koden ska kompilera utan fel nu:

   ![Version](media/sdk/qs-cpp-windows-06-build.png)

1. Starta programmet. Från menyraden väljer **felsöka** > **Starta felsökning**, eller tryck på **F5**.

   ![Starta appen into-felsökning](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Ett konsolfönster öppnas, där du uppmanas att säga något (på engelska).
   Resultatet av erkännande visas på skärmen.

   ![Konsolens utdata efter lyckad taligenkänning](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i den `quickstart/cpp-windows` mapp.

## <a name="next-steps"></a>Nästa steg

* [Hämta våra exempel](speech-sdk.md#get-the-samples)
