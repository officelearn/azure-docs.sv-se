---
title: 'Snabb start: identifiera tal C# , (Xamarin) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar du ett plattforms oberoende C# Xamarin-program för universell Windows-plattform (UWP), Android och iOS med hjälp av COGNITIVE Services Speech SDK. Du kan skriva tal i text i real tid från enhetens eller Simulatorns mikrofon. Programmet har skapats med tal SDK NuGet-paketet och Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: ad1c6f7d17d1e04fcfa5b187a29dadd2fa61edd8
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675609"
---
# <a name="quickstart-recognize-speech-by-using-a-cross-platform-xamarin-app-that-uses-the-speech-sdk"></a>Snabb start: identifiera tal med hjälp av en Xamarin-app över plattformar som använder tal-SDK

Snabb Starter är också tillgängliga för [tal för text](quickstart-csharp-uwp.md), [text till tal](quickstart-text-to-speech-csharp-uwp.md)och [tal översättning](quickstart-translate-speech-uwp.md).

I den här artikeln utvecklar du ett plattforms oberoende C# program med hjälp av Xamarin för den universell Windows-plattform (UWP), Android och iOS som använder Azure COGNITIVE Services [Speech SDK](speech-sdk.md). Programmet utvärderar tal till text i real tid från enhetens mikrofon. Programmet har skapats med [tal SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och alla versioner av Microsoft Visual Studio 2019.

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en utan kostnad](get-started.md).
* En Windows-dator med Windows 10 är Creators Update (10,0; Build 16299) eller senare och med en fungerande mikrofon.
* En [Xamarin-installation till Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* En [Xamarin Android-installation på Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* En [Xamarin iOS-installation på Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* För att rikta Android: 
   * En Android-enhet (ARM32/64, x86; API 23: Android 6,0 Marshmallow eller högre) har [Aktiver ATS för utveckling](https://developer.android.com/studio/debug/dev-options) med en fungerande mikrofon.
* Till mål-iOS: 
    * En iOS-enhet (ARM64) eller en iOS-Simulator (x64) [som är aktive rad för utveckling](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) med en fungerande mikrofon.
* För Windows ARM64 build-support installerar du de [valfria build-verktygen och Windows 10 SDK för arm/arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Lägg till exempel kod för det vanliga HelloWorld-projektet

Det vanliga projektet HelloWorld innehåller plattforms oberoende implementeringar för ditt plattforms oberoende program. Lägg nu till XAML-koden som definierar användar gränssnittet för programmet och Lägg till C# koden bakom implementeringen.

1. I **Solution Explorer**går du till det vanliga HelloWorld-projektet och öppnar `MainPage.xaml`.

1. I designerns XAML-vy infogar du följande XAML-kodfragment i **Rutnäts** tag gen mellan `<StackLayout>` och `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. Öppna käll filen bakomliggande kod `MainPage.xaml.cs` i **Solution Explorer**. Den är grupperad under `MainPage.xaml`.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Leta upp strängen `YourSubscriptionKey` i käll filens `OnRecognitionButtonClicked`-hanterare och ersätt den med din prenumerations nyckel.

1. Leta upp strängen `YourServiceRegion` i Hanteraren för `OnRecognitionButtonClicked` och ersätt den med den [region](regions.md) som är associerad med din prenumeration. Använd till exempel `westus` för den kostnads fria prov prenumerationen.

1. Därefter måste du skapa en [Xamarin-tjänst](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)som används för att fråga mikrofon behörigheter från olika plattforms projekt, till exempel UWP, Android och iOS. Det gör du genom att lägga till en ny mapp med namnet *tjänster* i projektet HelloWorld och skapa en C# ny källfil under den. Du kan högerklicka på mappen *tjänster* och välja **Lägg till**  > **nytt objekt**  > **kod fil**. Byt namn på filen `IMicrophoneService.cs` och placera all kod från följande kodfragment i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Lägg till exempel kod för HelloWorld. Android-projekt

Lägg nu till C# den kod som definierar den Android-specifika delen av programmet.

1. I **Solution Explorer**under HelloWorld. Android-projekt, öppna `MainActivity.cs`.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Lägg sedan till en Android-speciell implementering för `MicrophoneService` genom att skapa de nya mapp *tjänsterna* under HelloWorld. Android-projekt. Därefter skapar du en ny C# källfil under den. Byt namn på filen `MicrophoneService.cs`. Kopiera och klistra in följande kod avsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Efter det öppnar du `AndroidManifest.xml` under mappen *Egenskaper* . Lägg till följande användnings behörighets inställning för mikrofonen mellan `<manifest>` och `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Lägg till exempel kod för HelloWorld. iOS-projektet

Lägg nu till C# den kod som definierar den iOS-specifika delen av programmet. Skapa även Apple-enhetsspecifika konfigurationer i projektet HelloWorld. iOS.

1. I **Solution Explorer**går du till projektet HelloWorld. iOS och öppnar `AppDelegate.cs`.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Lägg sedan till iOS-speciell implementering för `MicrophoneService` genom att skapa de nya mapp *tjänsterna* under projektet HelloWorld.io. Därefter skapar du en ny C# källfil under den. Byt namn på filen `MicrophoneService.cs`. Kopiera och klistra in följande kod avsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Öppna `Info.plist` under projektet HelloWorld. iOS i text redigeraren. Lägg till följande nyckel värde par under avsnittet Dictation:

   <key>NSMicrophoneUsageDescription</key> 
   <string>den här exempel appen kräver åtkomst till mikrofonen</string>

   > [!NOTE] 
   > Om du skapar för en iPhone-enhet ser du till att `Bundle Identifier` matchar appens ID för etablerings profil för din enhet. Annars Miss kommer versionen. Med iPhoneSimulator kan du lämna den som den är.

1. Om du bygger på en Windows-dator upprättar du en anslutning till Mac-enheten för att bygga via **verktyg**  > **iOS**  > -**paret till Mac**. Följ anvisningarna i Visual Studio för att aktivera anslutningen till Mac-enheten.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Lägg till exempel kod för HelloWorld. UWP-projekt

Nu ska du C# lägga till den kod som definierar UWP-specifik del av programmet.

1. I **Solution Explorer**under HelloWorld. UWP-projekt öppnar du `MainPage.xaml.cs`.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Lägg sedan till en UWP implementering för `MicrophoneService` genom att skapa de nya mapp *tjänsterna* under HelloWorld. UWP-projekt. Därefter skapar du en ny C# källfil under den. Byt namn på filen `MicrophoneService.cs`. Kopiera och klistra in följande kod avsnitt i filen:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Sedan dubbelklickar du på `Package.appxmanifest`-filen under HelloWorld. UWP-projekt i Visual Studio. Under **funktioner**kontrollerar du att **mikrofon** är markerat och sparar filen.

   > [!NOTE] 
   > Om du ser varningen "certifikat filen finns inte: HelloWorld. UWP_TemporaryKey. pfx, "kontrol lera [tal-till-text-](quickstart-csharp-uwp.md) exemplet för mer information.

1. I meny raden väljer du **fil**  > **Spara alla** för att spara ändringarna.

## <a name="build-and-run-the-uwp-application"></a>Skapa och köra UWP-programmet

1. Ange HelloWorld. UWP som ett start projekt. Högerklicka på HelloWorld. UWP-projekt och välj **build** för att bygga programmet. 

1. Starta programmet genom att välja **felsök**  > **Starta fel sökning** (eller Välj **F5**). Fönstret **HelloWorld** visas.

   ![Exempel program för tal igenkänning i C# UWP-snabb start](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Välj **aktivera mikrofon**. När begäran om åtkomst behörighet visas väljer du **Ja**.

   ![Åtkomst behörighets förfrågan för mikrofon](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Välj **Starta tal igenkänning**och tala en engelsk fras eller mening i enhetens mikrofon. Ditt tal överförs till tal tjänster och skrivs till text, som visas i fönstret.

   ![Användar gränssnitt för tal igenkänning](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Skapa och kör Android-och iOS-program

Att skapa och köra Android-och iOS-program i enheten eller simulatorn sker på ett liknande sätt som UWP. Se till att alla SDK: er installeras korrekt enligt kraven i avsnittet "krav" i den här artikeln.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)
