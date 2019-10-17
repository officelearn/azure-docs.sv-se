---
title: 'Snabb start: identifiera tal C# , (Xamarin) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: I den här artikeln skapar du ett plattforms oberoende C# Xamarin-program för Windows UWP, Android och iOS med hjälp av COGNITIVE Services Speech SDK. Du kan skriva tal i text i real tid från enhetens eller Simulatorns mikrofon. Programmet har skapats med tal SDK NuGet-paketet och Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: c9467bac8b5998252c021faca4eb4177c42a1736
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387390"
---
# <a name="quickstart-recognize-speech-using-cross-platform-xamarin-app-by-using-the-speech-sdk"></a>Snabb start: identifiera tal med Xamarin-appen över plattformar med hjälp av tal-SDK

Snabb Starter är också tillgängliga för [tal till text](quickstart-csharp-uwp.md), [text till tal](quickstart-text-to-speech-csharp-uwp.md) -och [tal översättning](quickstart-translate-speech-uwp.md).

I den här artikeln utvecklar du ett plattforms oberoende C# program med Xamarin för universell Windows-plattform (UWP), Android och iOS med hjälp av [talet SDK](speech-sdk.md). Programmet utvärderar tal till text i real tid från enhetens mikrofon. Programmet har skapats med [tal SDK NuGet-paketet](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2019 (alla versioner).

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en utan kostnad](get-started.md).
* En Windows-dator med Windows 10 är Creators Update (10,0; Build 16299) eller senare och med en fungerande mikrofon.
* [Xamarin-installation till Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* [Xamarin Android-installation i Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* [Xamarin iOS-installation på Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* För att rikta Android: 
   * En Android-enhet (ARM32/64, x86; API 23: Android 6,0 Marshmallow eller högre) har [Aktiver ATS för utveckling](https://developer.android.com/studio/debug/dev-options) med en fungerande mikrofon.
* Rikta in dig på iOS: en iOS-enhet (ARM64) eller en iOS-Simulator (x64) [som är aktive rad för utveckling](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) med en fungerande mikrofon.
* För Windows ARM64 build-support installerar du de [valfria build-verktygen och Windows 10 SDK för arm/arm64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Lägg till exempel kod för det vanliga `helloworld`-projektet

Det vanliga `helloworld`-projektet innehåller plattforms oberoende implementeringar för ditt plattforms oberoende program.
Lägg nu till XAML-koden som definierar användar gränssnittet för programmet och Lägg till C# koden bakom implementeringen.

1. I **Solution Explorer**, under det vanliga `helloworld`-projektet öppet `MainPage.xaml`.

1. I designerns XAML-vy infogar du följande XAML-kodfragment i **Rutnäts** tag gen (mellan `<StackLayout>` och `</StackLayout>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. Öppna käll filen bakomliggande kod `MainPage.xaml.cs` i **Solution Explorer**. (Den grupperas under `MainPage.xaml`.)

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Leta upp strängen `YourSubscriptionKey` i käll filens `OnRecognitionButtonClicked`-hanterare och ersätt den med din prenumerations nyckel.

1. Leta upp strängen `YourServiceRegion` i Hanteraren för `OnRecognitionButtonClicked` och ersätt den med den [region](regions.md) som är associerad med din prenumeration. (Använd exempelvis `westus` för den kostnads fria prov prenumerationen.)

1. Därefter måste du skapa en [Xamarin-tjänst](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)som används för att fråga mikrofon behörigheter från olika plattforms projekt (UWP, Android och iOS). Om du vill göra det lägger du till en ny mapp `Services` under projektet @no__t- C# 1 och skapar en ny källfil under den (högerklicka på `Services`-mapp och **Lägg till** > **nytt objekt** > **kod fil**) och Byt namn på den till `IMicrophoneService.cs` och placera all kod från följande kodfragment i filen:

[!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Lägg till exempel kod för projektet `helloworld.Android`

Lägg nu till C# den kod som definierar den Android-specifika delen av programmet.

1. I **Solution Explorer**, under `helloworld.Android`-projektet öppet `MainActivity.cs`.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Lägg sedan till en Android-speciell implementering för `MicrophoneService` genom att skapa en ny mapp `Services` under projektet `helloworld.Android`. När du har skapat C# den nya käll filen under den och byter namn på den till `MicrophoneService.cs` och kopiera klistra in följande kodfragment i filen.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. När du har öppnat `AndroidManifest.xml` under mappen `Properties` och lägger till följande användnings behörighets inställning för mikrofonen mellan `<manifest>` och `</manifest>`.
```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Lägg till exempel kod för projektet `helloworld.iOS`

Nu kan du C# lägga till den kod som definierar den iOS-specifika delen av programmet och även skapa Apple Device specifika konfigurationer i projektet HelloWorld. iOS.

1. I **Solution Explorer**, under `helloworld.iOS`-projektet öppet `AppDelegate.cs`.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Lägg sedan till den iOS-speciella implementeringen för `MicrophoneService` genom att skapa en ny mapp `Services` under projektet `helloworld.iOS`. När du har skapat C# den nya käll filen under den och byter namn på den till `MicrophoneService.cs` och kopiera klistra in följande kodfragment i filen.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Öppna info. plist under `helloworld.iOS`-projektet till text redigeraren och Lägg till följande nyckel värde par under avsnittet dict <key>NSMicrophoneUsageDescription</key>
   <string>den här exempel appen kräver åtkomst till mikrofonen</string>
   > Obs! Om du riktar in dig på att bygga för iPhone-enheten bör du se till att `Bundle Identifier` matchar enhetens etablerings profil app-ID. det går inte att bygga. Med iPhoneSimulator kan du lämna den som den är.

1. Om du bygger på Windows-dator måste du upprätta en anslutning till Mac-enheten för att bygga via **Tools** > **iOS** > -**paret till Mac**. Följ anvisningarna i Visual Studio för att aktivera anslutning till Mac-enheten.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Lägg till exempel kod för projektet `helloworld.UWP`

Lägg nu till C# den kod som definierar den UWP specifika delen av programmet.

1. I **Solution Explorer**, under `helloworld.UWP`-projektet öppet `MainPage.xaml.cs`.

1. Ersätt all kod i den med följande kodfragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Lägg sedan till UWP-speciell implementering för `MicrophoneService` genom att skapa en ny mapp `Services` under projektet `helloworld.UWP`. När du har skapat C# den nya käll filen under den och byter namn på den till `MicrophoneService.cs` och kopiera klistra in följande kodfragment i filen.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Nästa gång du dubbelklickar på `Package.appxmanifest`-fil under `helloworld.UWP`-projektet i Visual Studio och under **funktioner** > **mikrofon** är markerat och sparar filen.
   > Obs: om du ser varning: certifikat filen finns inte: HelloWorld. UWP_TemporaryKey. pfx, kontrol lera [tal till text](quickstart-csharp-uwp.md) -exemplet för mer information.

1. I meny raden väljer du **arkiv** > **Spara alla** för att spara ändringarna.

## <a name="build-and-run-the-uwp-application"></a>Skapa och köra UWP-programmet

1. Ange `helloworld.UWP` som start projekt och högerklicka med musen på `helloworld.UWP`-projekt och välj **skapa** för att skapa programmet. 

1. Starta programmet genom att välja **felsök** > **Starta fel sökning** (eller tryck på **F5**). Fönstret **HelloWorld** visas.

   ![Exempel program för tal igenkänning i C# UWP-snabb start](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Välj **aktivera mikrofon**och när åtkomst behörighets förfrågan öppnas, Välj **Ja**.

   ![Åtkomst behörighets förfrågan för mikrofon](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Välj **Starta tal igenkänning**och tala en engelsk fras eller mening i enhetens mikrofon. Ditt tal överförs till tal tjänsterna och skrivs till text, som visas i fönstret.

   ![Användar gränssnitt för tal igenkänning](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Skapa och kör Android-och iOS-program

Att skapa och köra Android-och iOS-program i enheten eller simulatorn sker på liknande sätt än med UWP. Viktigt är att se till att alla SDK: er installeras korrekt i `Prerequisites`-avsnittet i det här dokumentet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)
