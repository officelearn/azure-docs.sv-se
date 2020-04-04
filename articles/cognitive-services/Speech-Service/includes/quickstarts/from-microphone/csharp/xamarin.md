---
title: 'Snabbstart: Känna igen tal från en mikrofon, C# (Xamarin) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar du ett C# Xamarin-program över flera plattformar för UWP(Universal Windows Platform), Android och iOS med hjälp av Cognitive Services Speech SDK. Du transkriberar tal till text i realtid från enhetens eller simulatorns mikrofon. Programmet är byggt med Speech SDK NuGet Package och Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 98181bac7cf9aca85766d78fcd753ad8bdabc3ee
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659561"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=xamarin)
> * Se till att du har tillgång till en mikrofon för ljudinspelning

Om du redan har gjort det här, bra. Låt oss fortsätta.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Lägg till exempelkod för det gemensamma helloworld-projektet

Det gemensamma helloworld-projektet innehåller plattformsoberoende implementeringar för ditt plattformsoberoende program. Lägg nu till XAML-koden som definierar programmets användargränssnitt och lägg till C#-koden bakom implementeringen.

1. Öppna **i Solution Explorer**, under `MainPage.xaml`det gemensamma helloworld-projektet .

1. I designerns XAML-vy infogar du följande XAML-kodavsnitt `<StackLayout>` `</StackLayout>`i **rutnätstaggen** mellan och:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. Öppna **Solution Explorer**den bakomgående källfilen `MainPage.xaml.cs`för kod bakom i Solution Explorer . Det är grupperat `MainPage.xaml`under .

1. Ersätt all kod i den med följande utdrag:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Leta reda på `OnRecognitionButtonClicked` strängen `YourSubscriptionKey`i källfilens hanterare och ersätt den med prenumerationsnyckeln.


1. Leta `OnRecognitionButtonClicked` reda på strängen `YourServiceregion`i hanteraren och ersätt den med **regionidentifieraren** från [den region](https://aka.ms/speech/sdkregion) som är associerad med din prenumeration. (Använd `westus` till exempel för den kostnadsfria provprenumerationen.)

1. Därefter måste du skapa en [Xamarin-tjänst](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), som används för att fråga mikrofonbehörigheter från olika plattformsprojekt, till exempel UWP, Android och iOS. Det gör du genom att lägga till en ny mapp med namnet *Tjänster* under helloworld-projektet och skapa en ny C#-källfil under den. Du kan högerklicka på mappen *Tjänster* och välja **Lägg till** > ny**artikelkodfil****New Item** > . Byt namn `IMicrophoneService.cs`på filen och placera all kod från följande utdrag i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Lägga till exempelkod för `helloworld.Android` projektet

Lägg nu till C#-koden som definierar den Android-specifika delen av programmet.

1. I **Solution Explorer**, under helloworld. Android-projekt, `MainActivity.cs`öppna .

1. Ersätt all kod i den med följande utdrag:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Lägg sedan till Android-specifik implementering för `MicrophoneService` genom att skapa den nya mappen *Tjänster* under helloworld. Android-projektet. Därefter skapar du en ny C#-källfil under den. Byt namn `MicrophoneService.cs`på filen . Kopiera och klistra in följande kodavsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Därefter öppnar `AndroidManifest.xml` du under mappen *Egenskaper.* Lägg till följande inställning för användningsbehörighet för mikrofonen mellan `<manifest>` och: `</manifest>`

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Lägga till exempelkod för `helloworld.iOS` projektet

Lägg nu till C#-koden som definierar den iOS-specifika delen av programmet. Skapa även Apple-enhetsspecifika konfigurationer till helloworld.iOS-projektet.

1. Öppna i **Solution Explorer**under helloworld.iOS-projektet `AppDelegate.cs`.

1. Ersätt all kod i den med följande utdrag:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Lägg sedan till iOS-specifik implementering för `MicrophoneService` genom att skapa den nya mappen *Tjänster* under helloworld.iO projektet. Därefter skapar du en ny C#-källfil under den. Byt namn `MicrophoneService.cs`på filen . Kopiera och klistra in följande kodavsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Öppna `Info.plist` under helloworld.iOS-projektet i textredigeraren. Lägg till följande nyckelvärdepar under diktavsnittet:

   <key>NSMicrophoneUsageDescription</key>
   <string>Den här exempelappen kräver åtkomst till mikrofon</string>

   > [!NOTE]
   > Om du skapar för en iPhone-enhet kontrollerar du att enhetens `Bundle Identifier` etableringsprofilapp-ID matchar. Annars misslyckas bygget. Med iPhoneSimulator kan du lämna det som det är.

1. Om du bygger på en Windows-dator upprättar du en anslutning till Mac-enheten för att bygga via **Tools** > **iOS** > **Pair to Mac**. Följ instruktionsguiden från Visual Studio för att aktivera anslutningen till Mac-enheten.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Lägga till exempelkod för `helloworld.UWP` projektet

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Lägg till exempelkod för helloworld. UWP-projekt

Lägg nu till C#-koden som definierar den UWP-specifika delen av programmet.

1. I **Solution Explorer**, under helloworld. UWP-projektet, `MainPage.xaml.cs`öppna .

1. Ersätt all kod i den med följande utdrag:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Lägg sedan till en UWP-specifik implementering för `MicrophoneService` genom att skapa den nya mappen *Tjänster* under helloworld. UWP-projektet. Därefter skapar du en ny C#-källfil under den. Byt namn `MicrophoneService.cs`på filen . Kopiera och klistra in följande kodavsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Dubbelklicka `Package.appxmanifest` sedan på filen under helloworld. UWP-projektet i Visual Studio. Under **Funktioner**kontrollerar du att **mikrofonen** är markerad och sparar filen.

1. Nästa dubbelklicksfil `Package.appxmanifest` `helloworld.UWP` under projektet i Visual Studio och under **Capabilities** > **Microphone** kontrolleras och sparas filen.
   > Obs: Om du ser varning: Certifikatfilen inte finns: helloworld. UWP_TemporaryKey.pfx, kontrollera tal [till textprov](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) för mer information.

1. På menyraden väljer du**Spara alla** **för** > att spara ändringarna.

## <a name="build-and-run-the-uwp-application"></a>Skapa och kör UWP-programmet

1. Sätt helloworld. UWP som ett startprojekt. Högerklicka på helloworld. UWP-projektet och välj **Bygg** för att skapa programmet.

1. Välj **Felsökning** > **Avsökning av startsökning** (eller välj **F5**) för att starta programmet. **Helloworld fönstret** visas.

   ![Exempel på UWP-program för taligenkänning i C# - snabbstart](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Välj **Aktivera mikrofon**. När åtkomstbehörighetsbegäran visas väljer du **Ja**.

   ![Begäran om åtkomstbehörighet för mikrofon](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Välj **Starta taligenkänning**och läs en engelsk fras eller mening i enhetens mikrofon. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i fönstret.

   ![Användargränssnitt för taligenkänning](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Skapa och kör Android- och iOS-programmen

Bygga och köra Android och iOS-program i enheten eller simulatorn sker på ett liknande sätt som UWP. Kontrollera att alla SDK:er är korrekt installerade enligt avsnittet "Förutsättningar" i den här artikeln.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](../footer.md)]
