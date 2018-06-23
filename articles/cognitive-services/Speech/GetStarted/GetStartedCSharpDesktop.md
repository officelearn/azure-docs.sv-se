---
title: Kom igång med Microsoft Speech Recognition API med hjälp av fjärrskrivbord biblioteket C# | Microsoft Docs
description: Utveckla grundläggande Windows-program som använder Microsoft Speech Recognition API för att konvertera tal till text.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/27/2017
ms.author: zhouwang
ms.openlocfilehash: e59b0e25401fb5182edd52f82985ffed9052286d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352245"
---
# <a name="get-started-with-the-speech-recognition-api-in-c35-for-net-on-windows"></a>Kom igång med Speech Recognition API i C&#35; för .NET i Windows

Den här sidan visar hur du utvecklar ett grundläggande Windows-program som använder Speech Recognition API för att konvertera tal till text. Med hjälp av klientbiblioteket kan för direktuppspelning av realtid, vilket innebär att när klientprogrammet skickar ljud till tjänsten, den samtidigt och asynkront tar emot partiella resultat tillbaka.

Utvecklare som vill använda tjänsten tal från appar som körs på alla enheter kan använda Fjärrskrivbord biblioteket C#. För att använda biblioteket, installera den [NuGet-paketet Microsoft.ProjectOxford.SpeechRecognition x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) för en 32-bitars plattform och [NuGet-paketet Microsoft.ProjectOxford.SpeechRecognition x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) för en 64-bitars plattform. API-referens för klientbiblioteket finns [Microsoft tal C# skrivbord biblioteket](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

I följande avsnitt beskrivs hur du installerar, skapa och kör exempelprogrammet C# med hjälp av fjärrskrivbord biblioteket C#.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

I följande exempel har utvecklats specifikt för Windows 8 + och .NET Framework 4.5 + med hjälp av [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Hämta exempelprogrammet

Klona prov från den [tal C# skrivbord biblioteket exempel](https://github.com/microsoft/cognitive-speech-stt-windows) databasen.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på Speech Recognition API och få en kostnadsfri utvärderingsprenumeration nyckel

Dikterings-API: N är en del av kognitiva tjänster (tidigare projekt Oxford). Du kan hämta en kostnadsfri utvärderingsprenumeration nycklar från den [kognitiva abonnemang](https://azure.microsoft.com/try/cognitive-services/) sidan. När du har valt tal-API, Välj **hämta API-nyckel** att hämta nyckeln. Returnerar en primär och en sekundär nyckel. Båda nycklarna är knutna till samma kvoten, så du kan använda antingen nyckel.

> [!IMPORTANT]
> * Hämta en nyckel för prenumerationen. Innan du använder klientbibliotek tal, måste du ha en [prenumeration nyckeln](https://azure.microsoft.com/try/cognitive-services/).
>
> * Använd din prenumeration nyckel. Med angivna C# skrivbord exempelprogrammet klistrar du in din prenumeration nyckel i textrutan när du kör exemplet. Mer information finns i [kör exempelprogrammet](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Steg 1: Installera exempelprogrammet

1. Starta Visual Studio 2015 och välj **filen** > **öppna** > **projekt/lösning**.

2. Bläddra till mappen där du sparade de hämtade Speech Recognition API-filerna. Välj **tal** > **Windows**, och välj sedan exempel WP-mapp.

3. Dubbelklicka för att öppna filen för Visual Studio 2015-lösning (.sln) som heter SpeechToText-WPF-Samples.sln. Lösningen öppnas i Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Steg 2: Skapa exempelprogrammet

1. Om du vill använda *recognition med avsikt*, måste du först registrerar dig för den [språk förstå Intelligent Service (THOMAS)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Sedan använda slutpunkts-URL för appens THOMAS för att ange värdet för nyckeln `LuisEndpointUrl` i app.config-filen i mappen prover/SpeechRecognitionServiceExample. Mer information om slutpunkts-URL för THOMAS appen finns [publicera appen](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Ersätt den `&` tecken i THOMAS slutpunkts-URL med `&amp;` så att URL: en tolkas korrekt av XML-parsern.

2. Tryck på Ctrl + Skift + B eller välj **skapa** på menyn i menyfliksområdet. Välj sedan **skapa lösning**.

## <a name="step-3-run-the-sample-application"></a>Steg 3: Kör exempelprogrammet

1. När versionen är klar, tryck på F5 eller välj **starta** på menyn menyfliksområdet om du vill köra exemplet.

2. Gå till den **projekt Oxford tal till exempel Text** fönster. Klistra in nyckeln prenumerationen i den **klistra in prenumerationen nyckeln här för att starta** textrutan som visas. För att bevara din prenumeration nyckel på din dator eller en bärbar dator, Välj **Spara nyckel**. Om du vill ta bort prenumerationen nyckeln från systemet, Välj **ta bort nyckeln** ta bort den från din dator eller en bärbar dator.

   ![Tal recognition klistra in nyckeln](../Images/SpeechRecog_paste_key.PNG)

3. Under **Speech Recognition källa**, Välj en av källorna sex tal som är indelade i två huvudkategorier för indata:

   * Använda datorns mikrofon eller en bifogade mikrofon för att avbilda tal.
   * Spela upp ett ljud.

   Varje kategori har tre recognition lägen:

    * **ShortPhrase läge**: en utterance upp till 15 sekunder långt. När data skickas till servern, klienten tar emot flera ofullständiga resultat och en slutresultatet med flera n bästa val.
    * **LongDictation läge**: en utterance upp till två minuter lång tid. När data skickas till servern, klienten tar emot flera ofullständiga resultat och flera slutliga resultat baserat på där servern anger meningen pausar.
    * **Avsiktshantering identifiering**: returnerar servern ytterligare strukturerad information om tal som indata. Om du vill använda avsiktshantering identifiering, måste du först tränar en modell med hjälp av [THOMAS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Använda exemplet ljudfiler med det här exempelprogrammet. Hitta filerna i databasen som du hämtade med det här exemplet under mappen prover/SpeechRecognitionServiceExample. Dessa exempel ljudfiler körs automatiskt om inga andra filer väljs när du väljer **Använd wav-filen för Shortphrase läge** eller **Använd wav-filen för Longdictation läge** som ditt tal som indata. För närvarande stöds endast WAV ljudformatet.

![Tal till Text-gränssnitt](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Exempel förklaras

### <a name="recognition-events"></a>Recognition händelser

* **Partiell resultat händelser**: den här händelsen anropas varje gång tal Service förutsäger vad du kan säger, innan du har talat klart (om du använder `MicrophoneRecognitionClient`) eller har skickat data (om du använder `DataRecognitionClient`).
* **Felhändelser**: anropas när tjänsten upptäcker ett fel.
* **Avsiktshantering händelser**: namnet på ”WithIntent” klienter (endast i ShortPhrase läge) när slutgiltiga resultatet är parsas till en strukturerad JSON-avsikt.
* **Leda händelser**:
  * I `ShortPhrase` -läge för den här händelsen kallas och returnerar n bäst resultat när du har talat klart.
  * I `LongDictation` läge händelsehanteraren anropas flera gånger, baserat på där tjänsten identifierar meningen pausar.
  * **För varje n bästa val**, returneras ett förtroende-värde och ett par olika typer av tolkad text. Mer information finns i [utdataformat](../Concepts.md#output-format).

Händelsehanterare är redan pekas i koden i form av koden kommentarer.

## <a name="related-topics"></a>Relaterade ämnen

* [Microsoft Speech skrivbord biblioteksreferens](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Kom igång med Microsoft Speech Recognition API i Java på Android](GetStartedJavaAndroid.md)
* [Kom igång med Microsoft Speech Recognition API i Objective-C på iOS](Get-Started-ObjectiveC-iOS.md)
* [Kom igång med Microsoft Speech Recognition API i JavaScript](GetStartedJSWebsockets.md)
* [Kom igång med Microsoft Speech Recognition API via REST](GetStartedREST.md)
