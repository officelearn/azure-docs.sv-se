---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: b5a24d83faef5a895317f162178f8bd588a1466d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620154"
---
1. Starta Visual Studio 2017.

1. Kontrollera att arbetsbelastningen **Desktop development with C++** (Skrivbordsutveckling med C++) är tillgänglig. Välj **Verktyg** > **Get Tools and Features** (Hämta verktyg och funktioner) på menyraden i Visual Studio för att öppna installationsprogrammet för Visual Studio. Om den här arbetsbelastningen redan är aktiverad går du direkt till nästa steg.

    ![Skärmbild av fliken för arbetsbelastningar i Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    Annars markerar du kryssrutan intill **Desktop development with C++** (Skrivbordsutveckling med C++).

1. Kontrollera att komponenten **NuGet-pakethanteraren** är tillgänglig. Växla sedan till fliken **Enskilda komponenter** i installationsdialogrutan för Visual Studio och välj fliken **NuGet-pakethanteraren** om den inte redan är aktiverad.

      ![Skärmbild av fliken Enskilda komponenter i Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. Om du behöver aktivera antingen C++-arbetsbelastning eller NuGet väljer du **Ändra** (i det nedre högra hörnet av dialogrutan). Installationen av de nya funktionerna tar en stund. Om båda funktionerna redan har aktiverats stänger du dialogrutan.

1. Skapa ett nytt Windows-konsolprogram för Visual C++ i Windows Desktop. Börja med att välja **Arkiv** > **Nytt** > **Projekt** på menyn. I dialogrutan **Nytt projekt** expanderar du **Installerat** > **Visual C++** > **Windows Desktop** i den vänstra fönsterrutan. Välj sedan **Windows-konsolprogram**. För projektnamnet anger du *helloworld*.

    ![Skärmbild av dialogrutan Nytt projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Om du använder 64-bitars Windows kan du byta utvecklingsplattform till `x64` med hjälp av den nedrullningsbara menyn i Visual Studio-verktygsfältet. (64-bitars versioner av Windows kan köra 32-bitars program, så det inte är ett krav.)

    ![Skärmbild av Visual Studio-verktygsfältet med x64-alternativet markerat](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. Högerklicka på lösningen i Solution Explorer och välj sedan **Hantera NuGet-paket för lösningen**.

    ![Skärmbild av Solution Explorer, med alternativet Hantera NuGet-paket för lösningen markerat](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. I det övre högra hörnet väljer du **nuget.org** i fältet **Package Source** (Paketkälla). Sök efter `Microsoft.CognitiveServices.Speech`-paketet och installera det i **helloworld**-projektet.

    ![Skärmbild av dialogrutan Hantera paket för lösningen](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Den aktuella versionen av Cognitive Services Speech SDK är `1.4.0`.

1. Acceptera licensvillkoren som visas för att påbörja installationen av NuGet-paketet.

    ![Skärmbild av dialogrutan för att acceptera licensvillkoren](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

När paketet har installerats visas en bekräftelse i Package Manager-konsolen.
