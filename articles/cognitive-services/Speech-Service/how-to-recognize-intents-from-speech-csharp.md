---
title: 'Självstudier: Identifiera avsikter från tal med hjälp av Speech SDK för C#'
titleSuffix: Azure Cognitive Services
description: I den här självstudien lär du dig att identifiera avsikter från tal med hjälp av Speech SDK för C#.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 9a00dfd1186d19ce9432db8e636bffa40eb977af
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280544"
---
# <a name="tutorial-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Självstudier: Identifiera avsikter från tal med hjälp av Speech SDK för C#

[!INCLUDE [Article selector](../../../includes/cognitive-services-speech-service-how-to-recognize-intents-from-speech-selector.md)]

Cognitive Services [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) kan integreras med [Language Understanding-tjänsten (LUIS)](https://www.luis.ai/home) för att tillhandahålla **avsiktsigenkänning**. En avsikt är något som användaren vill göra: boka en flygning, titta på vädret eller ringa ett samtal. Användaren kan använda de termer som känns naturliga. Med maskininlärning mappar LUIS användarbegäranden till de avsikter som du har definierat.

> [!NOTE]
> Ett LUIS-programmet definierar avsikter och entiteter som du vill identifiera. Det är separat från det C#-program som använder Speech-tjänsten. I den här artikeln betyder ”app” LUIS-appen medan ”program” innebär C#-koden.

I den här självstudien använder du Speech SDK för att utveckla ett C#-konsolprogram som härleder avsikter från användaryttranden via enhetens mikrofon. Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Visual Studio-projekt som refererar till Speech SDK NuGet-paketet
> * Skapa en talkonfiguration och få en avsiktsigenkännare
> * Hämta modellen för LUIS-appen och lägga till de avsikter som du behöver
> * Ange språk för taligenkänning
> * Identifiera tal från en fil
> * Använd asynkron, händelsedriven kontinuerlig igenkänning

## <a name="prerequisites"></a>Förutsättningar

Se till att du har följande innan du börjar den här självstudiekursen.

* Ett LUIS-konto. Du kan skaffa ett kostnadsfritt via [LUIS-portalen](https://www.luis.ai/home).
* Visual Studio 2017 (vilken version som helst).

## <a name="luis-and-speech"></a>LUIS och tal

LUIS integreras med Taltjänster ska kunna identifiera avsikter från tal. Du behöver inte en Speech Services-prenumeration, precis LUIS.

LUIS använder två typer av nycklar:

|Nyckeltyp|Syfte|
|--------|-------|
|redigering|gör att du kan skapa och ändra LUIS-appar programmatiskt|
|slutpunkt |auktoriserar åtkomst till en viss LUIS-app|

Slutpunktsnyckeln är den LUIS-nyckel som behövs för den här självstudien. Den här kursen använder LUIS-exempelappen Home Automation, som du kan skapa genom att följa [Använda en färdig hemautomatiseringsapp](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app). Om du har skapat en LUIS-app på egen hand kan du använda den i stället.

När du skapar en LUIS-app skapas automatiskt en startnyckel så att du kan testa appen med hjälp av textfrågor. Den här nyckeln Aktivera inte Speech Services-integrering och fungerar inte med den här självstudien. Du måste skapa en LUIS-resurs på Azure-instrumentpanelen och tilldela den till LUIS-appen. Du kan använda den kostnadsfria prenumerationsnivån för den här självstudiekursen.

När du har skapat LUIS-resursen på Azure-instrumentpanelen loggar du in på [LUIS-portalen](https://www.luis.ai/home), väljer ditt program på sidan Mina appar och växlar till appens hanteringssida. Klicka slutligen på **Nycklar och slutpunkter** i sidopanelen.

![LUIS-portalens inställningar för nycklar och slutpunkter](media/sdk/luis-keys-endpoints-page.png)

På sidan med inställningar för nycklar och slutpunkter:

1. Rulla ned till avsnittet Resurser och nycklar och klicka på **Tilldela resurs**.
1. I dialogrutan **Assign a key to your app** (tilldela en nyckel till appen) väljer du följande:

    * Välj Microsoft som klientorganisation.
    * Under Prenumerationsnamn väljer du den Azure-prenumeration som innehåller den LUIS-resurs du vill använda.
    * Under Nyckel väljer du den LUIS-resurs som du vill använda med appen.

Om en stund visas den nya prenumerationen i tabellen längst ned på sidan. Klicka på ikonen intill en nyckel för att kopiera den till Urklipp. (Du kan använda valfri nyckel.)

![Prenumerationsnycklar för LUIS-app](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Skapa ett talprojekt i Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Lägga till koden

Öppna filen `Program.cs` i Visual Studio-projektet och ersätt blocket med `using`-instruktioner i början av filen med följande deklarationer.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

I den angivna `Main()`-metoden lägger du till följande kod.

```csharp
RecognizeIntentAsync().Wait();
Console.WriteLine("Please press Enter to continue.");
Console.ReadLine();
```

Skapa en tom asynkron `RecognizeIntentAsync()`-metod enligt det som visas här.

```csharp
static async Task RecognizeIntentAsync()
{
}
```

Lägg till den här koden i brödtexten i den här nya metoden.

[!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

Ersätt platshållarna i den här metoden med din LUIS-prenumerationsnyckel, region och app-ID på följande sätt.

|Platshållare|Ersätt med|
|-----------|------------|
|`YourLanguageUnderstandingSubscriptionKey`|Din LUIS-slutpunktsnyckel. Som vi nämnde tidigare måste det här vara en nyckel som hämtats från Azure-instrumentpanelen, inte en ”startnyckel”. Du hittar den på appens sida Nycklar och slutpunkter (under Hantera) i [LUIS-portalen](https://www.luis.ai/home).|
|`YourLanguageUnderstandingServiceRegion`|Den korta identifieraren för den region som din LUIS-prenumeration är i, till exempel `westus` för USA, västra. Se [regioner](regions.md).|
|`YourLanguageUnderstandingAppId`|LUIS-app-ID. Du hittar det på appens sida Inställningar i [LUIS-portalen](https://www.luis.ai/home).|

När dessa ändringar har gjorts kan du skapa (Ctrl-Skift-B) och köra (F5) självstudieprogrammet. När du uppmanas försöker du säga ”stäng av lamporna” i datorns mikrofon. Resultatet visas i konsolfönstret.

Följande avsnitt innehåller en beskrivning av koden.


## <a name="create-an-intent-recognizer"></a>Skapa en avsiktsigenkännare

Det första steget i att identifiera avsikter är att skapa en talkonfiguration från din LUIS-slutpunktsnyckel och region. Talkonfigurationer kan användas för att skapa igenkännare för de olika funktionerna i Speech SDK. Talkonfigurationen har flera sätt att ange den prenumeration som du vill använda. Här använder vi `FromSubscription`, som tar prenumerationsnyckeln och regionen.

> [!NOTE]
> Använd nyckeln och region av prenumerationen THOMAS, inte av en Speech Services-prenumeration.

Nästa steg är att skapa en avsiktsigenkännare med hjälp av `new IntentRecognizer(config)`. Eftersom konfigurationen redan vet vilken prenumeration du ska använda behöver du inte ange prenumerationsnyckeln och slutpunkten igen när du skapar igenkännaren.

## <a name="import-a-luis-model-and-add-intents"></a>Importera en LUIS-modell och lägga till avsikter

Nu importerar du modellen från LUIS-appen med hjälp av `LanguageUnderstandingModel.FromAppId()` och lägger till de LUIS-avsikter som du vill känna igen igenkännarens `AddIntent()`-metod. De här två stegen förbättrar taligenkänningens noggrannhet genom att ange ord som det är troligt att användaren använder i sina begäranden. Du behöver inte lägga till alla appens avsikter om du inte behöver identifiera dem alla i ditt program.

Att lägga till avsikter kräver tre argument: LUIS-modell (som har skapats och heter `model`), avsiktlig namn och en avsiktlig-ID. Skillnaden mellan ID och namnet är som följer.

|`AddIntent()` Argumentet|Syfte|
|--------|-------|
|intentName |Namnet på avsikten enligt vad som definierats i LUIS-appen. Måste matcha LUIS-avsiktsnamnet exakt.|
|intentID    |Ett ID som tilldelas till en igenkänd avsikt av Speech SDK. Kan vara vad som helst; det behöver inte motsvara avsiktsnamnet enligt vad som definierats i LUIS-appen. Om flera avsikter till exempel hanteras av samma kod kan du använda samma ID för dem.|

Start Automation LUIS-app har två avsikter: en för att aktivera en enhet och en annan för att inaktivera en enhet. Raderna nedan lägger till dessa avsikter till igenkännaren. Ersätt de tre `AddIntent`-raderna i metoden `RecognizeIntentAsync()` med den här koden.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

I stället för att lägga till enskilda avsikter, du kan också använda den `AddAllIntents` metod för att lägga till alla avsikter i en modell till identifieraren.

## <a name="start-recognition"></a>Starta igenkänning

När igenkännaren har skapats och avsikterna lagts till kan igenkänningen börja. Speech SDK stöder både engångsigenkänning och kontinuerlig igenkänning.

|Igenkänningsläge|Metoder att anropa|Resultat|
|----------------|-----------------|---------|
|Engångsigenkänning|`RecognizeOnceAsync()`|Returnerar den igenkända avsikten, om sådan finns, efter ett yttrande.|
|Kontinuerlig igenkänning|`StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()`|Identifierar flera yttranden. Genererar händelser (t.ex. `IntermediateResultReceived`) när resultat är tillgängliga.|

Självstudieprogrammet använder engångsigenkänning och anropar därför `RecognizeOnceAsync()` för att påbörja igenkänning. Resultatet är ett `IntentRecognitionResult`-objekt som innehåller information om den igenkända avsikten. JSON-svaret från LUIS extraheras av följande uttryck:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Självstudieprogrammet parsar inte JSON-resultatet, utan visar det endast i konsolfönstret.

![LUIS-igenkänningsresultat](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Ange igenkänningsspråk

Som standard känner LUIS igen avsikter på amerikansk engelska (`en-us`). Genom att tilldela en kod för nationella inställningar till egenskapen `SpeechRecognitionLanguage` i talkonfigurationen kan du känna igen avsikter på andra språk. Lägg exempelvis till `config.SpeechRecognitionLanguage = "de-de";` i självstudieprogrammet innan du skapar igenkännaren om du vill känna igen avsikter på tyska. Se [Språk som stöds](language-support.md#speech-to-text).

## <a name="continuous-recognition-from-a-file"></a>Kontinuerlig igenkänning från en fil

Följande kod illustrerar två ytterligare funktioner för avsiktsigenkänning med hjälp av Speech SDK. Den första, som nämnts tidigare, är kontinuerlig igenkänning, där igenkännaren genererar händelser när resultat finns tillgängliga. Dessa händelser kan sedan bearbetas av händelsehanterare som du anger. Med kontinuerlig igenkänning anropar du igenkännarens `StartContinuousRecognitionAsync()` för att starta igenkänning i stället för `RecognizeOnceAsync()`.

Den andra funktionen är att läsa det ljud som innehåller det tal som ska bearbetas från en WAV-fil. Detta inbegriper att skapa en ljudkonfiguration som kan användas vid skapandet av avsiktsigenkännaren. Filen måste vara enkanalig (mono) med en samplingsfrekvens på 16 kHz.

Om du vill prova dessa funktioner ersätter du innehållet i metoden `RecognizeIntentAsync()` med följande kod.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Ändra koden så att den innehåller din LUIS-slutpunktsnyckel, region och app-ID för att lägga till Home Automation-avsikterna som tidigare. Ändra `whatstheweatherlike.wav` till namnet på din ljudfil. Skapa sedan och kör.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta upp koden från den här artikeln i mappen samples/csharp/sharedcontent/console.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hur du känna igen tal](how-to-recognize-speech-csharp.md)
