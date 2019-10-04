---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837494"
---
Om du vill skapa ett Visual Studio-projekt för plattforms oberoende mobil program .NET-utveckling med Xamarin måste du ställa in utvecklings alternativ för Visual Studio, skapa projektet, välja mål arkitekturen och installera talet SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurera alternativ för Visual Studio-utveckling

Starta genom att kontrol lera att du är korrekt konfigurerad i Visual Studio för mobil utveckling över plattformar med .NET:

1. Öppna Visual Studio 2019.

1. I meny raden i Visual Studio väljer du **verktyg** > **Hämta verktyg och funktioner** för att öppna Visual Studio Installer och Visa dialog rutan **ändra** .

   ![Fliken arbets belastningar, ändra dialog rutan, Visual Studio Installer](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. På fliken **arbets belastningar** , under **Windows**, letar du upp **mobil utveckling med .net** -arbets belastning. Om kryss rutan bredvid arbets belastningen redan är vald stänger du dialog rutan **ändra** och går till steg 5.

1. Markera kryss rutan **mobil utveckling med .net** , Välj **ändra**. i dialog rutan **innan vi kom igång** väljer du **Fortsätt** för att installera mobil utveckling med .net-arbets belastning. Det kan ta en stund att installera den nya funktionen.

1. Stäng Visual Studio Installer.

### <a name="create-the-project"></a>Skapa projektet

1. I meny raden i Visual Studio väljer du **fil** > **nytt** > -**projekt** för att visa fönstret **skapa ett nytt projekt** .

   ![Skapa ett nytt projekt – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Hitta och välj **mobilapp (Xamarin Forms)** .

1. Välj **Nästa** för att visa skärmen **Konfigurera ditt nya projekt** . 

   ![Konfigurera det nya projektet – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Skriv `helloworld` i **projekt namn**.

1. I **plats**navigerar du till och väljer eller skapar mappen där du vill spara projektet i.

1. Välj **skapa** för att gå till **projekt fönstret nytt Xamarin formulär i Mobile App** .

   ![Dialog rutan nytt Universell Windows-plattform projekt – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Välj **Tom** mall

1. I **plattform**, kryss rutor för **Android**, **iOS** och **Windows (UWP)** .

1. Välj **OK**. Du kommer tillbaka till Visual Studio IDE, där det nya projektet skapas och visas i rutan **Solution Explorer** .

   ![HelloWorld-projekt – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Välj nu plattforms plattform och start projekt. I Visual Studio-verktygsfältet hittar du List rutan med **plattforms plattformar** . (Om du inte ser det väljer du **visa** > **verktygsfält** > **standard** för att Visa verktygsfältet som innehåller **plattform för lösningar**.) Om du kör 64-bitars Windows väljer du **x64** i list rutan. 64-bitars Windows kan också köra 32-bitars program, så du kan välja **x86** om du föredrar det. I list rutan för **Start projekt** ställer du in HelloWorld. UWP (Universal Windows).

### <a name="install-the-speech-sdk"></a>Installera Speech SDK

Installera [NuGet-paketet för tal-SDK](https://aka.ms/csspeech/nuget)och referera till tal-SDK: n i projektet:

1. I **Solution Explorer**högerklickar du på din lösning och väljer **Hantera NuGet-paket för lösning** för att gå till fönstret **NuGet-lösning** .

1. Välj **Bläddra**.

   ![Skärmbild av dialogrutan Hantera paket för lösningen](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. I **paket källa**väljer du **NuGet.org**.

1. I **sökrutan anger du @no__t** -1 och väljer sedan det paketet när det visas i Sök resultatet.

   ![Skärmbild av dialogrutan Hantera paket för lösningen](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > Obs! iOS-biblioteket i Microsoft. CognitiveServices. Speech NuGet har inte bitcode aktiverat. Om du behöver bitcode-aktiverat bibliotek för ditt program ska du använda Microsoft. CognitiveServices. Speech. Xamarin. iOS NuGet för iOS-projektet specifikt.

1. I fönstret paket status bredvid Sök resultaten väljer du alla projekt. **HelloWorld**, **HelloWorld. Android**, **HelloWorld. iOS** och **HelloWorld. UWP**.

1. Välj **Installera**.

1. I dialog rutan **Förhandsgranska ändringar** väljer du **OK**.

1. I dialog rutan **licens godkännande** visar du licensen och väljer sedan **Jag accepterar** och installerar tal referens för tal-SDK i alla projekt. När installationen har slutförts kan du se följande varning för HelloWorld. iOS. Detta är ett känt problem och bör inte påverka din app-funktionalitet.

> Det gick inte att lösa referensen "C:\Users\Default @ no__t-0nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Om den här referensen krävs av din kod kan du få kompileringsfel.
