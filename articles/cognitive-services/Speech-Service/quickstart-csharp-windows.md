---
title: 'Snabbstart: Identifiera tal med kognitiva Services tal C# SDK för Windows | Microsoft Docs'
description: Lär dig att identifiera tal med C#-SDK för tal-tjänsten.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 9f37547b4b183594410b4e16b872645407ff4a6f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "35356249"
---
# <a name="quickstart-recognize-speech-using-the-cognitive-services-speech-c-sdk"></a>Snabbstart: Identifiera tal med kognitiva Services tal C# SDK

Lär dig hur du skapar ett C#-konsolprogram i Windows med kognitiva Services tal SDK för att transkribera tal till text i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

* En prenumeration nyckel för tjänsten tal. Se [prova tal tjänsten gratis](get-started.md).
* Visual Studio 2017, Community Edition eller högre.
* Den **.NET skrivbord development** arbetsbelastning i Visual Studio. Du kan aktivera den i **verktyg** \> **hämta verktyg och funktioner**. 

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Skapa en ny Visual C#-Konsolapp i Visual Studio 2017. I den **nytt projekt** dialogrutan i den vänstra rutan Expandera **installerad** och välj sedan **Konsolapp (.NET Framework)**. Projektnamnet, ange *CsharpHelloSpeech*.

    ![Skapa Visual C#-Konsolapp (.NET Framework)](media/sdk/speechsdk-05-vs-cs-new-console-app.png "skapa Visual C#-Konsolapp")

2. Installera och referera till tal SDK NuGet-paketet. Högerklicka på lösningen i Solution Explorer och markera **hantera NuGet-paket för lösningen**.

    ![Högerklicka på Hantera NuGet-paket för lösningen](media/sdk/speechsdk-06-vs-cs-manage-nuget-packages.png "hantera NuGet-paket för lösningen")

3. I det övre högra hörnet i den **Paketkällan** väljer **Nuget.org**. Sök efter och installera den `Microsoft.CognitiveServices.Speech` paketet och installera det i den **CsharpHelloSpeech** projekt.

    ![Installera NuGet-paketet för Microsoft.CognitiveServices.Speech](media/sdk/speechsdk-08-vs-cs-nuget-install.png "installera Nuget-paketet")

4. På skärmen licens som gör att acceptera licensvillkoren:

    ![Acceptera licensvillkoren](media/sdk/speechsdk-09-vs-cs-nuget-license.png "acceptera licensvillkoren")

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Skapa en plattformskonfigurationen motsvarande PC-arkitektur

I det här avsnittet kan du lägga till en ny plattform i konfigurationen som matchar din processorarkitektur.

1. Starta Configuration Manager. Välj **skapa** > **Configuration Manager**.

    ![Starta configuration manager](media/sdk/speechsdk-12-vs-cs-cfg-manager-click.png "starta configuration manager")

2. I den **Configuration Manager** dialogrutan Lägg till en ny plattform. Från den **aktiv lösning plattform** listrutan, Välj **ny**.

    ![Lägg till en ny plattform under configuration manager-fönstret](media/sdk/speechsdk-14-vs-cs-cfg-manager-new.png "lägga till en ny plattform under configuration manager-fönstret")

3. Om du kör 64-bitars Windows, skapa en ny plattformskonfiguration med namnet `x64`. Om du använder 32-bitars Windows, skapa en ny plattformskonfiguration med namnet `x86`. I den här artikeln får du skapa en `x64` plattformskonfigurationen. 

    ![Lägg till en ny plattform med namnet ”x64” på 64-bitars Windows](media/sdk/speechsdk-15-vs-cs-cfg-manager-add-x64.png "Lägg till x64 plattform")

## <a name="add-the-sample-code"></a>Lägg till exempelkoden

1. I den `Program.cs` för Visual Studio-projekt att ersätta brödtexten i den `Program` klassen med följande. Kontrollera att du ersätta den prenumeration och region med en som du hämtade för tjänsten.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#code)]

2. När du klistrat in koden, den `Main()` metoden måste likna som visas i följande skärmbild:

    ![Main-metoden när du klistrat in koden](media/sdk/speechsdk-17-vs-cs-paste-code.png "slutliga Main-metoden")

3. Visual Studio IntelliSense visar referenser till tal-SDK-klasserna som inte kunde matchas. Åtgärda felet genom att lägga till följande `using` instruktionen till början av koden (antingen manuellt eller med hjälp av Visual Studio [snabb åtgärder](https://docs.microsoft.com/visualstudio/ide/quick-actions)).

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#usingstatement)]

    ![Använd åtgärden snabbt att lägga till den saknade med hjälp av instruktionen](media/sdk/speechsdk-18-vs-cs-add-using.png "IntelliSense lösa problem")

4. Kontrollera att IntelliSense markeringen är löst och spara ändringarna i projektet.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Skapa programmet. I verktygsfältet och väljer **skapa** > **skapa lösning**. Koden ska kompileras utan fel nu:

    ![Lyckad build](media/sdk/speechsdk-20-vs-cs-build.png "lyckade build")

2. Starta programmet. I verktygsfältet och väljer **felsöka** > **Start Debugging**, eller tryck på **F5**. 

    ![Starta appen i felsökning](media/sdk/speechsdk-21-vs-cs-f5.png "starta appen i Felsökning")

3. Ett konsolfönster öppnas, där du uppmanas att säga något (på engelska).
Resultatet av redovisning visas på skärmen.

    ![Konsolen utdata efter lyckad recognition](media/sdk/speechsdk-22-cs-vs-console-output.png "konsolen utdata efter lyckad igenkänning")

## <a name="download-code"></a>Hämta koden

Senaste uppsättning exempel, finns det [kognitiva Services tal SDK exempel GitHub-lagringsplatsen](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Nästa steg

- [Översätta tal](how-to-translate-speech.md)
- [Anpassa tal modeller](how-to-customize-speech-models.md)
