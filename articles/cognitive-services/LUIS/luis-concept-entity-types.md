---
title: Förstå entitetstyper i THOMAS appar i Azure | Microsoft Docs
description: Lägg till enheter (viktiga data i ditt program domän) i språk förstå Intelligent Service (THOMAS) appar.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/22/2018
ms.author: v-geberr
ms.openlocfilehash: 918f5d9efa1163558e44c2c67028dbf802f479a5
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266795"
---
# <a name="entities-in-luis"></a>Entiteter i THOMAS

Entiteter är ord eller fraser i utterances som är viktiga data i ditt program domän.

## <a name="entity-compared-to-intent"></a>Entiteten jämfört med avsikt
Entiteten representerar ett ord eller en fras i utterance som du vill extraherade. En utterance kan inkludera många entiteter eller ingen alls. En enhet representerar en klass, inklusive en samling liknande objekt (platser, saker, personer, händelser eller begrepp). Entiteter beskriver information som är relevanta för avsikten och ibland de är viktiga för din app att utföra sina uppgifter. En News Search-app kan till exempel innehålla entiteter, till exempel ”avsnittet”, ”källa”, ”nyckelordet” och ”publiceringsdatum”, som är viktiga data att söka efter nyheter. En resa boka app, ”plats”, ”dag”, ”flygbolag”, ”resa klass” och ”biljetter” är viktig information för svarta boka (relevant att avsikten ”Bookflight”).

Avsikten representerar jämförelse förutsägelser av hela utterance. 

## <a name="entities-represent-data"></a>Entiteter representerar data
Entiteter är data som du vill hämta från utterance. Detta kan vara ett namn, datum, produktnamn eller en grupp med ord. 

|Utterance|Entitet|Data|
|--|--|--|
|Köp 3 biljetter New York|Fördefinierade tal<br>Location.Destination|3<br>New York|
|Köp en biljett från New York London 5 mars|Location.Origin<br>Location.Destination<br>Fördefinierade datetimeV2|New York<br>London<br>5 mars 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Entiteter är valfritt men rekommenderas
Medan avsikter krävs är entiteter valfria. Du behöver inte skapa entiteter för varje begrepp i din app, men endast för krävs för appen att vidta åtgärder. 

Du behöver inte lägga till dem om din utterances saknar information din bot måste fortsätta. Då appen utvecklas, kan du lägga till dem senare. 

Om du inte är säker på hur du använder informationen lägga till några vanliga färdiga entiteter, till exempel datetimeV2, ordningstal e-post och telefonnummer.

## <a name="label-for-word-meaning"></a>Etikett för word betydelse
Om word val eller word placering är detsamma, men inte betyda samma sak, inte etiketten med entiteten. 

Följande utterances, ordet `fair` är en homografiska. Det är stavat samma men har en annan betydelse:

```
What kind of county fairs are happening in the Seattle area this summer?
Is the current rating for the Seattle review fair?
```

Om du vill att en händelse entitet att hitta alla händelsedata etikett ordet `fair` i den första utterance, men inte i andra.

## <a name="entities-are-shared-across-intents"></a>Enheter som är gemensamma för avsikter
Enheter som ska delas mellan avsikter. De tillhör inte någon enskild avsikt. Intents och entiteter kan associeras semantiskt men det är inte en exklusiv relation.

I utterance ”bok mig en biljett till Paris”, ”Paris” är en entitet av typen plats. Genom att fokusera på de enheter som nämns i användarens indata, hjälper THOMAS dig att välja vilka specifika åtgärder för att uppfylla syftet.

## <a name="assign-entities-in-none-intent"></a>Tilldela enheter inget avsiktshantering
Alla avsikter, inklusive den **ingen** avsikt, bör ha entiteter med etiketten. Detta hjälper THOMAS mer information om var enheterna i utterances och ord är runt enheterna. 

## <a name="types-of-entities"></a>Typer av enheter
THOMAS erbjuder många typer av enheter. fördefinierade entiteter, anpassade datorn lärt dig entiteter och listan entiteter.

| Namn | Kan etikett | Beskrivning |
| -- |--|--|
| **Fördefinierade** <br/>[Anpassad](#prebuilt)| |  **Definition**<br>Inbyggda typer som representerar vanliga koncept. <br><br>**lista**<br/>viktiga frasen antal, ordningstal, temperatur, dimension, money, ålder, procent, e-post, URL, telefonnummer och viktiga frasen. <br><br>Fördefinierade entitetsnamn är reserverade. <br><br>Alla färdiga entiteter som har lagts till i programmet returneras i den [endpoint](luis-glossary.md#endpoint) frågan. Mer information finns i [färdiga entiteter](./Pre-builtEntities.md). <br/><br/>[Exempelsvar för entiteten](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **Reguljärt uttryck**<br/>[RegEx](#regex)||**Definition**<br>Anpassat reguljärt uttryck för formaterad text. Det är inte skiftlägeskänslig och ignorerar kulturell variant.  <br><br>Den här entiteten är bra för ord eller fraser som formateras konsekvent med alla variationer som också är konsekvent.<br><br>Matchning med reguljära uttryck tillämpas efter stavningen ändringar. <br><br>Om det reguljära uttrycket är för komplex, till exempel med många hakparenteser, går det inte att lägga till uttrycket i modellen. <br><br>**Exempel**<br>`kb[0-9]{6,}` matchar kb123456.<br/><br/>[Snabbstart](luis-quickstart-intents-regex-entity.md)<br>[Exempelsvar för entiteten](luis-concept-data-extraction.md)|
| **Enkel** <br/>[Datorn lärt dig](#machine-learned) | ✔ | **Definition**<br>En enkel enhet är en generisk entitet som beskriver ett enda koncept och lärs från datorn lärt dig kontext. Kontexten innehåller word val, word placering och utterance längd.<br/><br/>Det här är en bra entitet för ord eller fraser som inte är konsekvent formaterade men anger samma sak. <br/><br/>[Snabbstart](luis-quickstart-primary-and-secondary-data.md)<br/>[Exempelsvar för entiteten](luis-concept-data-extraction.md#simple-entity-data)|  
| **lista** <br/>[Exakt matchning](#exact-match)|| **Definition**<br>Listan entiteter representerar en fast, stängd uppsättning relaterade ord tillsammans med deras synoymns i systemet. <br><br>Varje entitet i listan kan ha ett eller flera formulär. Bäst för en känd uppsättning av varianter på olika sätt att representera samma begrepp.<br/><br/>THOMAS identifierar inte ytterligare värden för entiteter i listan. Använd den att se [semantiska ordlista](luis-glossary.md#semantic-dictionary) att få förslag på nya ord baserat på den aktuella listan.<br/><br>Om det finns fler än en entitet i listan med samma värde, returneras varje entitet i frågan slutpunkt. <br/><br/>[Snabbstart](luis-quickstart-intent-and-list-entity.md)<br>[Exempelsvar för entiteten](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[Blandat](#mixed) | ✔|**Definition**<br>Patterns.any är en platshållare för variabel längd som används endast i ett mönster mallen utterance Markera där entiteten börjar och slutar.  <br><br>**Exempel**<br>Pattern.any extraherar angivna utterance letar books baserat på titel fullständig rubrik. En mall utterance med pattern.any är `Who wrote {BookTitle}[?]`.<br/><br/>[Självstudie](luis-tutorial-pattern.md)<br>[Exempelsvar för entiteten](luis-concept-data-extraction.md#composite-entity-data)|  
| **Sammansatt** <br/>[Datorn lärt dig](#machine-learned) | ✔|**Definition**<br>En sammansatt entitet består av andra entiteter, till exempel färdiga entiteter och enkel. Separata entiteterna utgör helhet. Lista över enheter är inte tillåtna i sammansatta entiteter. <br><br>**Exempel**<br>En sammansatt entitet med namnet PlaneTicketOrder kan ha underordnade entiteter färdiga `number` och `ToLocation`. <br/><br/>[Självstudie](luis-tutorial-composite-entity.md)<br>[Exempelsvar för entiteten](luis-concept-data-extraction.md#composite-entity-data)|  
| **Hierarkisk** <br/>[Datorn lärt dig](#machine-learned) |✔ | **Definition**<br>En hierarkisk entitet är en kategori av sammanhang inlärda entiteter.<br><br>**Exempel**<br>Få en hierarkisk entiteten av `Location` med underordnade `ToLocation` och `FromLocation`, varje underordnad kan bestämmas utifrån den **kontexten** inom utterance. I utterance `Book 2 tickets from Seattle to New York`, `ToLocation` och `FromLocation` sammanhang skiljer baserat ord runtom. <br/><br/>**Använd inte om**<br>Om du letar efter en entitet som har exakt matchar för underordnade oavsett kontexten bör du använda en enhet i listan. Om du letar efter en överordnad-underordnad relation med andra typer av enheter, bör du använda sammansatta entiteten.<br/><br/>[Snabbstart](luis-quickstart-intent-and-hier-entity.md)<br>[Exempelsvar för entiteten](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
**Fördefinierade** enheter är anpassade enheter som tillhandahålls av THOMAS. Vissa av dessa enheter har definierats i öppen källkod [identifierare Text](https://github.com/Microsoft/Recognizers-Text) projekt. Det finns många [exempel](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs) i katalogen /Specs för stöds kulturer. Om din specifik kultur eller entitet inte stöds för närvarande, bidra till projektet. 

<a name="machine-learned"></a>
**Datorn lärt dig** enheter fungerar bäst när testas [endpoint frågor](luis-concept-test.md#endpoint-testing) och [granska endpoint utterances](label-suggested-utterances.md). 

<a name="regex"></a>
**Reguljärt uttryck entiteter** definieras av ett reguljärt uttryck som användaren anger som en del av entitetsdefinitionen. 

<a name="exact-match"></a>
**Exakt matchning** entiteter använda texten i entiteten textformatering en exakt matchar.

<a name="mixed"></a>
**Blandat** entiteterna används en kombination av entiteten identifieringsmetoder.

## <a name="entity-limits"></a>Entiteten gränser
Granska [gränser](luis-boundaries.md#model-boundaries) att förstå hur många av varje typ av enhet kan du lägga till en modell.

## <a name="composite-vs-hierarchical-entities"></a>Sammansatta vs hierarkiska entiteter
Sammansatta entiteter och hierarkiska entiteter både ha överordnade och underordnade relationer och är datorn lärt dig. Maskininlärning kan THOMAS att förstå enheter baserat på olika kontexter (placering av ord). Sammansatta entiteter är mer flexibel eftersom de låter olika enhetstyper som underordnade. En hierarkisk entitet underordnade är endast enkla enheter. 

|Typ|Syfte|Exempel|
|--|--|--|
|Hierarkisk|Överordnad-underordnad av enkla enheter|Location.Origin=New York<br>Location.Destination=London|
|Sammansatt|Överordnad-underordnad-entiteter: fördefinierad, lista enkla, hierarkisk| Antal = 3<br>lista = första klass<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>Data som matchar flera entiteter
Om ett ord eller en fras matchar fler än en entitet, returnerar endpoint-frågan varje entitet. Om du lägger till både fördefinierade nummer entiteten och prebuild datetimeV2 och har en utterance `create meeting on 2018/03/12 for lunch with wayne`, THOMAS känner igen alla entiteter och returnerar en matris med enheter som en del av JSON-slutpunkt-svar: 

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
Om ett ord eller en fras matchar fler än en entitet i listan, returnerar endpoint-frågan varje entitet i listan.

För frågan `when is the best time to go to red rock?`, och har appen ordet `red` i mer än en THOMAS identifierar alla entiteter och returnerar en matris med enheter som en del av JSON-slutpunkt-svar: 

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

Du kan behöva använda hierarkiska och sammansatta entiteter. Hierarkisk entiteter avspegla relationen mellan entiteter som har egenskaper eller som är medlemmar i en kategori. Underordnade enheter är alla medlemmar i deras överordnade kategori. En hierarkisk entitet med namnet PlaneTicketClass kan till exempel ha underordnade enheter EconomyClass och bästa. Hierarkin omfattar bara en nivå med djup.  

Sammansatta entiteter representerar delar av en helhet. En sammansatt entitet med namnet PlaneTicketOrder kan till exempel ha underordnade enheter flygbolag, mål, DepartureCity, DepartureDate och PlaneTicketClass. Du skapar en sammansatt entitet från befintliga enkla enheter underordnade hierarkiska entiteter eller färdiga entiteter.  

THOMAS ger också listtypen entitet som inte har registrerats för datorn, men ger THOMAS appen för att ange en fast lista med värden. Se [THOMAS gränser](luis-boundaries.md) referens till granska gränserna för listan entitetstypen. 

Kontakta supporten om du har tänkt hierarkisk, sammansatta, och visa en lista med enheter och behöver mer än gränsen. Om du vill göra det, samla in detaljerad information om datorn, gå till den [THOMAS] [ LUIS] webbplats och väljer sedan **Support**. Om din Azure-prenumeration innehåller supporttjänster, kontakta [Azure teknisk support](https://azure.microsoft.com/support/options/). 

## <a name="best-practices"></a>Bästa praxis

Skapa en [entiteten](luis-concept-entity-types.md) när det anropande programmet eller bot måste vissa parametrar eller data från utterance som krävs för att utföra en åtgärd. En entitet är ett ord eller fraser i utterance som du behöver extraherade--kanske som en parameter för en funktion. 

Du behöver veta hur data skrivs av användare för att välja rätt typ av enhet för att lägga till i ditt program. Varje typ av enhet hittas med hjälp av en annan mekanism, till exempel machine learning, stängda listan eller det reguljära uttrycket. Om du är osäker på börja med en enkel enhet och märka ett ord eller en fras som representerar att data i alla utterances över alla avsikter inklusive ingen avsiktshantering.  

Granska endpoint utterances regelbundet vill söka efter vanliga användning där en entitet kan identifieras som ett reguljärt uttryck eller med en exakt matchning hittades.  

Som en del av granskningen kan du överväga att lägga till en fras lista om du vill lägga till en signal till THOMAS för ord eller fraser som är viktiga för din domän, men är inte exakt matchar och som THOMAS inte ha en hög säkerhetsnivå.  

Se [metodtips](luis-concept-best-practices.md) för mer information.

## <a name="next-steps"></a>Nästa steg

Läs begrepp om bra [utterances](luis-concept-utterance.md). 

Se [lägga till enheter](luis-how-to-add-entities.md) lära dig mer om hur du lägger till entiteter i appen THOMAS.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website