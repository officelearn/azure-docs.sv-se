---
title: Förstå entitetstyper i LUIS-appar i Azure | Microsoft Docs
description: Lägg till entiteter (viktiga data i ditt programs domän) i appar för Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/28/2018
ms.author: diberry
ms.openlocfilehash: ace4aa48d3bfce5f88bce8947ab568f0990d67fa
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226619"
---
# <a name="entities-in-luis"></a>Entiteter i LUIS

Entiteter är ord eller fraser i yttranden som är viktiga data i ditt programs domän.

## <a name="entity-compared-to-intent"></a>Entiteten jämfört med avsikt
Entiteten representerar ett ord eller en fras i uttryck som du vill extrahera. Ett uttryck kan innehålla många entiteter eller inga alls. En entitet representerar en klass, inklusive en samling liknande objekt (platser, saker, personer, händelser eller begrepp). Entiteter beskriver information som är relevanta för avsikten och ibland de är viktiga för din app för att utföra sitt uppdrag. En nyhetssökning app kan exempelvis innehålla entiteter, till exempel ”ämne”, ”källa”, ”nyckelordet” och ”publiceringsdatum”, som är viktiga data till Sök efter nyheter. Är viktig information för flygning bokning (relevant med ”Bookflight” intentionen) i en app för bokning av resor, ”plats”, ”datum”, ”flygbolag”, ”resa class” och ”biljetter”.

Jämförelsevis har representerar avsikten förutsägelser av hela uttryck. 

## <a name="entities-represent-data"></a>Entiteter som representerar data
Entiteter är data som du vill hämta från uttryck. Detta kan vara ett namn, datum, produktnamn och grupper av orden. 

|Yttrande|Entitet|Data|
|--|--|--|
|Köpa 3 biljetter New York|Fördefinierade tal<br>Location.Destination|3<br>New York|
|Köp en biljett från New York till London 5 mars|Location.Origin<br>Location.Destination<br>Fördefinierade datetimeV2|New York<br>London<br>5 mars 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Entiteter är valfritt men rekommenderas
Avsikter är obligatoriska, är entiteter valfria. Du behöver inte skapa entiteter för varje begrepp i din app, men endast för de som krävs för appen att vidta åtgärder. 

Om din yttranden inte har information om din robot måste fortsätta, behöver du inte att lägga till dem. När din app utvecklas, kan du lägga till dem senare. 

Om du inte är säker på hur du använder informationen du lägga till några vanliga förskapade entiteter, till exempel datetimeV2, ordningstal, e-post och telefonnummer.

## <a name="label-for-word-meaning"></a>Etikett för word betydelse
Om word val eller word placering är samma, men inte betyda samma sak, du inte märker den med entiteten. 

Följande uttryck, ordet `fair` är en homografiska. Det har stavats samma, men har en annan betydelse:

```
What kind of county fairs are happening in the Seattle area this summer?
Is the current rating for the Seattle review fair?
```

Om du vill att en händelse entitet för att hitta alla händelsedata kan etikettera ordet `fair` i den första uttryck, men inte i andra.

## <a name="entities-are-shared-across-intents"></a>Entiteter som är gemensamma för avsikter
Entiteter delas avsikter. De tillhör inte någon enskild avsikt. Avsikter och entiteter kan associeras semantiskt men det är inte en exklusiv relation.

I uttryck ”boka mig en biljett till Paris”, ”Paris” är en entitet av typen plats. Genom att fokusera på de entiteter som nämns i användarens indata, hjälper LUIS dig att välja vilka åtgärder som leder till att uppfylla en avsikt.

## <a name="assign-entities-in-none-intent"></a>Tilldela enheter inget avsikt
Alla avsikter, inklusive den **ingen** avsikt, bör ha entiteter med etiketten. På så sätt kan LUIS mer information om var entiteterna finns i talade och ord är runt entiteterna. 

## <a name="types-of-entities"></a>Typer av enheter
LUIS erbjuder många typer av enheter. färdiga entiteter, anpassad dator lärt dig entiteter och listan över entiteter.

| Namn | Kan etiketten | Beskrivning |
| -- |--|--|
| **Fördefinierade** <br/>[Anpassad](#prebuilt)| |  **Definition**<br>Inbyggda typer som representerar vanliga begrepp. <br><br>**Lista**<br/>Diskussionsämne tal, ordningstal, temperaturen, dimension, pengar, ålder, procent, e-post, URL: en, telefonnummer och diskussionsämne. <br><br>Fördefinierade entitetsnamn är reserverade. <br><br>Alla förskapade entiteter som läggs till programmet returneras i de [endpoint](luis-glossary.md#endpoint) fråga. Mer information finns i [förskapade entiteter](./luis-prebuilt-entities.md). <br/><br/>[Exempel på ett svar för entitet](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **Reguljärt uttryck**<br/>[RegEx](#regex)||**Definition**<br>Anpassat reguljärt uttryck för formaterade raw uttryck text. Det är inte skiftlägeskänslig och ignorerar kulturella variant.  <br><br>Den här entiteten är bra för ord eller fraser som formateras konsekvent med alla variationer som också är konsekvent.<br><br>Matchning med reguljära uttryck tillämpas efter stavningskontroll förändras. <br><br>Om det reguljära uttrycket är för komplex, till exempel med många hakparenteser, går det inte att lägga till uttrycket i modellen. <br><br>**Exempel**<br>`kb[0-9]{6,}` matchar kb123456.<br/><br/>[Snabbstart](luis-quickstart-intents-regex-entity.md)<br>[Exempel på ett svar för entitet](luis-concept-data-extraction.md)|
| **Enkel** <br/>[Datorn lärt dig](#machine-learned) | ✔ | **Definition**<br>En enkel enhet är en allmän entitet som beskriver ett enda koncept och lära sig från datorn lärt dig kontext. Kontexten innehåller word val, word placering och uttryck längd.<br/><br/>Det här är en bra entitet efter ord eller fraser som inte är konsekvent formaterade men visar samma sak. <br/><br/>[Snabbstart](luis-quickstart-primary-and-secondary-data.md)<br/>[Exempel på ett svar för entitet](luis-concept-data-extraction.md#simple-entity-data)|  
| **Lista** <br/>[Exakt matchning](#exact-match)|| **Definition**<br>Lista över entiteter representerar en fast, stängda uppsättning närstående ord tillsammans med deras synoymns i systemet. <br><br>Varje lista entitet kan ha ett eller flera formulär. Bäst för en känd uppsättning av varianter på sätt att representera detsamma.<br/><br/>LUIS identifierar inte ytterligare värden för listan över entiteter. Använd den **rekommenderar** funktionen för att se förslag för nya ord baserat på den aktuella listan.<br/><br>Om det finns mer än en entitet i listan med samma värde, returneras varje entitet i frågan slutpunkt. <br/><br/>[Snabbstart](luis-quickstart-intent-and-list-entity.md)<br>[Exempel på ett svar för entitet](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[Blandat](#mixed) | ✔|**Definition**<br>Patterns.any är en platshållare för variabel längd som används endast i ett mönster mall uttryck för att markera där entiteten börjar och slutar.  <br><br>**Exempel**<br>Med ett uttryck letar böcker baserat på rubriken kan extraherar pattern.any fullständig rubriken. En mall-uttryck med hjälp av pattern.any är `Who wrote {BookTitle}[?]`.<br/><br/>[Självstudie](luis-tutorial-pattern.md)<br>[Exempel på ett svar för entitet](luis-concept-data-extraction.md#composite-entity-data)|  
| **Sammansatta** <br/>[Datorn lärt dig](#machine-learned) | ✔|**Definition**<br>En sammansatt entitet består av andra enheter, till exempel förskapade entiteter, enkel, regex, lista, hierarkiska. Separata entiteter utgör en helhet. Lista över entiteter är inte tillåtna i sammansatt entiteter. <br><br>**Exempel**<br>En sammansatt entitet med namnet PlaneTicketOrder kan ha underordnade entiteter fördefinierade `number` och `ToLocation`. <br/><br/>[Självstudie](luis-tutorial-composite-entity.md)<br>[Exempel på ett svar för entitet](luis-concept-data-extraction.md#composite-entity-data)|  
| **Hierarkisk** <br/>[Datorn lärt dig](#machine-learned) |✔ | **Definition**<br>En hierarkisk entitet är en viss kategori av sammanhangsmässigt inlärda enkla enheter.<br><br>**Exempel**<br>Får en hierarkisk entitet av `Location` med underordnade `ToLocation` och `FromLocation`, varje underordnad kan bestämmas utifrån den **kontext** inom uttryck. I uttryck, `Book 2 tickets from Seattle to New York`, `ToLocation` och `FromLocation` skiljer sig sammanhangsmässigt baserat orden runtom. <br/><br/>**Använd inte om**<br>Om du letar efter en entitet som har exakt denna matchningar för underordnade oavsett kontexten, bör du använda en entitet i listan. Om du letar efter en överordnad-underordnad-relation med andra typer av enheter, bör du använda sammansatta entiteten.<br/><br/>[Snabbstart](luis-quickstart-intent-and-hier-entity.md)<br>[Exempel på ett svar för entitet](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
**Fördefinierade** entiteter är anpassade entiteter som tillhandahålls av LUIS. Vissa av dessa entiteter har definierats i öppen källkod [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text) projekt. Det finns många [exempel](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs) i katalogen /Specs för kulturer som stöds. Om din specifika kultur eller entitet inte stöds för närvarande, bidra till projektet. 

<a name="machine-learned"></a>
**Datorn lärt dig** entiteter fungerar bäst när testas [endpoint frågor](luis-concept-test.md#endpoint-testing) och [granska endpoint yttranden](luis-how-to-review-endoint-utt.md). 

<a name="regex"></a>
**Reguljärt uttryck entiteter** definieras av ett reguljärt uttryck som användaren anger som en del av dess definition. 

<a name="exact-match"></a>
**Exakt matchning** entiteter använda texten i entiteten för att göra en exakt text som matchar.

<a name="mixed"></a>
**Blandade** entiteterna används en kombination av entiteten identifieringsmetoder.

## <a name="entity-limits"></a>Entiteten gränser
Granska [gränser](luis-boundaries.md#model-boundaries) att förstå hur många av varje typ av enhet du kan lägga till en modell.

## <a name="entity-roles"></a>Entiteten roller
Entiteten [roller](luis-concept-roles.md) används i endast mönster. 

## <a name="composite-vs-hierarchical-entities"></a>Sammansatta vs hierarkisk entiteter
Sammansatta entiteter och hierarkisk entiteter både har överordnade och underordnade relationer och är datorn lärt dig. Machine learning kan LUIS för att förstå entiteter baserat på olika kontexter (placering av ord). Sammansatta entiteter är mer flexibla eftersom de låter olika enhetstyper som underordnade. En hierarkisk entitet underordnade är bara enkla enheter. 

|Typ|Syfte|Exempel|
|--|--|--|
|Hierarkisk|Överordnad-underordnad av enkla enheter|Location.Origin=New York<br>Location.Destination=London|
|Sammansatta|Överordnade-underordnade entiteter: fördefinierade, lista, enkel, hierarkisk| Antal = 3<br>lista = första klass<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>Data som matchar flera entiteter
Om ett ord eller fraser matchar mer än en entitet, returnerar endpoint-frågan varje entitet. Om du lägger till både färdiga nummer entitets- och prebuild datetimeV2 och har ett uttryck `create meeting on 2018/03/12 for lunch with wayne`, LUIS identifierar alla entiteter och returnerar en matris med entiteter som en del av svaret för JSON-slutpunkten: 

```JSON
{
  "query": "create meeting on 2018/03/12 for lunch with wayne",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.9333419
  },
  "entities": [
    {
      "entity": "2018/03/12",
      "type": "builtin.datetimeV2.date",
      "startIndex": 18,
      "endIndex": 27,
      "resolution": {
        "values": [
          {
            "timex": "2018-03-12",
            "type": "date",
            "value": "2018-03-12"
          }
        ]
      }
    },
    {
      "entity": "2018",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 21,
      "resolution": {
        "value": "2018"
      }
    },
    {
      "entity": "03/12",
      "type": "builtin.number",
      "startIndex": 23,
      "endIndex": 27,
      "resolution": {
        "value": "0.25"
      }
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Data som matchar flera listan entiteter
Om ett ord eller fraser matchar mer än en entitet i listan, returnerar endpoint-frågan varje entitet i listan.

För frågan `when is the best time to go to red rock?`, och appen har ordet `red` i mer än en LUIS identifierar alla entiteter och returnerar en matris med entiteter som en del av svaret för JSON-slutpunkten: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Om du behöver mer än det maximala antalet enheter 

Du kan behöva använda hierarkisk och sammansatta entiteter. Hierarkisk entiteter visas relation mellan entiteter som har egenskaper eller som är medlemmar i en kategori. Underordnade entiteter är alla medlemmar i deras överordnade kategorin. En hierarkisk entitet med namnet PlaneTicketClass kan till exempel ha underordnade entiteter EconomyClass och bästa. Hierarkin omfattar endast en nivå med djup.  

Sammansatta entiteter representerar delar av en helhet. En sammansatt entitet med namnet PlaneTicketOrder kan till exempel ha underordnade entiteter flygbolag, mål, DepartureCity, DepartureDate och PlaneTicketClass. Du skapar en sammansatt entitet från befintliga enkla enheter, underordnade hierarkisk entiteter eller fördefinierade entiteter.  

LUIS innehåller också listtypen för entiteten som inte dator-lära sig, men tillåter LUIS-app att ange en fast lista med värden. Se [LUIS gränser](luis-boundaries.md) referens till granska gränserna för listtypen för entiteten. 

Om du har tänkt hierarkisk, sammansatta, och lista över entiteter och behöver mer än gränsen, kontakta supporten. Samla in detaljerad information om datorn för att göra det, går du till den [LUIS](luis-reference-regions.md#luis-website) webbplats och väljer sedan **Support**. Om din Azure-prenumeration innehåller supporttjänster, kontakta [teknisk support för Azure](https://azure.microsoft.com/support/options/). 

## <a name="best-practices"></a>Bästa praxis

Skapa en [entitet](luis-concept-entity-types.md) när anropande program / robotar måste vissa parametrar eller data från de uttryck som krävs för att utföra en åtgärd. En entitet är ett ord eller fraser i uttryck som du extraherade--kanske som en parameter för en funktion. 

Du behöver veta hur dessa data har angetts av användare för att välja rätt typ av enhet för att lägga till i ditt program. Varje typ av målentitet hittas med hjälp av en annan mekanism som machine learning, stängda lista eller reguljärt uttryck. Om du är osäker, börja med en enkel enhet och märka ord eller fraser som representerar dessa data i alla yttranden över alla avsikter inklusive ingen avsikt.  

Granska endpoint yttranden regelbundet att hitta vanliga användare där en entitet kan identifieras som ett reguljärt uttryck eller med en exakt denna matchning hittades.  

Som en del av granskningen kan du överväga att lägga till en fras lista för att lägga till en signal LUIS efter ord eller fraser som är relevanta för att din domän utan att vara exakta matchningar och som LUIS inte har en hög exakthet.  

Se [bästa praxis](luis-concept-best-practices.md) för mer information.

## <a name="next-steps"></a>Nästa steg

Lär dig mer begrepp om bra [yttranden](luis-concept-utterance.md). 

Se [Lägg till entiteter](luis-how-to-add-entities.md) mer information om hur du lägger till entiteter i din LUIS-app.