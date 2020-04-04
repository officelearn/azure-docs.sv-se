---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 5db99a9d500b05fa6886dce2f29087920f8a1790
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658274"
---
Om du vill skapa ett UWP-utveckling (Visual Studio-projekt för UWP) måste du konfigurera utvecklingsalternativ för Visual Studio, skapa projektet, välja målarkitektur, konfigurera ljudinspelning och installera Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurera utvecklingsalternativ för Visual Studio

Börja med att kontrollera att du är korrekt konfigurerad i Visual Studio för UWP-utveckling:

1. Öppna Visual Studio 2019 för att visa **Start-fönstret.**

   ![Startfönster - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Välj **Fortsätt utan kod** för att gå till Visual Studio IDE.

1. På menyraden i Visual Studio väljer du **Verktyg** > **hämta verktyg och funktioner** för att öppna Visual Studio Installer och visa dialogrutan **Ändra.**

   ![Fliken Arbetsbelastningar, dialogrutan Ändra, Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Hitta den universella utvecklingsarbetsbelastningen för **Windows-plattformen** under **Windows**på fliken **Arbetsbelastningar.** Om kryssrutan bredvid arbetsbelastningen redan är markerad stänger du dialogrutan **Ändra** och går till steg 6.

1. Markera kryssrutan **Universell utveckling av Windows-plattformen,** välj **Ändra**och välj sedan **Fortsätt** att installera UWP-utvecklingsarbetsbelastningen innan vi **kommer igång.** Installationen av den nya funktionen kan ta en stund.

1. Stäng Visual Studio Installer.

### <a name="create-the-project-and-select-the-target-architecture"></a>Skapa projektet och välj målarkitektur

Skapa sedan projektet:

1. I menyraden i Visual Studio väljer du **Arkiv** > **nytt** > **projekt** för att visa fönstret Skapa ett nytt **projekt.**

   ![Skapa ett nytt projekt - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Sök efter och välj **Tom App (Universella Fönster)**. Se till att du väljer C#-versionen av den här projekttypen (i motsats till Visual Basic).

1. Välj **Nästa** om du vill visa skärmen **Konfigurera det nya projektet.**

   ![Konfigurera ditt nya projekt - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. Skriv i `helloworld` **Projektnamn**.

1. Navigera till och markera eller skapa mappen som projektet ska sparas i i **Plats.**

1. Välj **Skapa** för att gå till fönstret **Nytt universellt Windows Platform Project.**

   ![Dialogrutan Nytt universellt Windows-plattformsprojekt - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. I **Minsta version** (den andra listrutan) väljer du Windows **10 Fall Creators Update (10.0; Bygg 16299)**, vilket är minimikravet för Tal SDK.

1. I **Målversionen** (den första listrutan) väljer du ett värde som är identiskt med eller senare än värdet i **Minimum-versionen**.

1. Välj **OK**. Du är tillbaka i Visual Studio IDE, med det nya projektet skapat och synligt i fönstret **Solution Explorer.**

   ![helloworld projekt - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Välj nu din målplattformsarkitektur. Leta reda på listrutan **Lösningsplattformar** i verktygsfältet Visual Studio. (Om du inte ser det väljer du **Visa** > verktygsfält**standard** för att visa**verktygsfältet** > som innehåller **lösningsplattformar**.) Om du kör 64-bitars Windows väljer du **x64** i listrutan. 64-bitars Windows kan också köra 32-bitarsprogram, så du kan välja **x86** om du vill.

> [!NOTE]
> Speech SDK stöder endast Intel-kompatibla processorer. ARM-processorer stöds för närvarande inte.

### <a name="set-up-audio-capture"></a>Konfigurera ljudinspelning

Låt sedan projektet fånga in ljudinmatning:

1. I **Solution Explorer**dubbelklickar du på **Package.appxmanifest** för att öppna paketprogrammanifestet.

1. Välj fliken **Funktioner**.

   ![Fliken Funktioner, Paketprogrammanifest - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Markera rutan för **mikrofonfunktionen.**

1. På menyraden väljer du Spara > **filpaket.appxmanifest** för att spara ändringarna. **File**

### <a name="install-the-speech-sdk"></a>Installera Speech SDK

Installera slutligen [speech SDK NuGet-paketet](https://aka.ms/csspeech/nuget)och referera till Tal-SDK i projektet:

1. Högerklicka på din lösning i **Solution Explorer**och välj **Hantera NuGet-paket för lösning för** att gå till fönstret **NuGet - Solution.**

1. Välj **Bläddra**.

   ![Skärmbild av dialogrutan Hantera paket för lösningen](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Välj **nuget.org**i **Paketkällan**.

1. I rutan **Sök** `Microsoft.CognitiveServices.Speech`anger du och väljer sedan paketet när det visas i sökresultaten.

   ![Skärmbild av dialogrutan Hantera paket för lösningen](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. Välj ditt **helloworld-projekt** i fönstret för paketstatus bredvid sökresultaten.

1. Välj **Installera**.

1. Välj **OK**i dialogrutan **Förhandsgranska ändringar** .

1. Visa licensen i dialogrutan **Licensacceptans** och välj sedan **Jag accepterar**. Paketinstallationen börjar och när installationen är klar visas ett meddelande som `Successfully installed 'Microsoft.CognitiveServices.Speech 1.11.0' to helloworld`liknar följande text i **fönstret Utdata:** .
