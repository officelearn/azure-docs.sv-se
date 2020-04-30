---
title: Ord lista – LUIS
titleSuffix: Azure Cognitive Services
description: Ord listan förklarar termer som du kan stöta på när du arbetar med LUIS-API-tjänsten.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: f764b0d42e08e68b45d49a5aae8542f05707ccde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82099367"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Ord lista för språk förståelse för vanliga ord listor och koncept
Ord listan Language Understanding (LUIS) förklarar termer som du kan stöta på när du arbetar med LUIS-API-tjänsten.

## <a name="active-version"></a><a name="active-version"></a>Aktiv version

Den aktiva LUIS-versionen är den version som tar emot ändringar i modellen. Om du vill göra ändringar i en version som inte är den aktiva versionen i [Luis](luis-reference-regions.md) -portalen måste du först ange den versionen som aktiv.

## <a name="authoring"></a><a name="authoring"></a>Redigering

Redigering är möjligheten att skapa, hantera och distribuera en Luis- [app](#luis-app), antingen med hjälp av [Luis](luis-reference-regions.md) -portalen eller [redigerings-API: er](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a><a name="authoring-key"></a>Redigerings nyckel

Tidigare med namnet "programmatisk" nyckel. Används för att redigera appen. Används inte för slut punkts frågor på produktions nivå. Mer information finns i [nyckel gränser](luis-limits.md#key-limits).

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>Batch test-JSON-fil

Batch-testning är möjligheten att verifiera en aktuell LUIS-Apps modell med en konsekvent och känd test uppsättning av användar-yttranden. Batch-testet definieras i en [JSON-formaterad fil](luis-concept-batch-test.md#batch-file-format).

Se även:
* [Begrepp](luis-concept-batch-test.md)
* [Anvisningar](luis-how-to-batch-test.md)
* [Självstudier](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Medarbetare

En medarbetare/deltagare, är inte [ägare](#owner) till appen, men har samma behörighet för att lägga till, redigera och ta bort avsikter, entiteter, yttranden.

## <a name="contributor"></a><a name="contributor"></a>Deltagare

En deltagare är samma sak som en [medarbetare](#collaborator).

## <a name="descriptor"></a><a name="descriptor"></a>Script

En beskrivning är en [funktion](#features) som tillämpas på en modell i inlärnings tid, inklusive [fras listor](#phrase-list) och [entiteter](#entity).

## <a name="domain"></a><a name="domain"></a>Domain

I LUIS-kontexten är en **domän** ett kunskaps området. Din domän är bara unik för din app i kunskaps fältet. Detta kan vara ett allmänt, till exempel appen rese agent. En app för rese agenter kan också vara särskilt unik för bara informations områdena för ditt företag, till exempel vissa geografiska platser, språk och tjänster.

## <a name="endpoint"></a><a name="endpoint"></a>Endpoint

[Luis-slutpunktens](https://go.microsoft.com/fwlink/?linkid=2092356) URL är den plats där du skickar Luis frågor när [appen Luis](#luis-app) har skapats och publicerats. Slut punkts-URL: en innehåller regionen för den publicerade appen samt app-ID. Du hittar slut punkten på sidan **[nycklar och slut punkter](luis-how-to-azure-subscription.md)** i din app, eller så kan du hämta slut punkts-URL: en från [Hämta app information](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API.

## <a name="entity"></a><a name="entity"></a>Entitet

[Entiteter](luis-concept-entity-types.md) är viktiga ord i [yttranden](luis-concept-utterance.md) som beskriver information som är relevant för [avsikten](luis-concept-intent.md)och som ibland är nödvändig för IT. En entitet är i grunden en datatype i LUIS.

## <a name="f-measure"></a><a name="f-measure"></a>F-mått

I [batch-testning](luis-interactive-test.md#batch-testing)är det ett mått på testets noggrannhet.

## <a name="false-negative-fn"></a><a name="false-negative"></a>Falskt negativt (FN)

I [batch-testning](luis-interactive-test.md#batch-testing)representerar data punkterna yttranden där appen felaktigt förutsäger frånvaron av mål avsikten/entiteten.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Falskt positivt (RP)

I [batch-testning](luis-interactive-test.md#batch-testing)representerar data punkterna yttranden där appen felaktigt förutsäger förekomsten av mål avsikten/entiteten.

## <a name="features"></a><a name="features"></a>Funktioner

I Machine Learning är en [funktion](luis-concept-feature.md) en särskiljande egenskaps-eller dataattribut som systemet iakttar.

## <a name="intent"></a><a name="intent"></a>Avsikt

En [avsikt](luis-concept-intent.md) representerar en uppgift eller åtgärd som användaren vill utföra. Det är ett syfte eller mål som uttrycks i en användares indata, t. ex. att boka en flygning, betala en faktura eller hitta en nyhets artikel. I LUIS baseras avsikts förutsägelsen på hela uttryck. Entiteter, efter jämförelse, är delar av en uttryck.

## <a name="labeling"></a><a name="labeling"></a>Märkning

Etikettering, eller markering, är en process för att associera ett ord eller en fras i en avsikts [uttryck](#utterance) med en [entitet](#entity) (datatyp).

## <a name="luis-app"></a><a name="luis-app"></a>LUIS-app

En LUIS-app är en samling språk modeller för bearbetning av naturligt språk, inklusive [avsikter](#intent), [entiteter](#entity)och etiketterade [yttranden](#utterance).

## <a name="owner"></a><a name="owner"></a>Ägare

Varje app har en ägare som är den person som skapade appen. Ägaren kan lägga till [medarbetare](#collaborator).

## <a name="patterns"></a><a name="pattern"></a>Mönster
Den föregående mönster funktionen ersätts med [mönster](luis-concept-patterns.md). Använd mönster för att förbättra förutsägelse noggrannhet genom att tillhandahålla färre inlärnings exempel.

## <a name="phrase-list"></a><a name="phrase-list"></a>Fras lista

En [fras lista](luis-concept-feature.md) innehåller en grupp med värden (ord eller fraser) som tillhör samma klass och som måste behandlas på samma sätt (t. ex. namn på städer eller produkter). En utbytbar lista behandlas som synonymer.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Fördefinierad domän

En [fördefinierad domän](luis-how-to-use-prebuilt-domains.md) är en Luis-app som kon figurer ATS för en speciell domän, till exempel Home Automation (HomeAutomation) eller restaurang reservationer (RestaurantReservation). Avsikter, yttranden och entiteter har kon figurer ATS för den här domänen.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Fördefinierad entitet

En [fördefinierad entitet](luis-prebuilt-entities.md) är en entitets Luis som innehåller vanliga typer av information som t. ex. nummer, URL och e-post. Du väljer att lägga till en fördefinierad entitet i ditt program.

## <a name="precision"></a><a name="precision"></a>Tillräcklig
I [batch-testning](luis-interactive-test.md#batch-testing)är precision (även kallat positivt förutsägande värde) den andel av relevanta yttranden bland de hämtade yttranden.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Programmerings nyckel

Har bytt namn till [redigerings nyckel](#authoring-key).

## <a name="publish"></a><a name="publish"></a>Publicera

Publicering innebär att göra en LUIS aktiv-version tillgänglig antingen för mellanlagrings-eller produktions [slut punkten](#endpoint).

## <a name="quota"></a><a name="quota"></a>Kvot

LUIS-kvoten är begränsningen för [Azure-prenumerations nivån](https://aka.ms/luis-price-tier). LUIS-kvoten kan begränsas av både begär Anden per sekund (HTTP-status 429) och totalt antal förfrågningar per månad (HTTP-status 403).

## <a name="recall"></a><a name="recall"></a>Återkalla
I [batch-testning](luis-interactive-test.md#batch-testing)kan du återkalla (även kallat känslighet) för att Luis ska kunna generaliseras.

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Semantisk ord lista
En semantisk ord lista finns på sidan lista entitet och sidan fras. Den semantiska ord listan innehåller förslag på ord baserade på det aktuella omfånget.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Attitydanalys
Sentiment-analys ger positiva eller negativa värden för yttranden som tillhandahålls av [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a><a name="speech-priming"></a>Tal Prima

Med tal Prima kan din röst tjänst definieras med din LUIS-modell.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Stavnings korrigering

Aktivera stavnings kontroll i Bing för att korrigera felstavade ord i yttranden innan förutsägelse.

## <a name="starter-key"></a><a name="starter-key"></a>Start nyckel

En kostnads fri nyckel som ska användas när den först börjar med LUIS.

## <a name="structure"></a><a name="structure"></a>Struktur

Lägg till strukturen till en enhet som har registrerats av enheten för att tillhandahålla del komponenter med beskrivningar (funktioner) och begränsningar (reguljära uttryck eller List enheter).

## <a name="subscription-key"></a><a name="subscription-key"></a>Prenumerationsnyckel

Prenumerations nyckeln är den **förutsägelse slut punkt** nyckel som är kopplad till den Luis-tjänst [som du skapade i Azure](luis-how-to-azure-subscription.md). Den här nyckeln är inte [redigerings nyckel](#programmatic-key). Om du har en slut punkts nyckel bör den användas för alla slut punkts begär anden i stället för redigerings nyckeln. Du kan se den aktuella slut punkts nyckeln i slut punkts-URL: en längst ned på [sidan **nycklar och slut punkter** ](luis-how-to-azure-subscription.md) på [Luis](luis-reference-regions.md) webbplats. Det är värdet för namn/värde-paret för **prenumerations nyckel** .

## <a name="test"></a><a name="test"></a>Testa

Att [testa](luis-interactive-test.md#test-your-app) en Luis app innebär att skicka en uttryck till Luis och Visa JSON-resultatet.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>Förskjutning av tidszon

Slut punkten innehåller timezoneOffset. Detta är det tal i minuter som du vill lägga till eller ta bort från datetimeV2-fördefinierad entitet. Om uttryck till exempel är "vilken tid är den nu?" returneras den aktuella tiden för klient förfrågan. Om din klientbegäran kommer från en robot eller ett annat program som inte är samma som din robots användare, bör du överföra förskjutningen mellan roboten och användaren.

Se [ändra tidszon för en fördefinierad datetimeV2-entitet](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a><a name="token"></a>Token
En token är den minsta enhet som kan märkas i en entitet. Tokenisering baseras på programmets [kultur](luis-language-support.md#tokenization).

## <a name="train"></a><a name="train"></a>Träna

Träning är en process för att lära sig LUIS om eventuella ändringar i den aktiva versionen sedan den senaste utbildningen.

## <a name="true-negative-tn"></a><a name="true-negative"></a>Sant negativt (TN)

I [batch-testning](luis-interactive-test.md#batch-testing)representerar data punkterna yttranden där appen korrekt förutsäger frånvaron av mål avsikten/entiteten.

## <a name="true-positive-tp"></a><a name="true-positive"></a>Sant positivt (TP)

I [batch-testning](luis-interactive-test.md#batch-testing)representerar data punkterna yttranden som din app korrekt förutsäger förekomsten av mål avsikt/entitet.

## <a name="utterance"></a><a name="utterance"></a>Yttrande

En uttryck är en fras för naturliga språk, till exempel "bok 2 biljetter till Seattle nästa tisdag". Exempel på yttranden läggs till i avsikten.

## <a name="version"></a><a name="version"></a>2.0.1

En LUIS- [version](luis-how-to-manage-versions.md) är en speciell data modell som är associerad med ett Luis app-ID och den publicerade slut punkten. Varje LUIS-app har minst en version.
