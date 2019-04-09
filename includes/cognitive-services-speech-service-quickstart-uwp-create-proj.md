---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 8612c1cc2867d27a86b4b90b1ba63c097ad43cf7
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59041655"
---
1. Starta Visual Studio 2017.

1. Kontrollera att arbetsbelastningen **Universal Windows Platform-utveckling** är tillgänglig. Välj **Verktyg** > **Get Tools and Features** (Hämta verktyg och funktioner) på menyraden i Visual Studio för att öppna installationsprogrammet för Visual Studio. Om den här arbetsbelastningen redan är aktiverad stänger du dialogrutan.

    ![Skärmbild av Visual Studio-installationsprogrammet med fliken Arbetsbelastningar markerad](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    Annars markerar du rutan bredvid **.NET cross-platform development** (Plattformsoberoende .NET-utveckling) och väljer **Ändra** i det nedre högra hörnet av dialogrutan. Installationen av den nya funktionen tar en stund.

1. Skapa en tom Visual C#-baserad Universal Windows-app. Börja med att välja **Arkiv** > **Nytt** > **Projekt** på menyn. I dialogrutan **Nytt projekt** expanderar du **Installerat** > **Visual C#** > **Windows Universal** i den vänstra rutan. Välj sedan **Tom app (Universal Windows)**. För projektnamnet anger du *helloworld*.

    ![Skärmbild av dialogrutan Nytt projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Speed SDK kräver att ditt program skapas för Windows 10 Fall Creators Update eller senare. I fönstret **New Universal Windows Platform Project** (Nytt Universal Windows Platform-projekt) som visas väljer du **Windows 10 Fall Creators Update (10.0; Build 16299)** som **minimiversion**. I rutan **Målversion** väljer du den här eller en senare version och klickar sedan på **OK**.

    ![Skärmbild av fönstret New Universal Windows Platform Project (Nytt Universal Windows Platform-projekt)](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Om du använder 64-bitars Windows kan du byta utvecklingsplattform till `x64` med hjälp av den nedrullningsbara menyn i Visual Studio-verktygsfältet. (64-bitars Windows kan köra 32-bitarsprogram, så du kan lämna inställningen som `x86` om du föredrar det.)

   ![Skärmbild av Visual Studio-verktygsfältet med x64 markerat](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Speech SDK stöder endast Intel-kompatibla processorer. ARM stöds inte för närvarande.

1. Installera och referera till [NuGet-paketet för Speech SDK](https://aka.ms/csspeech/nuget). Högerklicka på lösningen i Solution Explorer och välj sedan **Hantera NuGet-paket för lösningen**.

    ![Skärmbild av Solution Explorer, med alternativet Hantera NuGet-paket för lösningen markerat](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. I det övre högra hörnet väljer du **nuget.org** i fältet **Package Source** (Paketkälla). Sök efter `Microsoft.CognitiveServices.Speech`-paketet och installera det i **helloworld**-projektet.

    ![Skärmbild av dialogrutan Hantera paket för lösningen](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Installera NuGet-paket")

1. Acceptera licensvillkoren som visas för att påbörja installationen av NuGet-paketet.

    ![Skärmbild av dialogrutan för att acceptera licensvillkoren](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "Acceptera licensen")

1. Följande utdatarad visas i Package Manager-konsolen.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.4.0' to helloworld
   ```

1. Eftersom programmet använder mikrofonen för talindata lägger du till funktionen **Mikrofon** i projektet. I Solution Explorer dubbelklickar du på **Package.appxmanifest** för att redigera programmanifestet. Sedan växlar du till fliken **Funktioner**, markerar kryssrutan för funktionen **Mikrofon** och sparar dina ändringar.

   ![Skärmbild av programmanifestet i Visual Studio med Funktioner och Mikrofon markerat](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
