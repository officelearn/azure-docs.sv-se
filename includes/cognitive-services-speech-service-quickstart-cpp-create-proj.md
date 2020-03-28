---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 1287eb5de1391f40c4409d5d10522d8979ae4207
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78383833"
---
Om du vill skapa ett Visual Studio-projekt för C++-skrivbordsutveckling måste du ställa in utvecklingsalternativ för Visual Studio, skapa projektet, välja målarkitektur och installera Tal-SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurera utvecklingsalternativ för Visual Studio

Börja med att kontrollera att du är korrekt konfigurerad i Visual Studio för C++-skrivbordsutveckling:

1. Öppna Visual Studio 2019 för att visa **Start-fönstret.**

   ![Startfönster - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Välj **Fortsätt utan kod** för att gå till Visual Studio IDE.

1. På menyraden i Visual Studio väljer du **Verktyg** > **hämta verktyg och funktioner** för att öppna Visual Studio Installer och visa dialogrutan **Ändra.**

   ![Fliken Arbetsbelastningar, dialogrutan Ändra, Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Hitta **skrivbordsutvecklingen med C++-arbetsbelastning** under **Windows**på fliken **Arbetsbelastningar.** Om kryssrutan bredvid arbetsbelastningen inte redan är markerad markerar du den.

1. Leta reda på kryssrutan **Nuget package manager** på fliken **Enskilda komponenter.** Om kryssrutan inte redan är markerad markerar du den.

1. Markera knappen i hörnet med etiketten **Stäng** eller **Ändra**. (Knappnamnet varierar beroende på om du har valt några funktioner för installation.) Om du väljer **Ändra**börjar installationen, vilket kan ta en stund.

1. Stäng Visual Studio Installer.

### <a name="create-the-project-and-select-the-target-architecture"></a>Skapa projektet och välj målarkitektur

Skapa sedan projektet:

1. I menyraden i Visual Studio väljer du **Arkiv** > **nytt** > **projekt** för att visa fönstret Skapa ett nytt **projekt.**

   ![Skapa ett nytt projekt, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Sök efter och välj **Konsolapp**. Se till att du väljer C++-versionen av den här projekttypen (i motsats till C# eller Visual Basic).

1. Välj **Nästa** om du vill visa skärmen **Konfigurera det nya projektet.**

   ![Konfigurera ditt nya projekt, C++ - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. Skriv i `helloworld` **Projektnamn**.

1. Navigera till och markera eller skapa mappen som projektet ska sparas i i **Plats.**

Välj nu din målplattformsarkitektur. Leta reda på listrutan **Lösningsplattformar** i verktygsfältet Visual Studio. (Om du inte ser det väljer du **Visa** > verktygsfält**standard** för att visa**verktygsfältet** > som innehåller **lösningsplattformar**.) Om du kör 64-bitars Windows väljer du **x64** i listrutan. 64-bitars Windows kan också köra 32-bitarsprogram, så du kan välja **x86** om du vill.

### <a name="install-the-speech-sdk"></a>Installera Speech SDK

Installera slutligen [speech SDK NuGet-paketet](https://aka.ms/csspeech/nuget)och referera till Tal-SDK i projektet:

1. Högerklicka på din lösning i **Solution Explorer**och välj **Hantera NuGet-paket för lösning för** att gå till fönstret **Nuget - Solution.**

1. Välj **Bläddra**.

   ![NuGet - Fliken Lösning, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Välj **nuget.org**i **Paketkällan**.

1. I rutan **Sök** `Microsoft.CognitiveServices.Speech`anger du och väljer sedan paketet när det visas i sökresultaten.

   ![Microsoft.CognitiveServices.Speech C++-paketinstallation - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. Välj ditt **helloworld-projekt** i fönstret för paketstatus bredvid sökresultaten.

1. Välj **Installera**.

1. Välj **OK**i dialogrutan **Förhandsgranska ändringar** .

1. Visa licensen i dialogrutan **Licensacceptans** och välj sedan **Jag accepterar**. Paketinstallationen börjar och när installationen är klar visas ett meddelande som `Successfully installed 'Microsoft.CognitiveServices.Speech 1.10.0' to helloworld`liknar följande text i **fönstret Utdata:** .
