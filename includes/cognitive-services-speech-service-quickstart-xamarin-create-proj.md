---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0bcdd315fe11b7472166a5a9ad4f7395e22d2126
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72675600"
---
Om du vill skapa ett Visual Studio-projekt för plattformsoberoende mobilapp .NET-utveckling med Xamarin måste du:
- Ställ in utvecklingsalternativ för Visual Studio.
- Skapa projektet och välj målarkitekturen. 
- Installera Tal-SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurera utvecklingsalternativ för Visual Studio

Börja med att se till att du är korrekt konfigurerad i Visual Studio för mobil utveckling över flera plattformar med .NET:

1. Öppna Visual Studio 2019.

1. På menyraden i Visual Studio väljer du **Verktyg** > **hämta verktyg och funktioner** för att öppna Visual Studio Installer och visa dialogrutan **Ändra.**

   ![Fliken Arbetsbelastningar, dialogrutan Ändra, Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Hitta **mobilutvecklingen med .NET-arbetsbelastning** under **Windows**på fliken **Arbetsbelastningar.** Om kryssrutan bredvid arbetsbelastningen redan är markerad stänger du dialogrutan **Ändra** och går till steg 5.

1. Markera kryssrutan **Mobilutveckling med .NET** och välj **Ändra**. I dialogrutan **Innan vi kommer igång** väljer du **Fortsätt** att installera den mobila utvecklingen med .NET-arbetsbelastningen. Installationen av den nya funktionen kan ta ett tag.

1. Stäng Visual Studio Installer.

### <a name="create-the-project"></a>Skapa projektet

1. På menyraden i Visual Studio väljer du **Arkiv** > **nytt** > **projekt** om du vill visa fönstret Skapa ett nytt **projekt.**

   ![Skapa ett nytt projekt - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Hitta och välj **Mobilapp (Xamarin.Forms)**.

1. Välj **Nästa** om du vill visa skärmen **Konfigurera det nya projektet.**

   ![Konfigurera ditt nya projekt - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Ange *helloworld*i **Projektnamn**.

1. Gå till och markera eller skapa mappen som projektet ska sparas i i **Plats.**

1. Välj **Skapa** om du vill gå till fönstret **Nya mobilapp Xamarin Forms Project.**

   ![Dialogrutan Nytt universellt Windows-plattformsprojekt - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Markera mallen **Tom.**

1. I **Plattform**väljer du rutorna för **Android**, **iOS**och **Windows (UWP).**

1. Välj **OK**. Du är tillbaka i Visual Studio IDE, med det nya projektet skapat och synligt i fönstret **Solution Explorer.**

   ![Helloworld-projektet - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Välj nu din målplattformsarkitektur och startprojekt. Leta reda på listrutan **Lösningsplattformar** i verktygsfältet Visual Studio. (Om du inte ser det väljer du **Visa** > **verktygsfält** > **standard** för att visa verktygsfältet som innehåller **lösningsplattformar**.) Om du kör 64-bitars Windows väljer du **x64** i listrutan. Du kan välja **x86** om du vill eftersom 64-bitars Windows också kan köra 32-bitarsprogram. Ställ in helloworld i listrutan **Start-up** **Projects. UWP (Universella Fönster)**.

### <a name="install-the-speech-sdk"></a>Installera Speech SDK

Installera [paketet Speech SDK NuGet](https://aka.ms/csspeech/nuget)och referera till Tal-SDK i projektet.

1. Högerklicka på lösningen i **Solution Explorer.** Välj **Hantera NuGet-paket för lösning för** att gå till fönstret **NuGet - Solution.**

1. Välj **Bläddra**.

   ![Skärmbild av dialogrutan Hantera paket för lösningen](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Välj nuget.org i **Paketkällan.**

1. Ange *Microsoft.CognitiveServices.Speech*i **sökrutan** . Välj sedan det paketet när det visas i sökresultaten.

   ![Skärmbild av dialogrutan Hantera paket för lösningen](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > IOS-biblioteket `Microsoft.CognitiveServices.Speech` i NuGet har inte bitkod aktiverat. Om du behöver bitkodbiblioteket aktiverat för `Microsoft.CognitiveServices.Speech.Xamarin.iOS` ditt program använder du NuGet för iOS-projektet specifikt.

1. I fönstret paketstatus bredvid sökresultaten väljer du alla projekt: **helloworld**, **helloworld. Android**, **helloworld.iOS**och **helloworld. UWP**.

1. Välj **Installera**.

1. Välj **OK**i dialogrutan **Förhandsgranska ändringar** .

1. Visa licensen i dialogrutan **Licensacceptans** och välj sedan **Jag accepterar**. Installera paketet Speech SDK-paketreferens till alla projekt. När installationen har slutförts kan följande varning för helloworld.iOS visas. Detta är ett känt problem och bör inte påverka appens funktioner.

   > Det gick inte att lösa referensen "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Om den här referensen krävs av koden kan du få kompileringsfel.
