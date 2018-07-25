---
title: Ordlista för den API tjänst för Språkförståelse (LUIS) | Microsoft Docs
description: Ordlistan beskrivs de termer som du kan stöta på när du arbetar med LUIS API Service.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: f6606a3a09698f236f9ebe2c21ec784ca84bb149
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225514"
---
# <a name="glossary"></a>Ordlista

## <a name="active-version"></a>Aktiva versionen

Den aktiva LUIS-versionen är den version som tar emot alla ändringar i modellen. I den [LUIS](luis-reference-regions.md) webbplats, om du vill göra ändringar i en version som inte är den aktiva versionen måste du först ställa in den här versionen som aktiv. 

## <a name="authoring"></a>Redigering

Redigera är möjligheten att skapa, hantera och distribuera en [LUIS-app](#luis-app), antingen med hjälp av den [LUIS](luis-reference-regions.md) webbplats eller [redigera API: er](https://aka.ms/luis-authoring-api). 

## <a name="authoring-key"></a>Redigera nyckel

Tidigare kallades ”Programmatic” nyckel. Används för att redigera appen. Används inte för produktionsnivån endpoint-frågor. Mer information finns i [viktiga begränsningar](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>Batch text JSON-fil

Batchfilen är en JSON-matris. Varje element i matrisen har tre egenskaper: `text`, `intent`, och `entities`. Den `entities` egenskapen är en matris. Matrisen kan vara tom. Om den `entities` matrisen är inte tom, som behövs för att korrekt identifiera entiteterna.

```JSON
[
    {
        "text": "drive me home",
        "intent": "None",
        "entities": []
    },
    {
        "text": "book a flight to orlando on the 25th",
        "intent": "BookFlight",
        "entities": [
            {
                "entity": "orlando",
                "type": "Location",
                "startIndex": 18,
                "endIndex": 25
            }
        ]
    }
]

```


## <a name="collaborator"></a>Medarbetare

En medarbetare är inte den [ägare](#owner) av appen, men inte har samma behörigheter för att lägga till, redigera och ta bort avsikter, entiteter, yttranden.

## <a name="currently-editing"></a>Redigerar

Samma som [active version](#active-version)

## <a name="domain"></a>Domän

I kontexten LUIS en **domän** är en del av kunskaper. Din domän är specifik för din app område. Detta kan vara ett allmänt område, till exempel appen resa agent. En resa agent-app kan också vara specifika för bara delarna av information för ditt företag, till exempel specifika geografiska platser, språken och tjänsterna. 

## <a name="endpoint"></a>Slutpunkt

Den [LUIS endpoint](https://aka.ms/luis-endpoint-apis) URL: en är där du kan skicka LUIS frågor efter den [LUIS-app](#luis-app) har skapats och publicerats. Slutpunkts-URL innehåller regionen för den publicerade appen samt app-ID. Du hittar slutpunkten på den **[publicera](luis-how-to-publish-app.md)** sidan för din app, i tabellen resurser och nycklar eller du kan hämta slutpunkts-URL från den [hämta Appinfo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API.

Det ser ut som en exempel-slutpunkt:

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|Frågeparametern|beskrivning|
|--|--|
|region| [publicerade region](luis-reference-regions.md#publishing-regions) |
|appID | LUIS-app-ID |
|prenumerations-ID | LUIS slutpunkt (prenumeration)-nyckel som skapas i Azure-portalen |
|frågor och | Uttryck |
|timezoneOffset| minutes|

## <a name="entity"></a>Entitet

[Entiteter](luis-concept-entity-types.md) är viktiga ord i [yttranden](luis-concept-utterance.md) som beskriver information som är relevanta för den [avsikt](luis-concept-intent.md), och ibland de är mycket viktigt att den. En entitet är i grunden en datatyp i LUIS. 

## <a name="f-measure"></a>F-mått

I [batch testning](luis-interactive-test.md#batch-testing), ett mått på det test precision.

## <a name="false-negative"></a>FALSKT negativt (TN)

I [batch testning](luis-interactive-test.md#batch-testing), datapunkter representerar yttranden som din app felaktigt förutse avsaknad av avsikt/målentiteten.

## <a name="false-positive"></a>Falsk positiv identifiering (TP)

I [batch testning](luis-interactive-test.md#batch-testing), datapunkter representerar yttranden som din app felaktigt förutse förekomsten av avsikt/målentiteten.

## <a name="features"></a>Funktioner

I machine learning, en [funktionen](luis-concept-feature.md) är en särskiljande egenskap eller ett attribut av data där dina system.

## <a name="intent"></a>Avsikten

En [avsikt](luis-concept-intent.md) representerar en aktivitet eller åtgärd som användaren vill utföra. Det är en syfte eller mål som är uttryckt i indata för en användare, till exempel en flygning för bokning, betala en faktura eller att söka efter en nyhetsartikel. LUIS, är avsiktlig förutsägelse baserad på hela uttryck. Entiteter, jämförelse, är ett uttryck.

## <a name="labeling"></a>Märkning

Etiketter är en process för att associera ett ord eller fraser i ett intent [uttryck](#utterance) med en [entitet](#entity) (datatype). 

## <a name="luis-app"></a>LUIS-app

En LUIS-app är en tränad datamodell för bearbetning av naturligt språk, inklusive [avsikter](#intent), [entiteter](#entity), och märkta [yttranden](#utterance).

## <a name="owner"></a>Ägare

Varje app har en ägare som är den person som skapade appen. Ägare kan lägga till [medarbetare](#collaborator).

## <a name="pattern"></a>Mönster
Funktionen tidigare mönstret ersätts med [mönster](luis-concept-patterns.md). Använda mönster för att förbättra prognosens noggrannhet genom att tillhandahålla färre utbildning exempel. 

## <a name="phrase-list"></a>Fras lista

En [frasen lista](luis-concept-feature.md#what-is-a-phrase-list-feature) innehåller en grupp med värden (ord eller fraser) som tillhör samma klass och måste behandlas på samma sätt (t.ex, namn på städer eller produkter). En utbytbara lista behandlas som synonymer. 

## <a name="prebuilt-domains"></a>Fördefinierade domän

En [fördefinierade domän](luis-how-to-use-prebuilt-domains.md) är en LUIS-app som har konfigurerats för en specifik domän, till exempel home automation (HomeAutomation) eller restaurang reservationer (RestaurantReservation). Den avsikter och yttranden entiteter har konfigurerats för den här domänen. 

## <a name="prebuilt-entity"></a>Fördefinierade entitet

En [fördefinierade entitet](luis-prebuilt-entities.md) är en entitet som LUIS tillhandahåller för vanliga typer av information, till exempel antal, URL: en och e-post. Du kan välja att lägga till en fördefinierade entitet i ditt program. 

## <a name="precision"></a>Precision
I [batch testning](luis-interactive-test.md#batch-testing), precision (kallas även förutsägande positiv) är andelen av relevanta yttranden bland hämtade yttranden.

## <a name="programmatic-key"></a>Programmässig nyckel

Omdöpt till [redigering nyckeln](#authoring-key). 

## <a name="publish"></a>Publicera

Publicera sätt som gör en LUIS [aktiva versionen](#active-version) finns på den mellanlagring eller produktion [endpoint](#endpoint).  

## <a name="quota"></a>Kvot

LUIS kvoten är begränsningen av den [Azure-prenumeration nivå](https://aka.ms/luis-price-tier). LUIS-kvot kan begränsas av båda begäranden per sekund (http-Status 429) och Totalt antal begäranden under en månad (http-Status 403). 

## <a name="recall"></a>Återkallande
I [batch testning](luis-interactive-test.md#batch-testing), återkalla (även kallad känslighet), är möjligheten för LUIS för att generalisera. 

## <a name="semantic-dictionary"></a>Semantisk ordlista
En semantiska ordlista finns på sidan med listan över entiteten samt sidan fras. Den semantiska innehåller förslag på ord baserat på den aktuella omfattningen.

## <a name="sentiment-analysis"></a>Attitydanalys
Attitydanalys ger positivt eller negativt värde av yttranden som tillhandahålls av [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/). 

## <a name="speech-priming"></a>Tal promotor

Tal promotor kan ditt taltjänsten vara förbereder sig med LUIS-modellen. Se [aktivera tal promotor ](luis-how-to-publish-app.md#enable-speech-priming).

## <a name="spelling-correction"></a>Stavningskontroll

På sidan Publicera aktivera [Bing-stavningskontroll](luis-how-to-publish-app.md#enable-bing-spell-checker) att korrigera felstavade ord. i yttranden innan förutsägelse. 

## <a name="starter-key"></a>Starter-nyckel

Samma som [programmässiga nyckeln](#programmatic-key), omdöpt till redigering nyckel.

## <a name="subscription-key"></a>Prenumerationsnyckel

Prenumerationsnyckeln är den **endpoint** nyckel som är associerad med tjänsten LUIS [du skapade i Azure](luis-how-to-azure-subscription.md). Den här nyckeln är inte den [redigering nyckeln](#programmatic-key). Om du har en slutpunktsnyckel kan användas för alla begäranden om slutpunkten i stället för nyckeln för redigering. Du kan se din aktuella slutpunktsnyckeln i slutpunkts-URL längst ned i [ **publicera appen** sidan](luis-how-to-publish-app.md) i [LUIS](luis-reference-regions.md) webbplats. Det är värdet för **prenumerationsnyckel** namn/värde-par. 

## <a name="test"></a>Test

[Testa](luis-interactive-test.md#test-your-app) en LUIS-app innebär att skicka ett uttryck till LUIS och visa JSON-resultat.

## <a name="timezoneoffset"></a>Förskjutning i tidszon

Slutpunkten innehåller timezoneOffset. Detta är antalet minuter som du vill lägga till eller ta bort från datetimeV2 fördefinierade entitet. Till exempel om uttryck är är ”vilken tid är det nu”?, datetimeV2 som returneras den aktuella tiden för klientbegäran. Om klientens begäran kommer från en bot eller andra program som inte är samma som din robot användare, bör du skicka in förskjutningen mellan roboten och användaren. 

Se [ändra tidszon för fördefinierade datetimeV2 entitet](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
En token är den minsta enheten som kan förses med i en entitet. Tokenisering baseras på programmets [kultur](luis-supported-languages.md#tokenization).

## <a name="train"></a>Träna

Utbildning är en process där du undervisar LUIS om ändringar av den [aktiva versionen](#active-version) sedan den senaste utbildningen.

## <a name="true-negative"></a>SANT negativt (TN)

I [batch testning](luis-interactive-test.md#batch-testing), datapunkter representerar yttranden som din app korrekt förutse avsaknad av avsikt/målentiteten.

## <a name="true-positive"></a>Sann positiv händelse (TP)

I [batch testning](luis-interactive-test.md#batch-testing), datapunkter representerar yttranden som din app korrekt förutse förekomsten av avsikt/målentiteten.

## <a name="utterance"></a>Uttryck

Ett uttryck är ett naturligt språk, exempelvis ”boken 2 tickets till Seattle nästa tisdag”. Exempel yttranden läggs till i avsikten. 

## <a name="version"></a>Version

En LUIS [version](luis-how-to-manage-versions.md) är en specifika datamodell som är associerade med en LUIS-app-ID och den publicerade slutpunkten. Varje LUIS-app har minst en version.
