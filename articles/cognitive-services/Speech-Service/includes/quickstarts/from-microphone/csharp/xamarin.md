---
title: 'Snabb start: identifiera tal från en mikrofon C# , (Xamarin) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar du ett plattforms oberoende C# Xamarin-program för universell Windows-plattform (UWP), Android och iOS med hjälp av COGNITIVE Services Speech SDK. Du kan skriva tal i text i real tid från enhetens eller Simulatorns mikrofon. Programmet har skapats med tal SDK NuGet-paketet och Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: ed37e4f5b84431355b63b3d59b0e49b78eaeed86
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445490"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=xamarin)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=xamarin)
> * Kontrol lera att du har åtkomst till en mikrofon för ljud inspelning

Om du redan har gjort detta är det bra. Vi fortsätter.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Lägg till exempel kod för det vanliga HelloWorld-projektet

Det vanliga projektet HelloWorld innehåller plattforms oberoende implementeringar för ditt plattforms oberoende program. Lägg nu till XAML-koden som definierar användar gränssnittet för programmet och Lägg till C# koden bakom implementeringen.

1. I **Solution Explorer**går du till det vanliga HelloWorld-projektet och öppnar `MainPage.xaml`.

1. I designerns XAML-vy infogar du följande XAML-kodfragment i **Rutnäts** tag gen mellan `<StackLayout>` och `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. Öppna käll filen för bakomliggande kod `MainPage.xaml.cs`i **Solution Explorer**. Den är grupperad under `MainPage.xaml`.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Leta upp strängen `YourSubscriptionKey`i käll filens `OnRecognitionButtonClicked` hanterare och ersätt den med din prenumerations nyckel.


1. Leta upp strängen `YourServiceregion`i `OnRecognitionButtonClicked` hanteraren och ersätt den med **regions-ID: n** från den [region](https://aka.ms/speech/sdkregion) som är associerad med din prenumeration. (Använd till exempel `westus` för den kostnads fria prov prenumerationen.)

1. Därefter måste du skapa en [Xamarin-tjänst](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)som används för att fråga mikrofon behörigheter från olika plattforms projekt, till exempel UWP, Android och iOS. Det gör du genom att lägga till en ny mapp med namnet *tjänster* i projektet HelloWorld och skapa en C# ny källfil under den. Du kan högerklicka på mappen *tjänster* och välja **Lägg till** > **nytt objekt** > **kod fil**. Byt namn på filen `IMicrophoneService.cs`och placera all kod från följande kodfragment i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Lägg till exempel kod för `helloworld.Android`-projektet

Lägg nu till C# den kod som definierar den Android-specifika delen av programmet.

1. I **Solution Explorer**under HelloWorld. Android-projekt, öppna `MainActivity.cs`.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Lägg sedan till en Android-speciell implementering för `MicrophoneService` genom att skapa de nya mapp *tjänsterna* under HelloWorld. Android-projekt. Därefter skapar du en ny C# källfil under den. Byt namn på filen `MicrophoneService.cs`. Kopiera och klistra in följande kod avsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Efter det öppnar du `AndroidManifest.xml` under mappen *Egenskaper* . Lägg till följande användnings behörighets inställning för mikrofonen mellan `<manifest>` och `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Lägg till exempel kod för `helloworld.iOS`-projektet

Lägg nu till C# den kod som definierar den iOS-specifika delen av programmet. Skapa även Apple-enhetsspecifika konfigurationer i projektet HelloWorld. iOS.

1. I **Solution Explorer**går du till projektet HelloWorld. iOS och öppnar `AppDelegate.cs`.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Lägg sedan till iOS-speciell implementering för `MicrophoneService` genom att skapa de nya mapp *tjänsterna* under projektet HelloWorld.io. Därefter skapar du en ny C# källfil under den. Byt namn på filen `MicrophoneService.cs`. Kopiera och klistra in följande kod avsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Öppna `Info.plist` under projektet HelloWorld. iOS i text redigeraren. Lägg till följande nyckel värde par under avsnittet Dictation:

   <key>NSMicrophoneUsageDescription</key>
   <string>den här exempel appen kräver åtkomst till mikrofonen</string>

   > [!NOTE]
   > Om du skapar för en iPhone-enhet ser du till att `Bundle Identifier` matchar appens ID för etablerings profil för din enhet. Annars Miss kommer versionen. Med iPhoneSimulator kan du lämna den som den är.

1. Om du bygger på en Windows-dator upprättar du en anslutning till Mac-enheten för att bygga via **verktyg** > **iOS** > - **paret till Mac**. Följ anvisningarna i Visual Studio för att aktivera anslutningen till Mac-enheten.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Lägg till exempel kod för `helloworld.UWP`-projektet

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Lägg till exempel kod för HelloWorld. UWP-projekt

Nu ska du C# lägga till den kod som definierar UWP-specifik del av programmet.

1. I **Solution Explorer**under HelloWorld. UWP-projekt öppnar du `MainPage.xaml.cs`.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Lägg sedan till en UWP implementering för `MicrophoneService` genom att skapa de nya mapp *tjänsterna* under HelloWorld. UWP-projekt. Därefter skapar du en ny C# källfil under den. Byt namn på filen `MicrophoneService.cs`. Kopiera och klistra in följande kod avsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Sedan dubbelklickar du på `Package.appxmanifest`-filen under HelloWorld. UWP-projekt i Visual Studio. Under **funktioner**kontrollerar du att **mikrofon** är markerat och sparar filen.

1. Nästa steg: dubbelklicka på `Package.appxmanifest` fil under `helloworld.UWP` projektet i Visual Studio och under **funktioner** > **mikrofon** är markerad och spara filen.
   > Obs: om du ser varning: certifikat filen finns inte: HelloWorld. UWP_TemporaryKey. pfx, kontrol lera [tal till text](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) -exemplet för mer information.

1. I meny raden väljer du **fil** > **Spara alla** för att spara ändringarna.

## <a name="build-and-run-the-uwp-application"></a>Skapa och köra UWP-programmet

1. Ange HelloWorld. UWP som ett start projekt. Högerklicka på HelloWorld. UWP-projekt och välj **build** för att bygga programmet.

1. Starta programmet genom att välja **felsök** > **Starta fel sökning** (eller Välj **F5**). Fönstret **HelloWorld** visas.

   ![Exempel program för tal igenkänning i C# UWP-snabb start](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Välj **aktivera mikrofon**. När begäran om åtkomst behörighet visas väljer du **Ja**.

   ![Åtkomst behörighets förfrågan för mikrofon](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Välj **Starta tal igenkänning**och tala en engelsk fras eller mening i enhetens mikrofon. Ditt tal överförs till Speech-tjänsten och transkriberas till text som visas i fönstret.

   ![Användar gränssnitt för tal igenkänning](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Skapa och kör Android-och iOS-program

Att skapa och köra Android-och iOS-program i enheten eller simulatorn sker på ett liknande sätt som UWP. Se till att alla SDK: er installeras korrekt enligt kraven i avsnittet "krav" i den här artikeln.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
