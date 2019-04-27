---
title: 'Självstudier: Funktionalisering och funktionsspecifikation – Custom Decision Service'
titlesuffix: Azure Cognitive Services
description: Självstudie om funktionalisering och funktionsspecifikation för maskininlärning i Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: 9e091d3899132509d16854ebdbe14bcbc491deec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829148"
---
# <a name="tutorial-featurization-and-feature-specification"></a>Självstudier: Funktionalisering och funktionsspecifikation

I den här självstudien behandlas funktioner för avancerade maskininlärning i Custom Decision Service. Kursen består av två delar: [funktionalisering](#featurization-concepts-and-implementation) och [funktionsspecifikation](#feature-specification-format-and-apis). Funktionalisering innebär att representera dina data som ”funktioner” för maskininlärning. Funktionsspecifikation omfattar JSON-format och extra API:er för att ange funktioner.

Som standard är maskininlärning i Custom Decision Service transparent för kunden. Funktioner extraheras automatiskt från innehållet och en standardalgoritm för förstärkt inlärning används. Funktionsextrahering utnyttjar flera andra Azure Cognitive Services: [Entitetslänkning](../entitylinking/home.md), [Textanalys](../text-analytics/overview.md), [Känsloigenkänning](../emotion/home.md) och [Visuellt innehåll](../computer-vision/home.md). Du kan hoppa över den här kursen om du bara använder standardfunktionerna.

## <a name="featurization-concepts-and-implementation"></a>Funktionalisering: begrepp och implementering

Custom Decision Service fattar ett beslut i taget. Varje beslut inbegriper att välja bland flera olika alternativ, så kallade åtgärder. Beroende på program kan beslutet välja en enstaka åtgärd eller en (kort) rangordnad lista med åtgärder.

Exempel: anpassa valet av artiklar på startsidan för en webbplats. Här motsvarar åtgärder artiklar och varje beslut är vilka artiklar som ska visas för en viss användare.

Varje åtgärd representeras av en vektor med egenskaper som hädanefter kallas *funktioner*. Du kan nu ange nya funktioner, utöver de funktioner som extraheras automatiskt. Du kan även instruera Custom Decision Service att logga några funktioner, men ignorera dem för maskininlärning.

### <a name="native-vs-internal-features"></a>Inbyggda jämfört med interna funktioner

Du kan ange funktioner i ett format som är mest naturligt för programmet, oavsett om det är ett tal, en sträng eller en matris. Dessa funktioner kallas för ”inbyggda funktioner”. Custom Decision Service översätter varje inbyggd funktion till en eller flera numeriska funktioner, som kallas för ”interna funktioner”.

Översättningen till interna funktioner görs enligt följande:

- numeriska funktioner förändras inte
- en numerisk matris översätts till flera numeriska funktioner, en för varje element av matrisen
- en funktion med strängvärden, `"Name":"Value"`, översätts som standard till en funktion med namnet `"NameValue"` och värdet 1.
- En sträng kan också representeras som en uppsättning ord, ”[bag-of-words](https://en.wikipedia.org/wiki/Bag-of-words_model)”. En intern funktions skapas sedan för varje ord i strängen, vars värde är antalet förekomster av ordet.
- Interna funktioner med nollvärde utelämnas.

### <a name="shared-vs-action-dependent-features"></a>Delade jämfört med åtgärdsberoende funktioner

Vissa funktioner hänvisar till hela beslutet och är samma för alla åtgärder. Dessa kallas för *delade funktioner*. Vissa andra funktioner är specifika för en viss åtgärd. De kallas för *åtgärdsberoende funktioner*.

I exemplet kan delade funktioner beskriva användaren och/eller tillståndet i världen. Funktioner som användarens geoplats, ålder och kön, och vilka stora händelser som sker just nu. Åtgärdsberoende funktioner kan beskriva egenskaperna för en viss artikel, till exempel de ämnen som artikeln tar upp.

### <a name="interacting-features"></a>Interagerande funktioner

Funktioner interagerar ofta: effekten av en funktion beror på andra funktioner. Exempel: funktion x är om användaren är intresserad av sport. Funktion Y är om en viss artikel handlar om sport. Effekten av funktion är då mycket beroende av funktion X.

För att redovisa interaktionen mellan funktion X och Y skapar du en *kvadratisk* funktion vars värde är X\*Y. (Vi säger också korsa X och Y.) Du kan välja vilka par av funktioner som korsas.

> [!TIP]
> En delad funktion ska korsas med åtgärdsberoende funktioner för att påverka deras rangordning. En åtgärdsberoende funktion ska korsas med delade funktioner för att kunna bidra till anpassning.

Med andra ord: en delad funktion som inte korsas med några åtgärdsberoende funktioner påverkar varje åtgärd på samma sätt. En åtgärdsberoende funktion som inte korsas med någon delad funktion påverkar också varje beslut. Dessa typer av funktioner kan minska variansen för beräkningar av effekter.

### <a name="implementation-via-namespaces"></a>Implementering via namnrymder

Du kan implementera korsade funktioner (samt andra funktionaliseringsbegrepp) via ”VW-kommandoraden” i portalen. Syntaxen baseras på [Vowpal Wabbit](http://hunch.net/~vw/)-kommandoraden.

Centralt för implementeringen är begreppet *namnrymd*: en namngiven delmängd funktioner. Varje funktion tillhör exakt en namnrymd. Namnrymden kan anges uttryckligen när funktionsvärdet anges för Custom Decision Service. Det är det enda sättet at referera till en funktion på VW-kommandoraden.

En namnrymd är ”delad” eller ”åtgärdsberoende”: antingen består den bara av delade funktioner eller så består den bara av åtgärdsberoende funktioner av samma åtgärd.

> [!TIP]
> Det är en bra idé att omsluta funktioner i uttryckligen angivna namnrymder. Grupprelaterade funktioner i samma namnrymd.

Om namnrymden inte anges tilldelas funktionen automatiskt till standardnamnrymden.

> [!IMPORTANT]
> Funktioner och namnrymder behöver inte vara konsekventa över åtgärder. I synnerhet kan en namnrymd ha olika funktioner för olika åtgärder. Dessutom kan en viss namnrymd definieras för vissa åtgärder och inte definieras för andra.

Flera interna funktioner som kommer från samma inbyggda funktion med strängvärden grupperas i samma namnrymd. Två inbyggda funktioner som finns i olika namnrymder behandlas som distinkta, även om de har samma funktionsnamn.

> [!IMPORTANT]
> Långa, beskrivande namnrymds-ID:n är vanliga med VW-kommandoraden skiljer inte mellan namnrymder vars ID börjar på samma bokstav. Vidare är namnrymds-ID:n enstaka bokstäver, till exempel `x` och `y`.

Implementeringsinformationen är följande:

- Om du vill korsa namnrymd `x` och `y` skriver du `-q xy` eller `--quadratic xy`. Då korsas varje funktion i `x` med varje funktion i `y`. Använd `-q x:` för att korsa `x` med varje namnrymd och `-q ::` för att korsa alla par med namnrymder.

- Om du vill ignorera alla funktioner i namnrymd `x` skriver du `--ignore x`.

Dessa kommandon tillämpas på varje åtgärd separat, när namnrymderna definieras.

### <a name="estimated-average-as-a-feature"></a>Beräknat medelvärde som en funktion

Som tankeexperiment kan vi fråga oss: Vad skulle den genomsnittliga effekten av en åtgärd vara om den skulle väljas för alla beslut? Sådan genomsnittlig effekt kan användas som ett mått på ”total kvalitet” för den här åtgärden. Det är inte känt exakt när andra åtgärder har valts i stället i vissa beslut. Men det kan beräknas via metoder för förstärkt inlärning. Kvaliteten för denna beräkning förbättras med tiden.

Du kan välja att inkludera ”beräknad genomsnittlig effekt” som en funktion för en viss åtgärd. Sedan skulle Custom Decision Service automatiskt uppdatera denna beräkning då nya data anländer. Denna funktion kallas för *marginalfunktion* för denna åtgärd. Marginalfunktioner kan användas för maskininlärning och för granskning.

Skriv `--marginal <namespace>` på VW-kommandoraden om du vill lägga till marginalfunktioner. Definiera `<namespace>` i JSON så här:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Infoga den här namnrymden med andra åtgärdsberoende funktioner för en viss åtgärd. Ange den här definition för varje beslut, med samma `mf_name` och `action_id` för alla beslut.

Marginalfunktionen läggs till för varje åtgärd med `<namespace>`. `action_id` kan vara ett funktionsnamn som unikt identifierar åtgärden. Funktionsnamnet är inställt på `mf_name`. Särskilt behandlas marginalfunktioner med olika `mf_name` som olika funktioner – en annan vikt lärs in för varje `mf_name`.

Standardinställningen är att `mf_name` är samma för alla åtgärder. Sedan lärs en vikt in för alla marginalfunktioner.

Du kan även ange flera marginalfunktioner för samma åtgärd, med samma värden men olika funktionsnamn.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>1-hot-kodning

Du kan välja att representera vissa funktioner som bitvektorer, där varje bit motsvarar ett intervall med möjliga värden. Den här biten anges till 1 om och bara om funktionen ligger inom det här intervallet. Det finns alltså en ”het” som anges till 1 och den andra som anges till 0. Den här representationen kallas *1-hot-kodning*.

1-hot-kodningen är typisk för kategorifunktioner som ”geografiskt område” som inte har en inneboende meningsfull numerisk representation. Det rekommenderas även för numeriska funktioner vars inverkan på effekten troligen blir icke-linjär. Till exempel kan en viss artikel bara relevant för en viss åldersgrupp och irrelevant för alla äldre eller yngre.

Alla funktioner med strängvärden är 1-hot-kodade som standard: en distinkt intern funktion skapas för varje möjligt värde. Automatisk 1-hot-kodning för numeriska funktioner och/eller med anpassade intervall ges inte för närvarande.

> [!TIP]
> Maskininlärningsalgoritmerna behandlar alla möjliga värden för en viss intern funktion på samma sätt: via en gemensam ”vikt”. 1-hot-kodningen tillåter en separat ”vikt” för varje värdeintervall. Att göra intervallen mindre leder till bättre effekter när tillräckligt mycket data samlas in men kan öka mängden data som behövs för att konvergera till bättre effekter.

## <a name="feature-specification-format-and-apis"></a>Funktionsspecifikation: format och API:er

Du kan ange funktioner via flera extra API:er. Alla API:er använder ett vanligt JSON-format. Nedanför är API:erna och formaten på konceptuell nivå. Specification kompletteras av ett Swagger-schema.

Den grundläggande JSON-mallen för funktionsspecifikationen är följande:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

Här står `<name>` och `<value>` för funktionsnamn respektive funktionsvärde. `<value>` kan vara en sträng, ett heltal, ett flyttal, ett booleskt värde eller en matris. En funktion som inte är omsluten i en namnrymd tilldelas automatiskt till standardnamnrymden.

Om du vill representera en sträng som en ”bag-of-words” använder du en särskild syntax `"_text":"string"` i stället för `"<name>":<value>`. I själva verket skapas en intern funktion för varje ord i strängen. Dess värde är antalet förekomster av det här ordet.

Om `<name>` börjar med ”_” (och inte är `"_text"`) så ignoreras funktionen.

> [!TIP]
> Ibland slår du samman funktioner från flera JSON-källor. För enkelhetens skull kan du representera dem på följande sätt:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

Här refererar `<features>` till den grundläggande funktionsspecifikationen som definierats tidigare. Djupare nivåer av kapsling tillåts också. Custom Decision Service hittar automatiskt de ”djupaste” JSON-objekten som kan tolkas som `<features>`.

#### <a name="feature-set-api"></a>Funktionsuppsättnings-API

Funktionsuppsättnings-API returnerar en lista i det JSON-format som beskrivs ovan. Du kan använda flera slutpunkter för funktionsuppsättnings-API. Varje slutpunkt identifieras av funktionsuppsättnings-ID och en URL. Mappningen mellan funktionsuppsättnings-ID:n och URL:er anges i portalen.

Anropa funktionsuppsättnings-API genom att infoga motsvarande funktionsuppsättnings-ID på rätt plats i JSON. För åtgärdsberoende funktioner parametriseras anropet automatiskt av åtgärds-ID:t. Du kan ange flera funktionsuppsättnings-ID:n för samma åtgärd.

#### <a name="action-set-api-json-version"></a>Åtgärdsuppsättnings-API (JSON-version)

Åtgärdsuppsättnings-API har en version där åtgärder och funktioner anges i JSON. Funktioner kan anges uttryckligen och/eller via API:er för funktionsuppsättning. Delade funktioner kan anges en gång för alla åtgärder.

#### <a name="ranking-api-http-post-call"></a>Rangordnings-API (HTTP POST-anrop)

Rangordnings-API har en version som använder HTTP POST-anrop. Brödtexten i det här anropet anger åtgärder och funktioner via en flexibel JSON-syntax.

Åtgärder kan anges uttryckligen och/eller via åtgärdsuppsättnings-ID:n. När en åtgärdsuppsättnings-ID påträffas utförs ett anrop till motsvarande slutpunkt för åtgärdsuppsättnings-API.

Som för åtgärdsuppsättnings-API kan anges uttryckligen och/eller via funktionsuppsättnings-API:er. Delade funktioner kan anges en gång för alla åtgärder.
