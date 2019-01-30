---
title: Kom igång med Bing-Taligenkänning för taligenkänning med hjälp av C# skrivbord biblioteket | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Utveckla grundläggande Windows-program som använder Bing-Taligenkänning för taligenkänning för att omvandla talat ljud till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 4d03ccfddab9a4aab4a1eacde02d68652bf5103a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219081"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Snabbstart: Använd Bing-taligenkänning API i C&#35; för .NET i Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Den här sidan visar hur du utvecklar ett grundläggande Windows-program som använder den tal-API: T för att omvandla talat ljud till text. Använder klientbiblioteket får strömning i realtid, vilket innebär att när klientprogrammet skickar ljud till tjänsten, den samtidigt och asynkront får tillbaka delvisa igenkänningsresultat.

Utvecklare som vill använda Speech Service från appar som körs på valfri enhet kan använda Fjärrskrivbord biblioteket C#. För att använda biblioteket, installera den [NuGet-paketet Microsoft.ProjectOxford.SpeechRecognition x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) för en 32-bitars plattform och [NuGet-paketet Microsoft.ProjectOxford.SpeechRecognition x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) för en 64-bitars plattform. API-referens för klientbiblioteket finns [Microsoft Speech C# skrivbord biblioteket](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

I följande avsnitt beskrivs hur du installerar, bygga och köra C#-exempelprogram med hjälp av C# skrivbord biblioteket.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

I följande exempel har utvecklats specifikt för Windows 8 + och .NET Framework 4.5 + med hjälp av [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Hämta exempelprogrammet

Klona exemplet från den [tal C#-skrivbord biblioteket exempel](https://github.com/microsoft/cognitive-speech-stt-windows) lagringsplats.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på det tal-API: T och få en kostnadsfri utvärderingsprenumeration-nyckel

API för taligenkänning är en del av Cognitive Services (tidigare projekt Oxford). Du kan få kostnadsfri utvärderingsprenumeration nycklar från den [Cognitive Services-prenumeration](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt API för taligenkänning, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar den primära och sekundära nyckeln. Båda nycklarna är knutna till samma kvot, så du kan använda någon av nycklarna.

> [!IMPORTANT]
> * Få en prenumerationsnyckel. Innan du använder tal-klientbiblioteken, måste du ha en [prenumerationsnyckel](https://azure.microsoft.com/try/cognitive-services/).
>
> * Använd din prenumerationsnyckel. Med den angivna C# skrivbord exempelprogrammet klistrar du in din prenumerationsnyckel i textrutan när du kör exemplet. Mer information finns i [kör exempelprogrammet](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Steg 1: Installera exempelprogrammet

1. Starta Visual Studio 2015 och välj **filen** > **öppna** > **projekt/lösning**.

2. Bläddra till mappen där du sparade de hämtade filerna för tal-API: T. Välj **tal** > **Windows**, och välj sedan exempel WP-mapp.

3. Dubbelklicka för att öppna filen Visual Studio 2015-lösning (.sln) med namnet SpeechToText-WPF-Samples.sln. Lösningen öppnas i Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Steg 2: Skapa exempelprogrammet

1. Om du vill använda *erkännande med avsikt*, måste du först registrera dig för den [Language Understanding Intelligent Service (LUIS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Använd slutpunkts-URL för LUIS-appen för att ange värdet för nyckeln `LuisEndpointUrl` i app.config-fil i mappen samples/SpeechRecognitionServiceExample. Mer information om slutpunkts-URL för LUIS-app finns i [publicera din app](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Ersätt den `&` tecknet i LUIS slutpunkts-URL med `&amp;` så att URL: en tolkas korrekt av XML-parsern.

2. Tryck på Ctrl + Skift + B eller välj **skapa** på menyn i menyfliksområdet. Välj sedan **skapa lösning**.

## <a name="step-3-run-the-sample-application"></a>Steg 3: Köra exempelprogrammet

1. När bygget har slutförts, tryck på F5 eller välj **starta** på menyn menyfliksområdet för att köra exemplet.

2. Gå till den **projekt Oxford tal till textexempel** fönster. Klistra in din prenumerationsnyckel till den **klistra in din prenumerationsnyckel för att starta** textrutan som visas. För att bevara din prenumerationsnyckel på din dator eller bärbar dator, Välj **Spara nyckel**. Om du vill ta bort prenumerationsnyckeln från systemet, Välj **ta bort nyckeln** ta bort den från din dator eller bärbar dator.

   ![Tal erkännande klistra in nyckeln](../Images/SpeechRecog_paste_key.PNG)

3. Under **tal igenkänning av källa**, väljer du något av sex tal källor, dessa kan delas in i två huvudkategorier för indata:

   * Använd datorns mikrofon eller en anslutna mikrofon för att avbilda tal.
   * Spela upp en ljudfil.

   Varje kategori har tre lägen för igenkänning av:

    * **ShortPhrase läge**: Ett uttryck upp till 15 sekunder långa. När data skickas till servern, klienten tar emot flera delresultat och ett slutresultat med flera n bästa val.
    * **LongDictation läge**: Ett uttryck upp till två minuter lång. När data skickas till servern, tar klienten emot flera delresultat och flera slutresultat baserat på var servern indikerar paus i meningen.
    * **Avsiktshantering identifiering**: Servern returnerar mer strukturerad information om tal som indata. Om du vill använda avsikt identifiering, måste du först tränar en modell med hjälp av [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Använd exemplet ljudfiler med det här exempelprogrammet. Hitta filerna i databasen som du laddade ned med det här exemplet under mappen samples/SpeechRecognitionServiceExample. Dessa exempel ljudfiler körs automatiskt om inga andra filer du väljer när du väljer **Använd wav-fil för Shortphrase läge** eller **Använd wav-fil för Longdictation läge** som ditt tal som indata. För närvarande stöds endast WAV ljudformat.

![Tal till Text-gränssnitt](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Exempel beskrivs

### <a name="recognition-events"></a>Igenkänning av händelser

* **Partiell resultat händelser**: Den här händelsen anropas varje gång Speech Service förutsäger vad du kan säger, även innan du slutför talar (om du använder `MicrophoneRecognitionClient`) eller har skickat data (om du använder `DataRecognitionClient`).
* **Felhändelser**: Anropas när tjänsten upptäcker ett fel.
* **Avsiktshantering händelser**: Namnet på ”WithIntent” klienter (endast i ShortPhrase läge) när slutgiltiga resultatet parsas till en strukturerade JSON-avsikt.
* **Resultera händelser**:
  * I `ShortPhrase` läge, den här händelsen kallas och returnerar n-best-resultat när du har slutfört talar.
  * I `LongDictation` läge, händelsehanteraren anropas flera gånger, baserat på där tjänsten identifierar paus i meningen.
  * **För var och en av de n bästa val**, returneras ett förtroende-värde och ett par olika former av den tolkade texten. Mer information finns i [utdataformat](../Concepts.md#output-format).

Händelsehanterare är redan nämnts i kod i form av kodkommentarer.

## <a name="related-topics"></a>Relaterade ämnen

* [Microsoft Speech skrivbord biblioteksreferens](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Kom igång med API för taligenkänning igenkänning av Microsoft i Java på Android](GetStartedJavaAndroid.md)
* [Kom igång med API för taligenkänning igenkänning av Microsoft i Objective-C på iOS](Get-Started-ObjectiveC-iOS.md)
* [Kom igång med API för taligenkänning igenkänning av Microsoft i JavaScript](GetStartedJSWebsockets.md)
* [Kom igång med API för taligenkänning igenkänning av Microsoft via REST](GetStartedREST.md)
