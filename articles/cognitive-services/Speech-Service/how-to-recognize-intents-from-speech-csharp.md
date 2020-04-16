---
title: 'Så här känner du igen avsikter från tal med tal SDK C #'
titleSuffix: Azure Cognitive Services
description: I den här guiden får du lära dig hur du känner igen avsikter från tal med tal-SDK för C#.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: trbye
ms.openlocfilehash: 41ebcb7b44ea88af06a30a611960fd8bb0ceddee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402228"
---
# <a name="how-to-recognize-intents-from-speech-using-the-speech-sdk-for-c"></a>Så här känner du igen avsikter från tal med tal SDK för C #

Cognitive Services [Speech SDK](speech-sdk.md) integreras med [tjänsten Språkförståelse (LUIS)](https://www.luis.ai/home) för att ge **avsiktsigenkänning**. En avsikt är något som användaren vill göra: boka en flygning, titta på vädret eller ringa ett samtal. Användaren kan använda de termer som känns naturliga. Med hjälp av maskininlärning mappar LUIS användarbegäranden till de avsikter du har definierat.

> [!NOTE]
> Ett LUIS-programmet definierar avsikter och entiteter som du vill identifiera. Det är separat från det C#-program som använder Speech-tjänsten. I den här artikeln betyder ”app” LUIS-appen medan ”program” innebär C#-koden.

I den här guiden använder du Speech SDK för att utveckla ett C#-konsolprogram som härleder avsikter från användaryttranden via enhetens mikrofon. Du lär dig följande:

> [!div class="checklist"]
>
> - Skapa ett Visual Studio-projekt som refererar till Speech SDK NuGet-paketet
> - Skapa en talkonfiguration och få en avsiktsre recognizeer
> - Hämta modellen för LUIS-appen och lägga till de avsikter som du behöver
> - Ange språk för taligenkänning
> - Identifiera tal från en fil
> - Använd asynkron, händelsedriven kontinuerlig igenkänning

## <a name="prerequisites"></a>Krav

Se till att du har följande objekt innan du påbörjar den här guiden:

- Ett LUIS-konto. Du kan skaffa ett kostnadsfritt via [LUIS-portalen](https://www.luis.ai/home).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (valfri utgåva).

## <a name="luis-and-speech"></a>LUIS och tal

LUIS integreras med Speech-tjänsten för att identifiera avsikter från tal. Du behöver inte en Speech-tjänstprenumeration, bara LUIS.

LUIS använder tre typer av nycklar:

| Nyckeltyp  | Syfte                                               |
| --------- | ----------------------------------------------------- |
| Redigering | Gör att du kan skapa och ändra LUIS-appar programmässigt |
| Starter   | Gör att du kan testa LUIS-programmet med endast text   |
| Slutpunkt  | Auktoriserar åtkomst till en viss LUIS-app            |

För den här guiden behöver du nyckeltypen slutpunkt. Den här guiden använder exemplet Home Automation LUIS-appen, som du kan skapa genom att följa snabbstarten [Fördefinierad startsida för hemautomatisering.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) Om du har skapat en egen LUIS-app kan du använda den i stället.

När du skapar en LUIS-app genererar LUIS automatiskt en startnyckel så att du kan testa appen med hjälp av textfrågor. Den här nyckeln aktiverar inte integrering av taltjänsten och fungerar inte med den här guiden. Skapa en LUIS-resurs i Azure-instrumentpanelen och tilldela den till LUIS-appen. Du kan använda den kostnadsfria prenumerationsnivån för den här guiden.

När du har skapat LUIS-resursen i Azure-instrumentpanelen loggar du in på [LUIS-portalen,](https://www.luis.ai/home)väljer ditt program på sidan **Mina appar** och växlar sedan till appens **hantera-sida.** Slutligen väljer du **Nycklar och slutpunkter** i sidofältet.

![LUIS-portalens inställningar för nycklar och slutpunkter](media/sdk/luis-keys-endpoints-page.png)

På sidan **Inställningar för nycklar och slutpunkt:**

1. Bläddra ned till avsnittet **Resurser och nycklar** och välj Tilldela **resurs**.
1. Gör följande ändringar i dialogrutan **Tilldela en nyckel till appen:**

   - Välj **Microsoft**under **Klient**.
   - Under **Prenumerationsnamn**väljer du den Azure-prenumeration som innehåller den LUIS-resurs som du vill använda.
   - Under **Nyckel**väljer du den LUIS-resurs som du vill använda med appen.

   Om en stund visas den nya prenumerationen i tabellen längst ned på sidan.

1. Markera ikonen bredvid en tangent för att kopiera den till Urklipp. (Du kan använda valfri nyckel.)

![Prenumerationsnycklar för LUIS-app](media/sdk/luis-keys-assigned.png)

## <a name="create-a-speech-project-in-visual-studio"></a>Skapa ett talprojekt i Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-the-code"></a>Lägga till koden

Därefter lägger du till kod i projektet.

1. Öppna filen **Program.cs**från **Solution Explorer**.

1. Ersätt `using` satsernas block i början av filen med följande deklarationer:

   [!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

1. Ersätt den `Main()` angivna metoden med följande asynkrona motsvarighet:

   ```csharp
   public static async Task Main()
   {
       await RecognizeIntentAsync();
       Console.WriteLine("Please press Enter to continue.");
       Console.ReadLine();
   }
   ```

1. Skapa en tom asynkron metod `RecognizeIntentAsync()`enligt bilden här:

   ```csharp
   static async Task RecognizeIntentAsync()
   {
   }
   ```

1. Lägg till den här koden i brödtexten för den här nya metoden:

   [!code-csharp[Intent recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

1. Ersätt platshållarna i den här metoden med din LUIS-prenumerationsnyckel, region och app-ID på följande sätt.

   | Platshållare | Ersätt med |
   | ----------- | ------------ |
   | `YourLanguageUnderstandingSubscriptionKey` | Din LUIS-slutpunktsnyckel. Återigen måste du hämta det här objektet från din Azure-instrumentpanel, inte en "startnyckel". Du hittar den på appens sida **Nycklar och slutpunkter** (under **Hantera)** i [LUIS-portalen](https://www.luis.ai/home). |
   | `YourLanguageUnderstandingServiceRegion` | Den korta identifieraren för den region som din LUIS-prenumeration är i, till exempel `westus` för USA, västra. Se [regioner](regions.md). |
   | `YourLanguageUnderstandingAppId` | LUIS-app-ID. Du hittar den på appens **inställningssida** i [LUIS-portalen](https://www.luis.ai/home). |

Med dessa ändringar gjorda kan du skapa (**Control+Skift+B**) och köra (**F5**) programmet. När du uppmanas att göra det kan du prova att säga "Släcka lamporna" i datorns mikrofon. Programmet visar resultatet i konsolfönstret.

Följande avsnitt innehåller en beskrivning av koden.

## <a name="create-an-intent-recognizer"></a>Skapa en avsiktsigenkännare

Först måste du skapa en talkonfiguration från LUIS-slutpunktsnyckeln och -regionen. Du kan använda talkonfigurationer för att skapa identifierare för de olika funktionerna i Tal-SDK. Talkonfigurationen har flera sätt att ange vilken prenumeration du vill använda. här använder `FromSubscription`vi , som tar prenumerationsnyckeln och regionen.

> [!NOTE]
> Använd nyckeln och regionen för din LUIS-prenumeration, inte en taltjänstprenumeration.

Nästa steg är att skapa en avsiktsigenkännare med hjälp av `new IntentRecognizer(config)`. Eftersom konfigurationen redan vet vilken prenumeration som ska användas behöver du inte ange prenumerationsnyckeln och slutpunkten igen när du skapar identifieraren.

## <a name="import-a-luis-model-and-add-intents"></a>Importera en LUIS-modell och lägga till avsikter

Nu importerar du modellen från LUIS-appen med hjälp av `LanguageUnderstandingModel.FromAppId()` och lägger till de LUIS-avsikter som du vill känna igen igenkännarens `AddIntent()`-metod. De här två stegen förbättrar taligenkänningens noggrannhet genom att ange ord som det är troligt att användaren använder i sina begäranden. Du behöver inte lägga till alla appens avsikter om du inte behöver känna igen dem alla i ditt program.

Om du vill lägga till avsikter måste du ange tre argument: `model`LUIS-modellen (som har skapats och har fått namnet), avsiktsnamnet och ett avsikts-ID. Skillnaden mellan ID och namnet är som följer.

| `AddIntent()`&nbsp;argument | Syfte |
| --------------------------- | ------- |
| `intentName` | Namnet på avsikten enligt vad som definierats i LUIS-appen. Det här värdet måste stämma överens med LUIS-avsiktsnamnet exakt. |
| `intentID` | Ett ID som tilldelas till en igenkänd avsikt av Speech SDK. Detta värde kan vara vad du vill; Den behöver inte motsvara avsiktsnamnet enligt definitionen i LUIS-appen. Om flera avsikter till exempel hanteras av samma kod kan du använda samma ID för dem. |

Home Automation LUIS-appen har två avsikter: en för att slå på en enhet och en annan för att stänga av en enhet. Raderna nedan lägger till dessa avsikter till igenkännaren. Ersätt de tre `AddIntent`-raderna i metoden `RecognizeIntentAsync()` med den här koden.

```csharp
recognizer.AddIntent(model, "HomeAutomation.TurnOff", "off");
recognizer.AddIntent(model, "HomeAutomation.TurnOn", "on");
```

I stället för att lägga till `AddAllIntents` enskilda avsikter kan du också använda metoden för att lägga till alla avsikter i en modell till identifieraren.

## <a name="start-recognition"></a>Starta igenkänning

När igenkännaren har skapats och avsikterna lagts till kan igenkänningen börja. Speech SDK stöder både engångsigenkänning och kontinuerlig igenkänning.

| Igenkänningsläge | Metoder att anropa | Resultat |
| ---------------- | --------------- | ------ |
| Engångsigenkänning | `RecognizeOnceAsync()` | Returnerar den igenkända avsikten, om sådan finns, efter ett yttrande. |
| Kontinuerlig igenkänning | `StartContinuousRecognitionAsync()`<br>`StopContinuousRecognitionAsync()` | Känner igen flera yttranden. avger händelser (till `IntermediateResultReceived`exempel) när resultat är tillgängliga. |

Programmet använder single-shot läge `RecognizeOnceAsync()` och så samtal för att börja erkännande. Resultatet är ett `IntentRecognitionResult`-objekt som innehåller information om den igenkända avsikten. Du extraherar LUIS JSON-svaret med hjälp av följande uttryck:

```csharp
result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult)
```

Programmet tolkar inte JSON-resultatet. Den visar bara JSON-texten i konsolfönstret.

![Enstaka LUIS-igenkänningsresultat](media/sdk/luis-results.png)

## <a name="specify-recognition-language"></a>Ange igenkänningsspråk

Som standard känner LUIS igen avsikter på amerikansk engelska (`en-us`). Genom att tilldela en kod för nationella inställningar till egenskapen `SpeechRecognitionLanguage` i talkonfigurationen kan du känna igen avsikter på andra språk. Lägg till `config.SpeechRecognitionLanguage = "de-de";` exempel till i vår ansökan innan du skapar igenkännaren för att känna igen avsikter på tyska. Mer information finns i [LUIS språkstöd](../LUIS/luis-language-support.md#languages-supported).

## <a name="continuous-recognition-from-a-file"></a>Kontinuerlig igenkänning från en fil

Följande kod illustrerar två ytterligare funktioner för avsiktsigenkänning med hjälp av Speech SDK. Den första, som nämnts tidigare, är kontinuerlig igenkänning, där igenkännaren genererar händelser när resultat finns tillgängliga. Dessa händelser kan sedan bearbetas av händelsehanterare som du anger. Med kontinuerlig igenkänning anropar `StartContinuousRecognitionAsync()` du identifierarens `RecognizeOnceAsync()`metod för att starta igenkänningen i stället för .

Den andra funktionen är att läsa det ljud som innehåller det tal som ska bearbetas från en WAV-fil. Implementering innebär att skapa en ljudkonfiguration som kan användas när avsiktsrecenset skapas. Filen måste vara enkanalig (mono) med en samplingsfrekvens på 16 kHz.

Om du vill prova dessa funktioner tar `RecognizeIntentAsync()` du bort eller kommenterar metodens brödtext och lägger till följande kod i dess ställe.

[!code-csharp[Intent recognition by using events from a file](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

Ändra koden så att den innehåller din LUIS-slutpunktsnyckel, region och app-ID för att lägga till Home Automation-avsikterna som tidigare. Ändra `whatstheweatherlike.wav` namnet på den inspelade ljudfilen. Sedan bygga, kopiera ljudfilen till byggkatalogen och kör programmet.

Om du till exempel säger "Stäng av lamporna", pausar och säger "Slå på lamporna" i den inspelade ljudfilen kan konsolutdata som liknar följande visas:

![Resultat för LUIS-igenkänning av ljudfil](media/sdk/luis-results-2.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter koden från den här artikeln i **mappen samples/csharp/sharedcontent/console.**

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Känna igen tal från en mikrofon](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)
