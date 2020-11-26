---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 6d20df031633df4642ce9fb5cbbc469fd7f0a5da
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188505"
---
Om du vill skapa ett Visual Studio-projekt för plattforms oberoende mobil program .NET-utveckling med Xamarin måste du:
- Konfigurera alternativ för Visual Studio-utveckling.
- Skapa projektet och välj mål arkitekturen. 
- Installera talet SDK.

### <a name="set-up-visual-studio-development-options"></a>Konfigurera alternativ för Visual Studio-utveckling

Starta genom att kontrol lera att du är korrekt konfigurerad i Visual Studio för mobil utveckling över plattformar med .NET:

1. Öppna Visual Studio 2019.

1. I meny raden i Visual Studio väljer du **verktyg**  >  **Hämta verktyg och funktioner** för att öppna Visual Studio Installer och Visa dialog rutan **ändra** .

   ![Skärm bild som visar fliken arbets belastningar, ändra dialog rutan och installations programmet för Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. På fliken **arbets belastningar** , under **Windows**, letar du upp **mobil utveckling med .net** -arbets belastning. Om kryss rutan bredvid arbets belastningen redan är vald stänger du dialog rutan **ändra** och går till steg 5.

1. Markera kryss rutan **mobil utveckling med .net** och välj **ändra**. I dialog rutan **innan vi kommer igång** väljer du **Fortsätt** för att installera mobil utveckling med .net-arbets belastning. Det kan ta en stund att installera den nya funktionen.

1. Stäng Visual Studio Installer.

### <a name="create-the-project"></a>Skapa projektet

1. I meny raden i Visual Studio väljer du **Arkiv**  >  **nytt**  >  **projekt** för att visa fönstret **skapa ett nytt projekt** .

   ![Skärm bild som visar hur du skapar ett nytt projekt i Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Hitta och välj **mobilapp (Xamarin. Forms)**.

1. Välj **Nästa** för att visa skärmen **Konfigurera ditt nya projekt** .

   ![Skärm bild som visar hur du konfigurerar det nya projektet i Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Ange *HelloWorld* i **projekt namn**.

1. På **plats** går du till och väljer eller skapar mappen där du sparar projektet i.

1. Välj **skapa** för att gå till **projekt fönstret nytt Xamarin formulär i Mobile App** .

   ![Skärm bild som visar dialog rutan nytt Xamarin Forms-projekt i mobilapp i Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Välj den **tomma** mallen.

1. I **plattform** väljer du rutorna för **Android**, **iOS** och **Windows (UWP)**.

1. Välj **OK**. Du kommer tillbaka till Visual Studio IDE, där det nya projektet skapas och visas i rutan **Solution Explorer** .

   ![Projektet HelloWorld – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Välj nu plattforms arkitektur och start projekt. I verktygsfältet Visual Studio hittar du List rutan med **plattforms plattformar** . (Om du inte ser det väljer du **Visa**  >  **Verktygsfält**  >  **Standard** för att Visa verktygsfältet som innehåller **lösnings plattformar**.) Om du kör 64-bitars Windows väljer du **x64** i list rutan. Du kan välja **x86** om du vill eftersom 64-bitars Windows också kan köra 32-bitars program. I list rutan **Start-projekt** ställer du in **HelloWorld. UWP (Universal Windows)**.

### <a name="install-the-speech-sdk"></a>Installera Speech SDK

Installera [NuGet-paketet för tal-SDK](https://aka.ms/csspeech/nuget)och referera till tal-SDK i projektet.

1. Högerklicka på din lösning i **Solution Explorer**. Välj **Hantera NuGet-paket för lösning** för att gå till fönstret **NuGet-lösning** .

1. Välj **Bläddra**.

   ![Skärm bild av dialog rutan Hantera paket för lösning när du installerar talet SDK.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. I **paket källa** väljer du NuGet.org.

1. Skriv *Microsoft. CognitiveServices. Speech* i rutan **Sök** . Välj sedan det paketet efter att det visas i Sök resultaten.

   ![Skärm bild som visar paketet Microsoft. CognitiveServices. Speech.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > IOS-biblioteket i `Microsoft.CognitiveServices.Speech` NuGet har inte bitcode aktiverat. Om du behöver bitcode-biblioteket aktiverat för ditt program ska du använda `Microsoft.CognitiveServices.Speech.Xamarin.iOS` NuGet för iOS-projektet specifikt.

1. I fönstret paket status bredvid Sök resultaten väljer du alla projekt: **HelloWorld**, **HelloWorld. Android**, **HelloWorld. iOS** och **HelloWorld. UWP**.

1. Välj **Installera**.

1. I dialog rutan **Förhandsgranska ändringar** väljer du **OK**.

1. I dialog rutan **licens godkännande** visar du licensen och väljer sedan **Jag accepterar**. Installera tal SDK-paketets referens till alla projekt. När installationen har slutförts kan du se följande varning för HelloWorld. iOS. Detta är ett känt problem och bör inte påverka din app-funktionalitet.

   > Det gick inte att lösa referensen "C:\Users\Default \. nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.Core.a". Om den här referensen krävs av din kod kan du få kompileringsfel.
