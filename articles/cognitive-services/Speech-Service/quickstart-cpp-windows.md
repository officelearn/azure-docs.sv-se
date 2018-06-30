---
title: Snabbstart för tal-SDK för C++ och Windows | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Hämta information och exempel på kod för att snabbt komma igång med tal-SDK med Windows och C++ i kognitiva Services.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 4dea1209548fb3bb4f846cdf7eaf82751f938bea
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108852"
---
# <a name="quickstart-for-c-and-windows"></a>Snabbstart för C++ och Windows

Den aktuella versionen av kognitiva Services tal SDK är `0.4.0`.

Vi beskriver hur du skapar ett konsolprogram med C++ för Windows-skrivbordet som använder SDK för tal.
Programmet baseras på den [Microsoft kognitiva Services SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) och Microsoft Visual Studio 2017.

> [!NOTE]
> Om du letar efter en Snabbstart för C++- och Linux, gå [här](quickstart-cpp-linux.md).<br>
> Om du letar efter en Snabbstart för C# och Windows, gå [här](quickstart-csharp-windows.md).

> [!NOTE]
> Denna Snabbstart kräver en dator med en fungerande mikrofon.<br>
> Ett exempel som identifierar tal från en viss ljud indatafilen finns i [exempel](speech-to-text-sample.md#speech-recognition-from-a-file).

> [!NOTE]
> Kontrollera att installationen av Visual Studio innehåller den **skrivbord utveckling med C++** arbetsbelastning.
> Om du inte vet du följa dessa steg för att kontrollera och åtgärda: I Visual Studio 2017, Välj **verktyg** \> **hämta verktyg och funktioner** och bekräfta User Account Control fråga genom att välja **Ja**.
> I den **arbetsbelastningar** om **skrivbord utveckling med C++** inte har en uppsättning kryssrutan bredvid den, ange den och klicka på **ändra** spara ändringarna.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>Skapa en tom konsolapprojekt

Skapa en ny Visual C++ Windows Desktop Windows-konsolprogram i Visual Studio 2017 med namnet ”CppHelloSpeech”:

![Skapa Visual C++ Windows stationära Windows-konsolprogram](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

Om du kör på en 64-bitars Windows-installation, alternativt byta din build-plattformen `x64`:

![Växeln x64 av build-plattformen](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Installera och referera till tal SDK NuGet-paketet

> [!NOTE]
> Kontrollera NuGet package manager har aktiverats för din installation av Visual Studio 2017.
> Välj i Visual Studio 2017 **verktyg** \> **hämta verktyg och funktioner** och bekräfta User Account Control fråga genom att välja **Ja**. Välj sedan den **enskilda komponenter** fliken och Sök efter **NuGet-Pakethanteraren** under **Code verktyg**.
> Om kryssrutan till vänster inte har angetts måste du ange den och klicka på **ändra** spara ändringarna.
>
> ![Aktivera NuGet-Pakethanteraren i Visual Studio ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

Högerklicka på lösningen i Solution Explorer och klicka på **hantera NuGet-paket för lösningen**.

![Högerklicka på Hantera NuGet-paket för lösningen](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

I det övre högra hörnet i den **Paketkällan** , Välj ”Nuget.org”.
Från den **Bläddra** fliken Sök efter ”Microsoft.CognitiveServices.Speech” paketet, markerar du den och kontrollera den **projekt** och **CppHelloSpeech** rutorna till höger och Välj **installera** att installera den i projektet CppHelloSpeech.

![Installera Microsoft.CognitiveServices.Speech NuGet-paketet](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

På skärmen licens som gör att acceptera licensvillkoren:

![Acceptera licensvillkoren](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>Lägg till exempelkoden

Ersätt Koden standard starter med följande:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> Ersätt nyckeln prenumerationen med det som du har köpt. <br>
> Ersätt den [region](regions.md) med den som är associerade med prenumerationen, till exempel ersätta med `westus` för den kostnadsfria utvärderingsversionen.

![Lägg till din prenumeration nyckel](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

Koden ska kompileras utan fel nu:

![Lyckad build](media/sdk/speechsdk-16-vs-cpp-build.png)

Starta programmet under felsökaren med knappen Starta eller med hjälp av kortkommandot F5:

![Starta appen i Felsökning](media/sdk/speechsdk-17-vs-cpp-f5.png)

Ett konsolfönster ska visas, där du uppmanas att säga något (på engelska).
Resultatet av redovisning visas på skärmen.

![Konsolens utdata efter lyckad igenkänning](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>Hämta exemplet

Senaste uppsättning exempel, finns det [kognitiva Services tal SDK exempel GitHub-lagringsplatsen](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Nästa steg

* Besök den [-exempel](samples.md) ytterligare exempel.
