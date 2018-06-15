---
title: Ordlista för API-tjänsten språk förstå (THOMAS) | Microsoft Docs
description: Ordlistan beskrivs de termer som kan uppstå när du arbetar med LUIS API Service.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 420f268090dbcfcc4f2fa7383b8b8892952030ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355761"
---
# <a name="glossary"></a>Ordlista

## <a name="active-version"></a>Aktiva versionen

Den aktiva THOMAS-versionen är den version som tar emot alla ändringar i modellen. I den [THOMAS](luis-reference-regions.md) webbplats om du vill göra ändringar i en version som inte är den aktiva versionen måste du först ställa in den versionen som aktiv. 

## <a name="authoring"></a>Redigering

Redigering är möjligheten att skapa, hantera och distribuera en [THOMAS app](#luis-app), antingen med hjälp av den [THOMAS](luis-reference-regions.md) webbplats eller [redigering API: er](https://aka.ms/luis-authoring-api). 

## <a name="authoring-key"></a>Redigera nyckel

Tidigare kallades ”Programmatic” nyckel. Används för att skapa appen. Används inte för produktion nivå endpoint frågor. Mer information finns i [nyckeln gränser](luis-boundaries.md#key-limits).   

## <a name="batch-test-json-file"></a>Batch text JSON-fil

Kommandofilen är en JSON-matris. Varje element i matrisen har tre egenskaper: `text`, `intent`, och `entities`. Den `entities` är en matris. Matrisen får vara tomt. Om den `entities` matrisen inte är tom, som behövs för att identifiera enheterna.

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


## <a name="collaborator"></a>Deltagare

En samarbetspartner är inte den [ägare](#owner) av appen, men har samma behörigheter för att lägga till, redigera och ta bort avsikter, enheter, utterances.

## <a name="currently-editing"></a>Redigerar

Samma som [aktiva versionen](#active-version)

## <a name="domain"></a>Domän

I kontexten THOMAS en **domän** är en del av informationen. Din domän är specifik för din app område. Detta kan vara ett allmänt område, till exempel appen resa agent. En resa agent-app kan också vara specifika för bara delarna av information för ditt företag, till exempel specifika geografiska platser, språk och tjänster. 

## <a name="endpoint"></a>slutpunkt

Den [THOMAS endpoint](https://aka.ms/luis-endpoint-apis) URL: en är där du kan skicka THOMAS frågor efter den [THOMAS app](#luis-app) har skapats och publicerats. Slutpunkts-URL innehåller regionen och publicerade appen som app-ID. Du kan hitta slutpunkten på den **[publicera](publishapp.md)** sidan i din app i tabellen resurser och nycklar eller om du kan hämta slutpunkts-URL från den [hämta App-Info](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API.

Det ser ut som en exempel-slutpunkt:

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|QueryString-parametern|description|
|--|--|
|region| [publicerade region](luis-reference-regions.md#publishing-regions) |
|appID | THOMAS app-ID |
|Prenumerations-ID | THOMAS prenumeration nyckeln som skapats i Azure-portalen |
|Q | utterance |
|timezoneOffset| minutes|

## <a name="entity"></a>Entitet

[Entiteter](luis-concept-entity-types.md) är viktiga ord i [utterances](luis-concept-utterance.md) som beskriver information som är relevanta för den [avsikt](luis-concept-intent.md), och ibland de är mycket viktigt att den. En entitet är i grunden en datatyp i THOMAS. 

## <a name="f-measure"></a>F-mått

I [batch testar][batch-testing], ett mått på den test noggrannhet.

## <a name="false-negative"></a>FALSKT negativt (TN)

I [batch testar][batch-testing], datapunkter representerar utterances som din app felaktigt förutsade avsaknaden av avsikt/målentiteten.

## <a name="false-positive"></a>Falsklarm (TP)

I [batch testar][batch-testing], datapunkter representerar utterances som din app felaktigt förutsade förekomsten av avsikt/målentiteten.

## <a name="features"></a>Funktioner

I machine learning, en [funktionen](luis-concept-feature.md) är en särskiljande egenskap eller ett attribut av data där systemet.

## <a name="intent"></a>Avsikten

En [avsikt](luis-concept-intent.md) representerar en aktivitet eller åtgärd som användaren vill utföra. Det är ett syfte eller målet uttryckt i indata för en användare, till exempel Bokningstyp en svarta, betalar en faktura eller söka efter en nyhetsartikel i. I THOMAS utifrån hela utterance avsiktshantering förutsägelser. Entiteter, däremot är en utterance.

## <a name="labeling"></a>etiketter

Etiketter är en process för att associera ett ord eller en fras i en avsikt [utterance](#utterance) med en [entiteten](#entity) (datatype). 

## <a name="luis-app"></a>THOMAS app

En THOMAS app är en tränad datamodell för behandling av naturligt språk inklusive [intents](#intent), [entiteter](#entity), och etiketter [utterances](#utterance).

## <a name="owner"></a>Ägare

Varje app har en ägare som är den person som skapade appen. Ägare kan lägga till [medarbetare](#collaborator).

## <a name="pattern"></a>Mönster
Funktionen tidigare mönstret ersätts med [mönster](luis-concept-patterns.md). Använda mönster för att förbättra prognosens noggrannhet genom att tillhandahålla färre utbildning exempel. 

## <a name="phrase-list"></a>Frasen lista

En [frasen listan](luis-concept-feature.md#what-is-a-phrase-list-feature) innehåller en uppsättning värden (ord eller fraser) som tillhör samma klass och måste behandlas på samma sätt (t.ex, namn på städer eller produkter). En lista för utbytbara behandlas som synonymer. 

## <a name="prebuilt-domains"></a>Fördefinierade domän

En [färdiga domän](luis-how-to-use-prebuilt-domains.md) är en THOMAS app som konfigurerats för en specifik domän, till exempel hemma automation (HomeAutomation) eller restaurang reservationer (RestaurantReservation). Avsikter, utterances och enheter har konfigurerats för den här domänen. 

## <a name="prebuilt-entity"></a>Fördefinierade entitet

En [färdiga entiteten](pre-builtentities.md) är en entitet THOMAS ger för vanliga typer av information, till exempel antal, URL och e-post. Du vill lägga till en fördefinierad entitet i ditt program. 

## <a name="precision"></a>Precision
I [batch testar][batch-testing], precision (kallas även positivt värde) är del av relevanta utterances bland hämtade utterances.

## <a name="programmatic-key"></a>Programmässiga nyckel

Bytt namn till [redigering nyckeln](#authoring-key). 

## <a name="publish"></a>Publicera

Publicera sätt att göra en THOMAS [aktiva versionen](#active-version) finns på den mellanlagring eller produktion [endpoint](#endpoint).  

## <a name="quota"></a>kvot

THOMAS kvot är att begränsa den [Azure-prenumeration nivå](https://aka.ms/luis-price-tier). THOMAS kvoten kan begränsas av båda begäranden per sekund (http-Status 429) och Totalt antal begäranden under en månad (http-Status 403). 

## <a name="recall"></a>Återkalla
I [batch testar][batch-testing], återkalla (kallas även känslighet), är möjligheten för THOMAS att generalisera. 

## <a name="semantic-dictionary"></a>Semantiska ordlista
En semantiska ordlista finns på sidan entitet som sidan frasen. Den semantiska ordlistan innehåller förslag på ord baserat på den aktuella omfattningen.

## <a name="sentiment-analysis"></a>Sentiment analys
Sentiment analys ger positiva eller negativa värden för utterances som tillhandahålls av [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/). 

## <a name="speech-priming"></a>Tal promotor

Tal promotor kan ditt tal tjänsten göras klar med THOMAS modellen för att användning. Se [aktivera tal promotor ](publishapp.md#enable-speech-priming).

## <a name="spelling-correction"></a>Stavningskontroll

På sidan Publicera aktivera [Bing stavningskontrollen](publishapp.md#enable-bing-spell-checker) att korrigera stavningen i utterances innan förutsägelse. 

## <a name="starter-key"></a>Start-nyckel

Samma som [programmässiga nyckeln](#programmatic-key), bytt namn till redigering nyckel.

## <a name="subscription-key"></a>Prenumerationen nyckel

Nyckeln prenumerationen är den nyckel som är associerad med tjänsten THOMAS [du skapade i Azure](luis-how-to-azure-subscription.md). Den här nyckeln är inte den [redigering nyckeln](#programmatic-key). Om du har en prenumeration nyckel kan användas för alla endpoint-förfrågningar i stället för nyckeln för redigering. Du kan se din aktuella prenumeration nyckel i slutpunkts-URL längst ned i [ **publicera appen** sidan](publishapp.md) i [THOMAS](luis-reference-regions.md) webbplats. Det är värdet för **prenumeration nyckeln** namn/värde-par. 

## <a name="test"></a>Test

[Testa](train-test.md#test-your-app) en THOMAS app innebär att skicka en utterance till THOMAS och visa JSON resultat.

## <a name="timezoneoffset"></a>Förskjutning av tidszonen

Slutpunkten innehåller timezoneOffset. Detta är antalet minuter som du vill lägga till eller ta bort från datetimeV2 färdiga entitet. Till exempel om utterance är är ”vilken tid är den nu”?, datetimeV2 som returnerades den aktuella tiden för klientbegäran. Om klientens begäran kommer från en bot eller andra program som inte är samma som din bot användare, bör du överföra i förskjutningen mellan bot och användaren. 

Se [ändra tidszon på fördefinierade datetimeV2 entiteten](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
En token är den minsta enheten kan förses med i en entitet. Tokenisering baseras på programmets [kultur](luis-supported-languages.md#tokenization).

## <a name="train"></a>Train

Utbildning är lära THOMAS om ändringar i [aktiva versionen](#active-version) sedan den senaste utbildningen.

## <a name="true-negative"></a>True negativt (TN)

I [batch testar][batch-testing], datapunkter representerar utterances som din app korrekt förutsade avsaknaden av avsikt/målentiteten.

## <a name="true-positive"></a>True positiv (TP)

I [batch testar][batch-testing], datapunkter representerar utterances som din app korrekt förutsade förekomsten av avsikt/målentiteten.

## <a name="utterance"></a>Utterance

En utterance är ett naturligt språk, exempelvis ”book 2 biljetter till Seattle nästa tisdag”. Exempel utterances läggs till avsikten. 

## <a name="version"></a>Version

En THOMAS [version](luis-how-to-manage-versions.md) är en datamodell som är associerade med en THOMAS app-ID och publicerade slutpunkten. Varje THOMAS app har minst en version.

[batch-testing]: train-test.md#batch-testing
