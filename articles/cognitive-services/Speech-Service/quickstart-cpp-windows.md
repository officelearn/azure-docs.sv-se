---
title: 'Snabbstart: Identifiera tal i C++ på Windows med hjälp av Speech Service SDK'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du identifierar tal i C++ på Windows Desktop med hjälp av Speech Service SDK
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: 8947ba3f39cebf51e956db0d841e393963832bc4
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218751"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Snabbstart: Identifiera tal i C++ på Windows med hjälp av tal-API

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln får skapa du ett C++-konsolprogram för Windows. Du använder Cognitive Services [Speech SDK](speech-sdk.md) för att transkribera tal till text i realtid från datorns mikrofon. Programmet har skapats med [Speech SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017 (alla versioner).

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en prenumerationsnyckel för Speech-tjänsten för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Mer information finns i [Prova Speech-tjänsten kostnadsfritt](get-started.md).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Starta Visual Studio 2017.

1. Kontrollera att arbetsbelastningen **Desktop development with C++** (Skrivbordsutveckling med C++) är tillgänglig. Välj **Verktyg** > **Get Tools and Features** (Hämta verktyg och funktioner) på menyraden i Visual Studio för att öppna installationsprogrammet för Visual Studio. Om den här arbetsbelastningen redan är aktiverad går du direkt till nästa steg. 

    ![Skärmbild av fliken för arbetsbelastningar i Visual Studio](media/sdk/vs-enable-cpp-workload.png)

    Annars markerar du kryssrutan intill **Desktop development with C++** (Skrivbordsutveckling med C++). 

1. Kontrollera att komponenten **NuGet-pakethanteraren** är tillgänglig. Växla sedan till fliken **Enskilda komponenter** i installationsdialogrutan för Visual Studio och välj fliken **NuGet-pakethanteraren** om den inte redan är aktiverad.

      ![Skärmbild av fliken Enskilda komponenter i Visual Studio](media/sdk/vs-enable-nuget-package-manager.png)

1. Om du behöver aktivera antingen C++-arbetsbelastning eller NuGet väljer du **Ändra** (i det nedre högra hörnet av dialogrutan). Installationen av de nya funktionerna tar en stund. Om båda funktionerna redan har aktiverats stänger du dialogrutan.

1. Skapa ett nytt Windows-konsolprogram för Visual C++ i Windows Desktop. Börja med att välja **Arkiv** > **Nytt** > **Projekt** på menyn. I dialogrutan **Nytt projekt** expanderar du **Installerat** > **Visual C++** > **Windows Desktop** i den vänstra fönsterrutan. Välj sedan **Windows-konsolprogram**. För projektnamnet anger du *helloworld*.

    ![Skärmbild av dialogrutan Nytt projekt](media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Om du använder 64-bitars Windows kan du byta utvecklingsplattform till `x64` med hjälp av den nedrullningsbara menyn i Visual Studio-verktygsfältet. (64-bitars versioner av Windows kan köra 32-bitars program, så det inte är ett krav.)

    ![Skärmbild av Visual Studio-verktygsfältet med x64-alternativet markerat](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. Högerklicka på lösningen i Solution Explorer och välj sedan **Hantera NuGet-paket för lösningen**.

    ![Skärmbild av Solution Explorer, med alternativet Hantera NuGet-paket för lösningen markerat](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. I det övre högra hörnet väljer du **nuget.org** i fältet **Package Source** (Paketkälla). Sök efter `Microsoft.CognitiveServices.Speech`-paketet och installera det i **helloworld**-projektet.

    ![Skärmbild av dialogrutan Hantera paket för lösningen](media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Den aktuella versionen av Cognitive Services Speech SDK är `1.1.0`.

1. Acceptera licensvillkoren som visas för att påbörja installationen av NuGet-paketet.

    ![Skärmbild av dialogrutan för att acceptera licensvillkoren](media/sdk/qs-cpp-windows-05-nuget-license.png)

När paketet har installerats visas en bekräftelse i Package Manager-konsolen.

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna källfilen *helloworld.cpp*. Ersätt all kod under den första include-instruktionen (`#include "stdafx.h"` eller `#include "pch.h"`) med följande:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. I menyraden väljer du **Skapa** > **Skapa lösning**. Koden ska kompileras utan fel.

   ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-cpp-windows-06-build.png)

1. Starta programmet. I menyraden väljer du **Felsök** > **Starta felsökning**, eller tryck på **F5**.

   ![Skärmbild av Visual Studio-programmet med Starta felsökning markerat](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Ett konsolfönster öppnas där du uppmanas att säga något. Säg en engelsk fras eller en mening. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i samma fönster.

   ![Skärmbild av konsolutdata efter lyckad taligenkänning](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i mappen `quickstart/cpp-windows`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera avsikter från tal med hjälp av Speech SDK för C++](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>Se även

- [Översätta tal](how-to-translate-speech-csharp.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
