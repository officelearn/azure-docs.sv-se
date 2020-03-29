---
title: Ordlista - LUIS
titleSuffix: Azure Cognitive Services
description: Ordlistan förklarar termer som du kan stöta på när du arbetar med LUIS API-tjänsten.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220956"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Ordlista för språkförståelse med gemensamt ordförråd och begrepp
I LUIS-ordlistan (Language Understanding) förklaras termer som du kan stöta på när du arbetar med LUIS API-tjänsten.

## <a name="active-version"></a><a name="active-version"></a>Aktiv version

Den aktiva LUIS-versionen är den version som tar emot eventuella ändringar i modellen. Om [LUIS](luis-reference-regions.md) du vill göra ändringar i en version som inte är den aktiva versionen i LUIS-portalen måste du först ange den versionen som aktiv.

## <a name="authoring"></a><a name="authoring"></a>Redigering

Redigering är möjligheten att skapa, hantera och distribuera en [LUIS-app](#luis-app), antingen med [LUIS-portalen](luis-reference-regions.md) eller [redigerings-API:erna](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a><a name="authoring-key"></a>Redigeringsnyckel

Tidigare kallad "Programmatic" nyckel. Används för att skapa appen. Används inte för slutpunktsfrågor på produktionsnivå. Mer information finns i [Nyckelgränser](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>Batchtest JSON-fil

Batchtestning är möjligheten att validera en aktuell LUIS-apps modell med en konsekvent och känd testuppsättning av användaryttranden. Batchtestet definieras i en [JSON-formaterad fil](luis-concept-batch-test.md#batch-file-format).

Se även:
* [Begrepp](luis-concept-batch-test.md)
* [Anvisningar](luis-how-to-batch-test.md)
* [Självstudier](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Medarbetare

En medarbetare/deltagare, är inte [ägare](#owner) till appen, men har samma behörighet att lägga till, redigera och ta bort avsikter, entiteter, yttranden.

## <a name="contributor"></a><a name="contributor"></a>Deltagare

En bidragsgivare är samma sak som en [medarbetare](#collaborator).

## <a name="descriptor"></a><a name="descriptor"></a>Deskriptor

En deskriptor är en [funktion](#features) som tillämpas på en modell vid utbildning, inklusive [fraslistor](#phrase-list) och [entiteter](#entity). 

## <a name="domain"></a><a name="domain"></a>Domain

I LUIS-kontexten är en **domän** ett kunskapsområde. Din domän är specifik för ditt appområde. Detta kan vara ett allmänt område som resebyråappen. En reseagentapp kan också vara specifik för bara informationsområden för ditt företag, till exempel specifika geografiska platser, språk och tjänster.

## <a name="endpoint"></a><a name="endpoint"></a>Slutpunkt

[LUIS-slutpunkts-URL:en](https://go.microsoft.com/fwlink/?linkid=2092356) är den plats där du skickar LUIS-frågor efter att [LUIS-appen](#luis-app) har skapats och publicerats. Slutpunkts-URL:en innehåller regionen för den publicerade appen samt app-ID:et. Du kan hitta slutpunkten på sidan **[Nycklar och slutpunkter](luis-how-to-azure-subscription.md)** i din app, eller så kan du hämta slutpunkts-URL:en från [Api:et hämta appinformation.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37)

## <a name="entity"></a><a name="entity"></a>Entitet

[Entiteter](luis-concept-entity-types.md) är viktiga ord i [yttranden](luis-concept-utterance.md) som beskriver information som är relevant för [avsikten](luis-concept-intent.md), och ibland är de nödvändiga för den. En entitet är i huvudsak en datatyp i LUIS.

## <a name="f-measure"></a><a name="f-measure"></a>F-åtgärd

Vid [batchtestning](luis-interactive-test.md#batch-testing), ett mått på testets noggrannhet.

## <a name="false-negative-fn"></a><a name="false-negative"></a>Falskt negativt (FN)

I [batchtestning](luis-interactive-test.md#batch-testing)representerar datapunkterna yttranden där appen felaktigt förutspådde frånvaron av målavsikten/entiteten.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Falskt positivt (FP)

I [batchtestning](luis-interactive-test.md#batch-testing)representerar datapunkterna yttranden där din app felaktigt förutspådde förekomsten av målavsikten/entiteten.

## <a name="features"></a><a name="features"></a>Funktioner

I maskininlärning är en [funktion](luis-concept-feature.md) ett särskiljande drag eller attribut för data som ditt system observerar.

## <a name="intent"></a><a name="intent"></a>Avsikt

En [avsikt](luis-concept-intent.md) representerar en uppgift eller åtgärd som användaren vill utföra. Det är ett syfte eller mål som uttrycks i en användares indata, till exempel att boka en flygning, betala en faktura eller hitta en nyhetsartikel. I LUIS baseras avsiktsförutsägelsen på hela uttrycket. Entiteter, som jämförelse, är bitar av ett uttryck.

## <a name="labeling"></a><a name="labeling"></a>Märkning

Märkning, eller märkning, är processen att associera ett ord eller en fras i en avsikts [yttrande](#utterance) med en [entitet](#entity) (datatyp).

## <a name="luis-app"></a><a name="luis-app"></a>LUIS-appen

En LUIS-app är en samling språkmodeller för bearbetning av naturligt språk, inklusive [avsikter,](#intent) [entiteter](#entity)och [märkta yttranden](#utterance).

## <a name="owner"></a><a name="owner"></a>Ägare

Varje app har en ägare som är den person som skapade appen. Ägaren kan lägga till [medarbetare](#collaborator).

## <a name="patterns"></a><a name="pattern"></a>Mönster
Den tidigare mönsterfunktionen [ersätts](luis-concept-patterns.md)med Mönster . Använd mönster för att förbättra förutsägelsenoggrannheten genom att ge färre träningsexempel.

## <a name="phrase-list"></a><a name="phrase-list"></a>Fraslista

En [fraslista](luis-concept-feature.md) innehåller en grupp värden (ord eller fraser) som tillhör samma klass och som måste behandlas på samma sätt (till exempel namn på städer eller produkter). En utbytbar lista behandlas som synonymer.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Fördefinierad domän

En [fördefinierad domän](luis-how-to-use-prebuilt-domains.md) är en LUIS-app som konfigurerats för en viss domän, till exempel hemautomation (HomeAutomation) eller restaurangbokningar (RestaurantReservation). Avsikter, yttranden och entiteter är konfigurerade för den här domänen.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Fördefinierad enhet

En [fördefinierad entitet](luis-prebuilt-entities.md) är en entitet LUIS tillhandahåller vanliga typer av information som nummer, URL och e-post. Du väljer att lägga till en fördefinierad entitet i ditt program.

## <a name="precision"></a><a name="precision"></a>Precision
I [batchtestning](luis-interactive-test.md#batch-testing)är precision (även kallat positivt prediktivt värde) den del av relevanta yttranden bland de hämtade yttrandena.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Programmatisk nyckel

Bytt namn till [redigeringsnyckel](#authoring-key).

## <a name="publish"></a><a name="publish"></a>Publicera

Publicering innebär att göra en LUIS-aktiv version tillgänglig på antingen mellanlagrings- eller [produktionsslutpunkten](#endpoint).  

## <a name="quota"></a><a name="quota"></a>Kvot

LUIS-kvoten är begränsningen av [Azure-prenumerationsnivån](https://aka.ms/luis-price-tier). LUIS-kvoten kan begränsas av både begäranden per sekund (HTTP Status 429) och totala begäranden under en månad (HTTP-status 403).

## <a name="recall"></a><a name="recall"></a>Återkalla
I [batchtestning](luis-interactive-test.md#batch-testing), återkallande (kallas även känslighet), är möjligheten för LUIS att generalisera.

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Semantisk ordlista
En semantisk ordlista finns på sidan Listahet samt på sidan Fraslista. Den semantiska ordlistan innehåller förslag på ord baserat på det aktuella omfånget.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Attitydanalys
Sentimentanalys ger positiva eller negativa värden för de yttranden som tillhandahålls av [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a><a name="speech-priming"></a>Tal priming

Med talpriming kan din taltjänst förberedas med din LUIS-modell.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Stavningskorrigering

Aktivera Stavningskontroll av Bing för att korrigera felstavade ord i uttryck före förutsägelse.

## <a name="starter-key"></a><a name="starter-key"></a>Startnyckel

En ledig nyckel att använda när du först börjar använda LUIS.

## <a name="structure"></a><a name="structure"></a>Struktur

Lägg till struktur i en datorinlärd entitet för att tillhandahålla underkomponenter med beskrivningar (funktioner) och begränsningar (reguljära uttryck eller listentiteter).

## <a name="subscription-key"></a><a name="subscription-key"></a>Prenumerationsnyckel

Prenumerationsnyckeln är **den slutpunktsnyckel för förutsägelse som** är associerad med LUIS-tjänsten som du skapade i [Azure](luis-how-to-azure-subscription.md). Den här nyckeln är inte [redigeringsnyckeln](#programmatic-key). Om du har en slutpunktsnyckel bör den användas för alla slutpunktsbegäranden i stället för redigeringsnyckeln. Du kan se din aktuella slutpunktsnyckel i slutpunktsadressen längst ned på sidan [ **Nycklar och slutpunkter** ](luis-how-to-azure-subscription.md) på [LUIS-webbplatsen.](luis-reference-regions.md) Det är värdet av **prenumerationsnyckelnamn/värdepar.**

## <a name="test"></a><a name="test"></a>Test

[Att testa](luis-interactive-test.md#test-your-app) en LUIS-app innebär att skicka ett uttryck till LUIS och visa JSON-resultaten.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>Tidszon förskjutning

Slutpunkten innehåller tidszonoffset. Det här är numret i minuter som du vill lägga till eller ta bort från den fördefinierade entiteten datetimeV2. Om uttrycket till exempel är "vilken tid är det nu?", är datetimeV2 som returneras den aktuella tiden för klientbegäran. Om din klientbegäran kommer från en bot eller annat program som inte är samma som din bot användare, bör du passera i förskjutningen mellan bot och användaren.

Se [Ändra tidszon för fördefinierad datetimeV2-entitet](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a><a name="token"></a>Token
En token är den minsta enhet som kan märkas i en entitet. Tokenisering baseras på programmets [kultur](luis-language-support.md#tokenization).

## <a name="train"></a><a name="train"></a>Tåg

Utbildning är processen att lära LUIS om eventuella ändringar i den aktiva versionen sedan den senaste utbildningen.

## <a name="true-negative-tn"></a><a name="true-negative"></a>Sant negativt (TN)

I [batchtestning](luis-interactive-test.md#batch-testing)representerar datapunkterna yttranden där appen korrekt förutspådde frånvaron av målavsikten/entiteten.

## <a name="true-positive-tp"></a><a name="true-positive"></a>Sant positivt (TP)

I [batchtestning](luis-interactive-test.md#batch-testing)representerar datapunkterna yttranden där appen korrekt förutspådde förekomsten av målavsikten/entiteten.

## <a name="utterance"></a><a name="utterance"></a>Yttrande

Ett yttrande är ett naturligt språk fras som "boka 2 biljetter till Seattle nästa tisdag". Exempelyttranden läggs till i avsikten.

## <a name="version"></a><a name="version"></a>Version

En [LUIS-version](luis-how-to-manage-versions.md) är en specifik datamodell som är associerad med ett LUIS-app-ID och den publicerade slutpunkten. Varje LUIS-app har minst en version.
