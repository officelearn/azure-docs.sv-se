---
title: Ord lista – LUIS
titleSuffix: Azure Cognitive Services
description: Ordlistan beskrivs de termer som du kan stöta på när du arbetar med LUIS API Service.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220956"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Ord lista för språk förståelse för vanliga ord listor och koncept
Den Språkförståelse (LUIS) ordlista förklarar villkor som du kan stöta på när du arbetar med LUIS API Service.

## <a name="active-version"></a>Aktiv version

Den aktiva LUIS-versionen är den version som tar emot alla ändringar i modellen. Om du vill göra ändringar i en version som inte är den aktiva versionen i [Luis](luis-reference-regions.md) -portalen måste du först ange den versionen som aktiv.

## <a name="authoring"></a>Redigerings

Redigering är möjligheten att skapa, hantera och distribuera en Luis- [app](#luis-app), antingen med hjälp av [Luis](luis-reference-regions.md) -portalen eller [redigerings-API: er](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a>Redigerings nyckel

Tidigare kallades ”Programmatic” nyckel. Används för att redigera appen. Används inte för produktionsnivån endpoint-frågor. Mer information finns i [nyckel gränser](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a>Batch test-JSON-fil

Batch-testning är möjligheten att verifiera en aktuell LUIS-Apps modell med en konsekvent och känd test uppsättning av användar-yttranden. Batch-testet definieras i en [JSON-formaterad fil](luis-concept-batch-test.md#batch-file-format).

Se även:
* [Koncept](luis-concept-batch-test.md)
* [Anvisningar](luis-how-to-batch-test.md)
* [Självstudie](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a>Medarbetare

En medarbetare/deltagare, är inte [ägare](#owner) till appen, men har samma behörighet för att lägga till, redigera och ta bort avsikter, entiteter, yttranden.

## <a name="contributor"></a>Arbeta

En deltagare är samma sak som en [medarbetare](#collaborator).

## <a name="descriptor"></a>Script

En beskrivning är en [funktion](#features) som tillämpas på en modell i inlärnings tid, inklusive [fras listor](#phrase-list) och [entiteter](#entity). 

## <a name="domain"></a>Domänsuffix

I LUIS-kontexten är en **domän** ett kunskaps området. Din domän är specifik för din app område. Detta kan vara ett allmänt område, till exempel appen resa agent. En resa agent-app kan också vara specifika för bara delarna av information för ditt företag, till exempel specifika geografiska platser, språken och tjänsterna.

## <a name="endpoint"></a>Endpoint

[Luis-slutpunktens](https://go.microsoft.com/fwlink/?linkid=2092356) URL är den plats där du skickar Luis frågor när [appen Luis](#luis-app) har skapats och publicerats. Slutpunkts-URL innehåller regionen för den publicerade appen samt app-ID. Du hittar slut punkten på sidan **[nycklar och slut punkter](luis-how-to-azure-subscription.md)** i din app, eller så kan du hämta slut punkts-URL: en från [Hämta app information](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API.

## <a name="entity"></a>Entitetsrelation

[Entiteter](luis-concept-entity-types.md) är viktiga ord i [yttranden](luis-concept-utterance.md) som beskriver information som är relevant för [avsikten](luis-concept-intent.md)och som ibland är nödvändig för IT. En entitet är i grunden en datatyp i LUIS.

## <a name="f-measure"></a>F-mått

I [batch-testning](luis-interactive-test.md#batch-testing)är det ett mått på testets noggrannhet.

## <a name="false-negative"></a>Falskt negativt (FN)

I [batch-testning](luis-interactive-test.md#batch-testing)representerar data punkterna yttranden där appen felaktigt förutsäger frånvaron av mål avsikten/entiteten.

## <a name="false-positive"></a>Falskt positivt (RP)

I [batch-testning](luis-interactive-test.md#batch-testing)representerar data punkterna yttranden där appen felaktigt förutsäger förekomsten av mål avsikten/entiteten.

## <a name="features"></a>Egenskaper

I Machine Learning är en [funktion](luis-concept-feature.md) en särskiljande egenskaps-eller dataattribut som systemet iakttar.

## <a name="intent"></a>Avsikt

En [avsikt](luis-concept-intent.md) representerar en uppgift eller åtgärd som användaren vill utföra. Det är en syfte eller mål som är uttryckt i indata för en användare, till exempel en flygning för bokning, betala en faktura eller att söka efter en nyhetsartikel. LUIS, är avsiktlig förutsägelse baserad på hela uttryck. Entiteter, jämförelse, är ett uttryck.

## <a name="labeling"></a>Märkning

Etikettering, eller markering, är en process för att associera ett ord eller en fras i en avsikts [uttryck](#utterance) med en [entitet](#entity) (datatyp).

## <a name="luis-app"></a>LUIS-app

En LUIS-app är en samling språk modeller för bearbetning av naturligt språk, inklusive [avsikter](#intent), [entiteter](#entity)och etiketterade [yttranden](#utterance).

## <a name="owner"></a>Innehavare

Varje app har en ägare som är den person som skapade appen. Ägaren kan lägga till [medarbetare](#collaborator).

## <a name="pattern"></a>Skuggning
Den föregående mönster funktionen ersätts med [mönster](luis-concept-patterns.md). Använda mönster för att förbättra prognosens noggrannhet genom att tillhandahålla färre utbildning exempel.

## <a name="phrase-list"></a>Fras lista

En [fras lista](luis-concept-feature.md) innehåller en grupp med värden (ord eller fraser) som tillhör samma klass och som måste behandlas på samma sätt (t. ex. namn på städer eller produkter). En utbytbara lista behandlas som synonymer.

## <a name="prebuilt-domains"></a>Fördefinierad domän

En [fördefinierad domän](luis-how-to-use-prebuilt-domains.md) är en Luis-app som kon figurer ATS för en speciell domän, till exempel Home Automation (HomeAutomation) eller restaurang reservationer (RestaurantReservation). Den avsikter och yttranden entiteter har konfigurerats för den här domänen.

## <a name="prebuilt-entity"></a>Fördefinierad entitet

En [fördefinierad entitet](luis-prebuilt-entities.md) är en entitets Luis som innehåller vanliga typer av information som t. ex. nummer, URL och e-post. Du kan välja att lägga till en fördefinierade entitet i ditt program.

## <a name="precision"></a>Tillräcklig
I [batch-testning](luis-interactive-test.md#batch-testing)är precision (även kallat positivt förutsägande värde) den andel av relevanta yttranden bland de hämtade yttranden.

## <a name="programmatic-key"></a>Programmerings nyckel

Har bytt namn till [redigerings nyckel](#authoring-key).

## <a name="publish"></a>Publish

Publicering innebär att göra en LUIS aktiv-version tillgänglig antingen för mellanlagrings-eller produktions [slut punkten](#endpoint).  

## <a name="quota"></a>Resurser

LUIS-kvoten är begränsningen för [Azure-prenumerations nivån](https://aka.ms/luis-price-tier). LUIS-kvot kan begränsas av båda begäranden per sekund (http-Status 429) och Totalt antal begäranden under en månad (http-Status 403).

## <a name="recall"></a>Föregående
I [batch-testning](luis-interactive-test.md#batch-testing)kan du återkalla (även kallat känslighet) för att Luis ska kunna generaliseras.

## <a name="semantic-dictionary"></a>Semantisk ord lista
En semantiska ordlista finns på sidan med listan över entiteten samt sidan fras. Den semantiska innehåller förslag på ord baserat på den aktuella omfattningen.

## <a name="sentiment-analysis"></a>Attitydanalys
Sentiment-analys ger positiva eller negativa värden för yttranden som tillhandahålls av [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a>Tal Prima

Tal promotor kan ditt taltjänsten vara förbereder sig med LUIS-modellen.

## <a name="spelling-correction"></a>Stavnings korrigering

Aktivera Bing-stavningskontroll att korrigera felstavade ord. i yttranden innan förutsägelse.

## <a name="starter-key"></a>Start nyckel

En kostnads fri nyckel som ska användas när den först börjar med LUIS.

## <a name="structure"></a>Hierarkistruktur

Lägg till strukturen till en enhet som har registrerats av enheten för att tillhandahålla del komponenter med beskrivningar (funktioner) och begränsningar (reguljära uttryck eller List enheter).

## <a name="subscription-key"></a>Prenumerations nyckel

Prenumerations nyckeln är den **förutsägelse slut punkt** nyckel som är kopplad till den Luis-tjänst [som du skapade i Azure](luis-how-to-azure-subscription.md). Den här nyckeln är inte [redigerings nyckel](#programmatic-key). Om du har en slutpunktsnyckel kan användas för alla begäranden om slutpunkten i stället för nyckeln för redigering. Du kan se den aktuella slut punkts nyckeln i slut punkts-URL: en längst ned på [sidan **nycklar och slut punkter** ](luis-how-to-azure-subscription.md) på [Luis](luis-reference-regions.md) webbplats. Det är värdet för namn/värde-paret för **prenumerations nyckel** .

## <a name="test"></a>Koncentration

Att [testa](luis-interactive-test.md#test-your-app) en Luis app innebär att skicka en uttryck till Luis och Visa JSON-resultatet.

## <a name="timezoneoffset"></a>Förskjutning av tidszon

Slutpunkten innehåller timezoneOffset. Detta är antalet minuter som du vill lägga till eller ta bort från datetimeV2 fördefinierade entitet. Till exempel om uttryck är är ”vilken tid är det nu”?, datetimeV2 som returneras den aktuella tiden för klientbegäran. Om klientens begäran kommer från en bot eller andra program som inte är samma som din robot användare, bör du skicka in förskjutningen mellan roboten och användaren.

Se [ändra tidszon för en fördefinierad datetimeV2-entitet](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Åtkomsttokenbegäran
En token är den minsta enheten som kan förses med i en entitet. Tokenisering baseras på programmets [kultur](luis-language-support.md#tokenization).

## <a name="train"></a>Utbilda

Träning är en process för att lära sig LUIS om eventuella ändringar i den aktiva versionen sedan den senaste utbildningen.

## <a name="true-negative"></a>Sant negativt (TN)

I [batch-testning](luis-interactive-test.md#batch-testing)representerar data punkterna yttranden där appen korrekt förutsäger frånvaron av mål avsikten/entiteten.

## <a name="true-positive"></a>Sant positivt (TP)

I [batch-testning](luis-interactive-test.md#batch-testing)representerar data punkterna yttranden som din app korrekt förutsäger förekomsten av mål avsikt/entitet.

## <a name="utterance"></a>Uttryck

Ett uttryck är ett naturligt språk, exempelvis ”boken 2 tickets till Seattle nästa tisdag”. Exempel yttranden läggs till i avsikten.

## <a name="version"></a>2.0.1

En LUIS- [version](luis-how-to-manage-versions.md) är en speciell data modell som är associerad med ett Luis app-ID och den publicerade slut punkten. Varje LUIS-app har minst en version.
