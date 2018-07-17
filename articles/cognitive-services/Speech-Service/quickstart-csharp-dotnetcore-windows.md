---
title: 'Snabbstart: Känna igen tal i C# under .NET Core för Windows med hjälp av Cognitive Services tal SDK | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Lär dig att känna igen tal i C# under .NET Core för Windows med hjälp av Cognitive Services tal SDK
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: f00653d0c58f52c73126693422ab0a485540dd7a
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072601"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i C# under .NET Core för Windows med hjälp av tal-SDK

I den här artikeln får du lära dig hur du skapar ett C#-konsolprogram för .NET Core för Windows med hjälp av Cognitive Services tal SDK för att transkribera tal till text.
Programmet är baserad på den [Microsoft Cognitive Services tal SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Förutsättningar

* En prenumerationsnyckel för Speech-tjänsten. Se [prova speech-tjänsten utan kostnad](get-started.md).
* En Windows-dator med en fungerande mikrofon.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition eller senare.
* Den **.NET Core plattformsoberoende utveckling** arbetsbelastningen i Visual Studio. Du kan aktivera den i **verktyg** \> **hämta verktyg och funktioner**.

  ![Aktivera .NET Core plattformsoberoende utveckling](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Skapa en ny Visual C# .NET Core-Konsolapp i Visual Studio 2017. I den **nytt projekt** dialogrutan i den vänstra rutan expanderar **installerad** \> **Visual C#** \> **.NET Core**och välj sedan **Konsolapp (.NET Core)**. Projektnamnet, ange *helloworld*.

    ![Skapa Visual C#-Konsolapp (.NET Core)](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "skapar Visual C#-Konsolapp (.NET Core)")

1. Installera och referera till den [tal NuGet-paketet SDK](https://aka.ms/csspeech/nuget). Högerklicka på lösningen i Solution Explorer och välj **hantera NuGet-paket för lösningen**.

    ![Högerklicka på Hantera NuGet-paket för lösningen](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "hantera NuGet-paket för lösningen")

1. I det övre högra hörnet i den **Paketkällan** väljer **Nuget.org**. Sök efter och installera den `Microsoft.CognitiveServices.Speech` paketera och installera den i den **helloworld** projekt.

    ![Installera NuGet-paketet för Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "installera Nuget-paketet")

1. Acceptera licensvillkoren på skärmen som visas för licens:

    ![Acceptera licensvillkoren](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "acceptera licensvillkoren")

## <a name="add-the-sample-code"></a>Lägg till exempelkoden

1. I den `Program.cs` filen i Visual Studio-projektet och ersätt all kod med följande:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerade med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringar i projektet.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Skapa programmet. Från menyraden väljer **skapa** > **skapa lösning**. Koden ska kompilera utan fel nu:

    ![Version](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "version")

1. Starta programmet. Från menyraden väljer **felsöka** > **Starta felsökning**, eller tryck på **F5**.

    ![Starta appen i felsökning](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "starta appen into-felsökning")

1. Ett konsolfönster öppnas, där du uppmanas att säga något (på engelska).
Resultatet av erkännande visas på skärmen.

    ![Konsolens utdata efter lyckad erkännande](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "konsolens utdata efter lyckad taligenkänning")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i den `quickstart/csharp-dotnetcore-windows` mapp.

## <a name="next-steps"></a>Nästa steg

- [Omvandla tal](how-to-translate-speech.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
