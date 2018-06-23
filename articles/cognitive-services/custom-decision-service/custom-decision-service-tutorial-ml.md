---
title: Machine Learning - kognitiva Azure-tjänster | Microsoft Docs
description: En självstudiekurs för machine learning i Azure anpassad beslut Service är en molnbaserad API för kontextuella beslutsfattande.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh
ms.openlocfilehash: 50814d67ee39c6657954610358462d877843416e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354597"
---
# <a name="machine-learning"></a>Maskininlärning

Den här självstudiekursen adresser avancerade funktioner i anpassade beslut Service för maskininlärning. Kursen består av två delar: [featurization](#featurization-concepts-and-implementation) och [funktion specifikationen](#feature-specification-format-and-apis). Featurization avser som representerar dina data efter ”-funktioner för machine learning. Funktionen specifikation omfattar JSON-format och tillhörande-API: er för att ange funktioner.

Som standard är maskininlärning i anpassade beslut Service transparent för kunden. Funktioner extraheras automatiskt från ditt innehåll och en standard förstärkning inlärningsalgoritm används. Funktionen extrahering utnyttjar flera andra Azure kognitiva tjänster: [entitet länka](../entitylinking/home.md), [textanalys](../text-analytics/overview.md), [Känslo](../emotion/home.md), och [datorn Vision](../computer-vision/home.md). Den här kursen kan hoppas över om endast standardfunktionerna används.

## <a name="featurization-concepts-and-implementation"></a>Featurization: koncept och genomförande

Anpassade beslut Service fattar beslut i taget. Varje beslut innebär att välja mellan flera alternativ, kallas även, åtgärder. Beroende på programmet, kan du välja en åtgärd eller en (korta) rankningslista över åtgärder beslutet.

Till exempel anpassa valet av artiklar på sidan klient till en webbplats. Här, åtgärder motsvarar artiklar och varje beslut är vilka artiklar för att visa att en viss användare.

Varje åtgärd representeras av en vector av egenskaper, hädanefter kallas *funktioner*. Du kan ange nya funktioner, förutom de funktioner som extraheras automatiskt. Du kan också instruera anpassad beslut-tjänsten för att logga vissa funktioner, men ignoreras för machine learning.

### <a name="native-vs-internal-features"></a>Intern kontra interna funktioner

Du kan ange funktioner i ett format som är mest naturliga för ditt program, är det en siffra, en sträng eller en matris. Dessa funktioner kallas ”native-funktioner. Anpassade beslut Service översätter varje inbyggd funktion till en eller flera numeriska funktioner, kallas ”interna-funktioner.

Översättning till interna funktioner är följande:

- numeriska funktioner förblir densamma.
- en matris innebär att flera numeriska funktioner, ett för varje element i matrisen.
- en funktion med strängvärden `"Name":"Value"` är som standard översättas till en funktion med namnet `"NameValue"` och värdet 1.
- Du kan också en sträng kan representeras som [egenskapsuppsättning av ord](https://en.wikipedia.org/wiki/Bag-of-words_model). En intern funktion skapas för varje ord i strängen, vars värde är antalet förekomster av ordet.
- Noll-värden, interna funktioner har uteslutits.

### <a name="shared-vs-action-dependent-features"></a>Delad kontra åtgärd beroende funktioner

Vissa funktioner finns i hela beslutet och är samma för alla åtgärder. Vi kallar dem *Delade funktioner*. Vissa andra funktioner som är specifika för en viss åtgärd. Vi kallar dem *åtgärd beroende funktioner* (AD FS).

I exemplet körs kunde Delade funktioner beskrivs användaren och/eller tillståndet i världen. Funktioner som geoplats, ålder och kön för användaren och där viktiga händelser som sker just nu. Åtgärd-beroende funktioner kan beskrivs egenskaperna för en viss artikel, till exempel ämnen som omfattas av den här artikeln.

### <a name="interacting-features"></a>Interagerar funktioner

Funktioner ofta ”interagera”: effekten av en som är beroende av andra. Funktionen X är till exempel om användaren är intresserad av sport. Funktionen Y är om en viss artikeln handlar om sport. Sedan är effekten av funktionen Y mycket beroende av funktionen X.

För att kontot för samverkan mellan funktioner X och Y, skapa en *kvadratisk* funktionen vars värde är X\*Y. (Vi också exempelvis ”mellan” X och Y.) Du kan välja vilka par av funktioner passeras.

> [!TIP]
> En funktion som ska passeras med åtgärden beroende funktioner för att påverka deras rang. En åtgärd beroende funktion bör passeras med delade funktioner för att bidra till anpassning.

Med andra ord påverkar en delad funktion inte uppnås med någon ADFs varje åtgärd på samma sätt. En ADM inte uppnås med en delad funktion påverkar för varje beslut. Dessa typer av funktioner som kan minska variansen för ersättning uppskattningar.

### <a name="implementation-via-namespaces"></a>Implementeringen via namnområden

Du kan implementera korsade funktioner (och även andra featurization begrepp) via ”VW kommandoraden” på portalen. Syntaxen baseras på den [Vowpal Wabbit](http://hunch.net/~vw/) kommandoraden.

Central genomförandet är begreppet *namnområde*: en namngiven uppsättning funktioner. Varje funktion som hör till ett namnområde. Namnområdet kan anges explicit när funktionen värde har angetts för anpassade beslut Service. Det är det enda sättet att referera till en funktion i kommandoraden VW.

Ett namnområde är ”delade” eller ”action-beroende”: det består endast av delade funktioner eller består endast av åtgärden beroende funktioner i samma åtgärd.

> [!TIP]
> Det är en bra idé att omsluta funktioner i explicit angiven namnområden. Gruppera relaterade funktioner i samma namnområde.

Om namnområdet inte är anges, tilldelas funktionen automatiskt standardnamnområde.

> [!IMPORTANT]
> Funktioner och namnområden behöver inte vara konsekvent över åtgärder. I synnerhet kan ett namnområde ha olika funktioner för olika åtgärder. Dessutom kan ett visst namnområde definieras för vissa åtgärder och inte för vissa andra.

Flera interna funktioner som kommer från samma strängvärden inbyggda funktioner grupperas i samma namnområde. Två inbyggda funktioner som ligger i olika namnområden behandlas som distinkta, även om de har samma funktion namn.

> [!IMPORTANT]
> När långa, beskrivande namnområde-ID: N är vanliga skiljer kommandoraden VW inte namnområden vars id börjar med samma bokstav. I vilka sätt namnområde ID: N är enstaka bokstäver som `x` och `y`.

Implementeringsdetaljerna är följande:

- Att passera namnområden `x` och `y`, skriva `-q xy` eller `--quadratic xy`. Sedan varje funktion i `x` skärs med varje funktion i `y`. Använd `-q x:` korsar `x` med varje namnområde och `-q ::` till mellan alla par namnområden.

- Ignorera alla funktioner i namnområdet `x`, skriva `--ignore x`.

Dessa kommandon som tillämpas på varje åtgärd separat, när namnområden som har definierats.

### <a name="estimated-average-as-a-feature"></a>Beräknad genomsnittlig som en funktion

Vad skulle vara Genomsnittlig ersättning för en viss åtgärd om den har valts för alla beslut som en idé experiment? Sådana genomsnittliga trafik kan användas som ett mått på ”övergripande kvaliteten” på den här åtgärden. Det är inte känt exakt när andra åtgärder har valts i stället för i vissa beslut. De kan dock beräknas via förstärkning maskininlärningstekniker. Kvaliteten på denna uppskattning förbättrar vanligtvis över tid.

Du kan välja att inkludera den här ”beräknad genomsnittlig ersättning” som en funktion för en viss åtgärd. Anpassad beslut tjänsten skulle sedan automatiskt uppdatera denna uppskattning som nya data tas emot. Den här funktionen kallas den *marginell funktionen* för denna åtgärd. Marginell funktioner kan användas för machine learning och för granskning.

Om du vill lägga till marginell funktioner, skriva `--marginal <namespace>` på kommandoraden VW. Definiera `<namespace>` i JSON på följande sätt:

```json
{<namespace>: {"mf_name":1 "action_id":1}
```

Infoga det här namnområdet tillsammans med andra åtgärd beroende funktioner i en viss åtgärd. Ange den här definitionen för varje beslut som använder samma `mf_name` och `action_id` för alla beslut.

Funktionen marginell har lagts till för varje åtgärd med `<namespace>`. Den `action_id` kan vara vilket namn som funktionen som unikt identifierar åtgärden. Funktionsnamnet har angetts till `mf_name`. I synnerhet marginal funktioner med olika `mf_name` behandlas som olika funktioner, en annan vikt lärs för varje `mf_name`.

Standardanvändning är att `mf_name` är densamma för alla åtgärder. Sedan lärs en vikt för alla marginell funktioner.

Du kan också ange flera marginell funktioner för samma åtgärd med samma värden men olika funktionsnamn.

```json
{<namespace>: {"mf_name1":1 "action_id":1 "mf_name2":1 "action_id":1}}
```

### <a name="1-hot-encoding"></a>1-hot kodning

Du kan välja att visa vissa funktioner som bitars angreppsmetoderna, där varje bit motsvarar en uppsättning möjliga värden. Den här biten anges till 1 om funktionen ligger i det här intervallet. Därför är det en ”heta” bit som har angetts till 1 och andra som är inställda på 0. Detta kallas även *hot 1-kodning*.

1-hot kodningen är typiska för kategoriska funktioner, till exempel ”geografisk region” som inte har en natur meningsfulla numerisk representation. Det är lämpligt för numeriska funktioner vars inverkan på trafik som kan förväntas vara icke-linjär. En viss artikel kan till exempel vara relevanta för en viss grupp ålder och irrelevanta för alla äldre eller små.

Alla strängvärden funktionen är 1-hot som standard kodade: en distinkta interna funktion skapas för varje möjligt värde. Automatisk hot 1-kodning för numeriska funktioner och/eller med anpassade områden tillhandahålls inte för närvarande.

> [!TIP]
> Maskininlärningsalgoritmer behandlar alla möjliga värden för en viss interna funktion på ett enhetligt sätt: via en gemensam ”vikt”. 1-hot kodning kan en separat ”vikt” för varje värdeintervallet. Att göra intervall mindre leder till att bättre utdelningen tillräckligt med data samlas in, men du kan öka mängden data som behövs för att Konvergera att bättre utdelningen.

## <a name="feature-specification-format-and-apis"></a>Funktionen specifikationen: format och API: er

Du kan ange funktioner via flera underordnade API: er. Alla API: er använda ett gemensamt JSON-format. Nedan visas de API: er och format på en grundläggande nivå. Specifikationen kompletteras av en Swagger-schemat.

Grundläggande JSON-mall för funktionen är följande:

```json
{
"<name>":<value>, "<name>":<value>, ... ,
"namespace1": {"<name>":<value>, ... },
"namespace2": {"<name>":<value>, ... },
...
}
```

Här `<name>` och `<value>` betyder för funktionsnamnet och funktionen värde respektive. `<value>` kan vara en sträng, ett heltal, ett flyttal, ett booleskt värde eller en matris. En funktion som inte har wrappats till ett namnområde tilldelas automatiskt till standardnamnområde.

Använd en särskild syntax för att representera en sträng som en egenskapsuppsättning-för-ord `"_text":"string"` i stället för `"<name>":<value>`. Effektivt, skapas en separat internt funktion för varje ord i strängen. Dess värde är antalet förekomster av ordet.

Om `<name>` börjar med ”_” (och inte `"_text"`), och sedan funktionen ignoreras.

> [!TIP]
> Ibland kan du koppla funktioner från flera källor i JSON. För enkelhetens skull kan du representera dem på följande sätt:
>
> ```json
> {
> "source1":<features>,
> "source2":<features>,
> ...
> }
> ```

Här `<features>` refererar till grundläggande funktioner specifikationen som definierats tidigare. Djupare kapslingsnivåer ”” tillåts för. Anpassade beslut automatiskt hittas ”djupaste” JSON-objekt som kan tolkas som `<features>`.

#### <a name="feature-set-api"></a>Funktionen uppsättning API

Funktionen Ange API: N returnerar en lista över funktioner i JSON-format som beskrivs ovan. Du kan använda flera funktionen Ange API-slutpunkter. Varje slutpunkt identifieras av funktionen set-id och en URL. Mappningen mellan funktionen ID: N och URL: er har angetts på portalen.

Anropa API för funktionen anges genom att lägga till motsvarande set-id för funktionen på lämplig plats i JSON. För åtgärden beroende funktioner parametriserade anropet automatiskt av åtgärds-id. Du kan ange flera funktionsuppsättning ID: N för samma åtgärd.

#### <a name="action-set-api-json-version"></a>Åtgärden Ange API (JSON-version)

Åtgärden Ange API har en version som åtgärder och funktioner som har angetts i JSON. Funktioner kan anges explicit och/eller via funktionen Ange API: er. Delade funktioner kan anges en gång för alla åtgärder.

#### <a name="ranking-api-http-post-call"></a>Rangordning API (HTTP POST-anrop)

Rangordning API har en version som använder HTTP POST-anrop. Innehållet i det här anropet anger åtgärder och funktioner via en flexibel JSON-syntax.

Åtgärder kan anges explicit eller via åtgärden Ange ID: N. Ett anrop till motsvarande åtgärden Ange API-slutpunkten körs när en åtgärd set-id har påträffats.

För åtgärden Ange API anges funktioner explicit och/eller via funktionen Ange API: er. Delade funktioner kan anges en gång för alla åtgärder.