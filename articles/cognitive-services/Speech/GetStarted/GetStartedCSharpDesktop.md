---
title: Kom igång med Taligenkänning i Bing igenkännings-API med hjälp C# av Skriv bords biblioteket | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 'Utveckla grundläggande Windows-program som använder Taligenkänning i Bing igenkännings-API: et för att konvertera talade ljud till text.'
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3160ccc1c8741d87fcee94a6face48551a79052d
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966908"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Snabbstart: Använd Taligenkänning i Bing igenkännings-API i&#35; C för .net i Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Den här sidan visar hur du utvecklar ett grundläggande Windows-program som använder API: et för tal igenkänning för att konvertera talade ljud till text. Med hjälp av klient biblioteket kan du strömma i real tid, vilket innebär att när klient programmet skickar ljud till tjänsten samtidigt och tar emot delvis igenkännings resultat asynkront.

Utvecklare som vill använda tal tjänsten från appar som körs på en enhet kan använda C# Skriv bords biblioteket. Om du vill använda biblioteket installerar du [NuGet-paketet Microsoft. ProjectOxford. SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) för en 32-bitars plattform och [NuGet-paketet Microsoft. ProjectOxford. SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) för en 64-bitars plattform. Information om API-referens för klient bibliotek finns i [Microsoft Speech C# Desktop Library](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

I följande avsnitt beskrivs hur du installerar, skapar och kör C# exempel programmet med hjälp av C# Skriv bords biblioteket.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Följande exempel har utvecklats för Windows 8 + och .NET Framework 4.5 + med hjälp av [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Hämta exempel programmet

Klona exemplet från exempel lagrings platsen för [tal C# Skriv bords bibliotek](https://github.com/microsoft/cognitive-speech-stt-windows) .

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Prenumerera på API för tal igenkänning och få en prenumerations nyckel för kostnads fri utvärdering

Speech API är en del av Cognitive Services (tidigare Project-Oxford). Du kan få kostnads fria utvärderings prenumerations nycklar från sidan [Cognitive Services prenumeration](https://azure.microsoft.com/try/cognitive-services/) . När du har valt Speech API väljer du **Hämta API-nyckel** för att hämta nyckeln. Den returnerar en primär och sekundär nyckel. Båda nycklarna är knutna till samma kvot, så du kan använda båda nycklarna.

> [!IMPORTANT]
> * Hämta en prenumerations nyckel. Innan du använder klient biblioteken för tal måste du ha en [prenumerations nyckel](https://azure.microsoft.com/try/cognitive-services/).
>
> * Använd din prenumerations nyckel. Med det tillhandahållna C# Skriv bords exempel programmet klistrar du in prenumerations nyckeln i text rutan när du kör exemplet. Mer information finns i [Kör exempel programmet](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Steg 1: Installera exempel programmet

1. Starta Visual Studio 2015 och välj **fil** > **Öppna** > **projekt/lösning**.

2. Bläddra till mappen där du sparade de hämtade API-filerna för tal igenkänning. Välj **tal** > **fönster**och välj sedan mappen Sample-wp.

3. Öppna Visual Studio 2015-lösningen (. SLN) med namnet SpeechToText-WPF-Samples. SLN genom att dubbelklicka på den. Lösningen öppnas i Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Steg 2: Bygg exempel programmet

1. Om du vill använda *igenkänning med avsikten*måste du först registrera dig för [language Understanding intelligent service (Luis)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Använd sedan slut punkts-URL: en för Luis-appen för att ange `LuisEndpointUrl` värdet för nyckeln i filen app. config i mappen Samples/SpeechRecognitionServiceExample. Mer information om slut punkts-URL: en för LUIS-appen finns i [publicera din app](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Ersätt tecknen i slut punkts-URL: `&amp;` en för Luis med för att säkerställa att URL-adressen tolkas korrekt av XML-parsern. `&`

2. Tryck på CTRL + SKIFT + B eller Välj **build** på menyn i menyfliksområdet. Välj sedan **build-lösning**.

## <a name="step-3-run-the-sample-application"></a>Steg 3: Köra exempelprogrammet

1. När bygget är klart trycker du på F5 eller väljer **Start** på menyn i menyfliksområdet för att köra exemplet.

2. Gå till **exempel fönstret Project Oxford Speech to text** . Klistra in din prenumerations nyckel i text rutan **Klistra in prenumerations nyckeln här för att starta** text rutan som visas. Om du vill behålla prenumerations nyckeln på din dator eller bärbara dator väljer du **Spara nyckel**. Om du vill ta bort prenumerations nyckeln från systemet väljer du **ta bort nyckel** för att ta bort den från din dator eller bärbara dator.

   ![Klistra in nyckel för tal igenkänning](../Images/SpeechRecog_paste_key.PNG)

3. Under **tal igenkännings källa**väljer du en av de sex tal källorna, som finns i två huvudsakliga ingångs kategorier:

   * Använd datorns mikrofon eller en ansluten mikrofon för att avbilda tal.
   * Spela upp en ljudfil.

   Varje kategori har tre igenkännings lägen:

    * **ShortPhrase-läge**: En uttryck upp till 15 sekunder lång. När data skickas till servern får klienten flera delar av resultaten och ett slut resultat med flera n-bästa alternativ.
    * **LongDictation-läge**: En uttryck upp till två minuter lång. När data skickas till servern får klienten flera partiella resultat och flera slut resultat baserat på var servern anger att meningen ska pausas.
    * **Identifiering av avsikter**: Servern returnerar ytterligare strukturerad information om tal indata. Om du vill använda identifiering av identifierings metoder måste du först träna en modell med hjälp av [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Använd exempel på ljudfiler med det här exempel programmet. Hitta filerna i lagrings platsen som du laddade ned med det här exemplet under mappen Samples/SpeechRecognitionServiceExample. De här exempelfilerna körs automatiskt om inga andra filer väljs när du väljer **Använd WAV-fil för Shortphrase läge** eller **Använd WAV-fil för Longdictation-läge** som indata för tal. För närvarande stöds endast formatet WAV-ljud.

![Tal till text gränssnitt](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Exempel på förklaring

### <a name="recognition-events"></a>Igenkännings händelser

* **Partiella resultat händelser**: Den här händelsen anropas varje gång tal tjänsten förutsäger vad du kan säga, till och med innan du har slutfört tal ( `MicrophoneRecognitionClient`om du använder) eller Slutför sändningen av `DataRecognitionClient`data (om du använder).
* **Fel händelser**: Anropas när tjänsten identifierar ett fel.
* **Avsikts händelser**: Anropas på "WithIntent"-klienter (endast i ShortPhrase-läge) efter det slutliga igenkännings resultatet parsas till en strukturerad JSON-avsikt.
* **Resultat händelser**:
  * I `ShortPhrase` läget anropas den här händelsen och returnerar de n bästa resultaten när du har slutfört inläsningen.
  * I `LongDictation` läget anropas händelse hanteraren flera gånger, baserat på var tjänsten identifierar mening pausar.
  * **För var och en av de n bästa alternativen**returneras ett konfidens värde och några olika former av den tolkade texten. Mer information finns i [utdataformat](../Concepts.md#output-format).

Händelse hanterare har redan pekat i koden i form av kod kommentarer.

## <a name="related-topics"></a>Relaterade ämnen

* [Referens för Microsoft Speech Desktop Library](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Kom igång med API: t Microsoft Speech igenkänning i Java på Android](GetStartedJavaAndroid.md)
* [Kom igång med API: t Microsoft Speech igenkänning i mål-C på iOS](Get-Started-ObjectiveC-iOS.md)
* [Kom igång med API: t Microsoft Speech igenkänning i Java Script](GetStartedJSWebsockets.md)
* [Kom igång med API: t Microsoft Speech igenkänning via REST](GetStartedREST.md)
