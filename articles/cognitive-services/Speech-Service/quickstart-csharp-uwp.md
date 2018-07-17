---
title: 'Snabbstart: Känna igen tal i C# i en UWP-app i Cognitive Services tal SDK | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Lär dig att känna igen tal i en UWP-app med hjälp av Cognitive Services tal SDK
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 4599c3c4c69397a1ab1f65c246e4440085b8bb91
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072586"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i en UWP-app med hjälp av tal-SDK

I den här artikeln får du lära dig hur du skapar en Universal Windows Platform (UWP)-program med hjälp av Cognitive Services tal SDK för att transkribera tal till text.
Programmet är baserad på den [Microsoft Cognitive Services tal SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017.

> [!NOTE]
> UWP-appar som skapats med SDK: N för tal ännu skickar inte Windows App Certification Kit (WACK).
> Det är möjligt, skicka en app på Windows Store inte är separat inläsning av appar.
> Detta kommer att åtgärdas i en kommande version.

## <a name="prerequisites"></a>Förutsättningar

* En prenumerationsnyckel för Speech-tjänsten. Se [prova speech-tjänsten utan kostnad](get-started.md).
* En Windows-dator (Windows 10 Fall Creators Update eller senare) med en fungerande mikrofon.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition eller senare.
* Den **Universal Windows Platform-utveckling** arbetsbelastningen i Visual Studio.You kan aktivera den på **verktyg** \> **hämta verktyg och funktioner**.

  ![Aktivera Universal Windows Platform-utveckling](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Skapa en ny Visual C# Windows Universal tom App i Visual Studio 2017. I den **nytt projekt** dialogrutan i den vänstra rutan expanderar **installerad** \> **Visual C#** \> **Windows Universal** och välj sedan **tom App (Universal Windows)**. Projektnamnet, ange *helloworld*.

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. I den **nya Universal Windows Platform-projekt** fönster som POP, Välj **Windows 10 Fall Creators Update (10.0; Skapa 16299)** som **minimiversion**, och detta eller senare version som **målversion**, klicka sedan på **OK**.

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Om du kör på en 64-bitars Windows-installation, kan du växla build-plattformen för att `x64`:

   ![Växla build-plattform till x64](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > För tillfället stöder inte tal SDK: N till mål ARM-processorer.

1. Installera och referera till den [tal NuGet-paketet SDK](https://aka.ms/csspeech/nuget). Högerklicka på lösningen i Solution Explorer och välj **hantera NuGet-paket för lösningen**.

    ![Högerklicka på Hantera NuGet-paket för lösningen](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. I det övre högra hörnet i den **Paketkällan** väljer **Nuget.org**. Sök efter och installera den `Microsoft.CognitiveServices.Speech` paketera och installera den i den **helloworld** projekt.

    ![Installera NuGet-paketet för Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "installera Nuget-paketet")

1. Acceptera licensvillkoren på skärmen som visas för licens:

    ![Acceptera licensvillkoren](media/sdk/qs-csharp-uwp-06-nuget-license.png "acceptera licensvillkoren")

1. Du bör se följande utdatarad i Package Manager-konsolen:

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>Lägg till exempelkoden

1. I Solution Explorer dubbelklickar du på **Package.appxmanifest** redigera programmanifestet.
   Välj den **funktioner** fliken, markerar kryssrutan för den **mikrofon** funktion, och spara dina ändringar.

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Redigera din app UI genom att dubbelklicka på `MainPage.xaml` i Solution Explorer.
   I den designer XAML vyn infoga följande kodavsnitt i XAML i rutnätet taggen (mellan `<Grid>` och `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Redigera XAML bakomliggande kod genom att dubbelklicka på `MainPage.xaml.cs` i Solution Explorer (det är grupperade under den `MainPage.xaml` objekt).
   Ersätt all kod med följande:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. I den `SpeechRecognitionFromMicrophone_ButtonClicked` hanteraren, Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. I den `SpeechRecognitionFromMicrophone_ButtonClicked` hanteraren, Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerade med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara alla ändringar i projektet.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Skapa programmet. Från menyraden väljer **skapa** > **skapa lösning**. Koden ska kompilera utan fel nu:

    ![Version](media/sdk/qs-csharp-uwp-08-build.png "version")

1. Starta programmet. Från menyraden väljer **felsöka** > **Starta felsökning**, eller tryck på **F5**.

    ![Starta appen i felsökning](media/sdk/qs-csharp-uwp-09-start-debugging.png "starta appen into-felsökning")

1. Ett GUI-fönster öppnas. Klicka först på den **Aktivera mikrofon** knappen och bekräfta behörighet-begäran som visas.

    ![Starta appen i felsökning](media/sdk/qs-csharp-uwp-10-access-prompt.png "starta appen into-felsökning")

1. Klicka sedan på den **taligenkänning med mikrofon indata** och talar ett kort fras i mikrofonen.
   Igenkänningsresultatet tal ska visas i det grafiska Användargränssnittet.

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i den `quickstart/csharp-uwp` mapp.

## <a name="next-steps"></a>Nästa steg

- [Omvandla tal](how-to-translate-speech.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
