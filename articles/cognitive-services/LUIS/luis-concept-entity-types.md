---
title: Entitetstyper
titleSuffix: Language Understanding - Azure Cognitive Services
description: 'Entiteter extraherar data från uttryck. Entitetstyper ger dig förutsägbar data extrahering. Det finns två typer av entiteter: maskin-och icke-maskin-inlärd. Det är viktigt att veta vilken typ av entitet du arbetar med i yttranden.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: a5a3ba8c25107317e7c47ee358f9a6ebe7d4556f
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479118"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Entitetstyper och deras syfte i LUIS

Entiteter extraherar data från uttryck. Entitetstyper ger dig förutsägbar data extrahering. Det finns två typer av entiteter: maskin-och icke-maskin-inlärd. Det är viktigt att veta vilken typ av entitet du arbetar med i yttranden. 

## <a name="entity-compared-to-intent"></a>Entiteten jämfört med avsikt

Entiteten representerar ett ord eller en fras i uttryck som du vill extrahera. Ett uttryck kan innehålla många entiteter eller inga alls. Ett klient program kan behöva entiteten för att utföra sin uppgift eller använda den som en guide med flera alternativ för att presentera för användaren. 

En entitet:

* Representerar en klass inklusive en samling liknande objekt (platser, saker, personer, händelser eller koncept). 
* Beskriver information som är relevant för avsikten


En nyhetssökning app kan exempelvis innehålla entiteter, till exempel ”ämne”, ”källa”, ”nyckelordet” och ”publiceringsdatum”, som är viktiga data till Sök efter nyheter. I en rese boknings app är "plats", "datum", "flyg", "rese klass" och "biljetter" viktig information för flyg bokning (som är relevant för avsikten "Book Flight").

Jämförelsevis har representerar avsikten förutsägelser av hela uttryck. 

## <a name="entities-help-with-data-extraction-only"></a>Hjälp om entiteter med endast data extrahering

Du kan märka eller markera entiteter enbart för att extrahera entiteter, utan hjälp av avsikts förutsägelse.

## <a name="entities-represent-data"></a>Entiteter som representerar data

Entiteter är data som du vill hämta från uttryck. Detta kan vara ett namn, datum, produktnamn och grupper av orden. 

|Yttrande|Entitet|Data|
|--|--|--|
|Köpa 3 biljetter New York|Fördefinierade tal<br>Location.Destination|3<br>New York|
|Köp en biljett från New York till London 5 mars|Location.Origin<br>Location.Destination<br>Fördefinierade datetimeV2|New York<br>London<br>5 mars 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Entiteter är valfritt men rekommenderas

Avsikter är obligatoriska, är entiteter valfria. Du behöver inte skapa entiteter för varje koncept i appen, men endast för de som krävs för att klient programmet ska vidta åtgärder. 

Om din yttranden inte har information om din robot måste fortsätta, behöver du inte att lägga till dem. När din app utvecklas, kan du lägga till dem senare. 

Om du inte är säker på hur du ska använda informationen lägger du till några vanliga fördefinierade entiteter som [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordnings](luis-reference-prebuilt-ordinal.md)tal, [e-post](luis-reference-prebuilt-email.md)och [telefonnummer](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Etikett för word betydelse

Om word val eller word placering är samma, men inte betyda samma sak, du inte märker den med entiteten. 

Följande uttryck, ordet `fair` är en homografiska. Det har stavats samma, men har en annan betydelse:

|Yttrande|
|--|
|Vilken typ av region mässor sker i Seattle-området den här sommaren?|
|Är den aktuella klassificeringen för Seattle-granskning fair?|

Om du vill att en händelse entitet för att hitta alla händelsedata kan etikettera ordet `fair` i den första uttryck, men inte i andra.

## <a name="entities-are-shared-across-intents"></a>Entiteter som är gemensamma för avsikter

Entiteter delas avsikter. De tillhör inte någon enskild avsikt. Avsikter och entiteter kan semantiskt associeras, men det är inte en exklusiv relation.

I uttryck "bok mig a-biljett till Paris" är "Paris" en entitet som refererar till platsen. Genom att känna igen de entiteter som nämns i användarens uttryck, hjälper LUIS ditt klient program att välja de specifika åtgärder som ska vidtas för att uppfylla användarens begäran.

## <a name="mark-entities-in-none-intent"></a>Markera entiteter i ingen avsikt

Alla avsikter, inklusive **ingen** avsikt, bör ha markerat entiteter när det är möjligt. På så sätt kan LUIS mer information om var entiteterna finns i talade och ord är runt entiteterna. 

## <a name="entity-status-for-predictions"></a>Status för enheter för förutsägelser

LUIS-portalen visar när entiteten i ett exempel uttryck är antingen en annan än den markerade entiteten eller är för nära en annan entitet och därför oklar. Detta anges med en röd understrykning i exemplet uttryck. 

Mer information finns i [enhets status förutsägelser](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Typer av enheter

LUIS erbjuder många typer av entiteter. Välj entiteten baserat på hur data ska extraheras och hur de ska representeras när den har extraherats.

Entiteter kan extraheras med Machine Learning, vilket gör att LUIS kan fortsätta att lära sig om hur entiteten visas i uttryck. Entiteter kan extraheras utan maskin inlärning, som matchar antingen exakt text eller ett reguljärt uttryck. Entiteter i mönster kan extraheras med en blandad implementering. 

När entiteten har extraherats kan enhets data representeras som en enskild enhet med information eller kombineras med andra entiteter för att bilda en informations enhet som klient programmet kan använda.

|Machine-learned|Kan markera|Självstudie|Exempel<br>Svar|Entitetstyp|Syfte|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Sammansättning**](#composite-entity)|Gruppering av entiteter, oavsett enhets typ.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**Lista**](#list-entity)|Lista över objekt och deras synonymer som extraheras med exakt text matchning.|
|Blandad||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Mönster. alla**](#patternany-entity)|Entiteten där slutet av entiteten är svårt att fastställa.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Fördefinierade**](#prebuilt-entity)|Redan utbildad för att extrahera olika typer av data.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Reguljärt uttryck**](#regular-expression-entity)|Använder reguljärt uttryck för att matcha text.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Gång**](#simple-entity)|Innehåller ett enda begrepp i ord eller fras.|

Endast enheter som har registrerats av enheten måste markeras i exemplet yttranden. Enheter som har lärts fungerar bäst när de testas via [slut punkts frågor](luis-concept-test.md#endpoint-testing) och [granskar slut punkts yttranden](luis-how-to-review-endoint-utt.md). 

Mönster. alla entiteter måste markeras i exempel på [mönster](luis-how-to-model-intent-pattern.md) mal len, inte avsikten med användar exempel. 

Blandade entiteter använder en kombination av entitetens identifierings metoder.

## <a name="machine-learned-entities-use-context"></a>Enhet som har lärts in enheten använder kontext

Enheter som lärts från enheten lär sig från kontexten i uttryck. På så sätt blir variationen av placering i exemplet yttranden stor. 

## <a name="non-machine-learned-entities-dont-use-context"></a>Entiteter som inte är från en dator använder inte kontext

Följande förhandlade entiteter som inte är från en dator tar inte uttryck i beaktande vid matchning av entiteter: 

* [Fördefinierade entiteter](#prebuilt-entity)
* [Regex-entiteter](#regular-expression-entity)
* [Lista över entiteter](#list-entity) 

Dessa entiteter kräver inte etiketter eller tränar modellen. När du har lagt till eller konfigurerat entiteten extraheras entiteterna. Kompromissen är att dessa entiteter kan matchas, där om kontexten har beaktats, skulle matchningen inte ha gjorts. 

Detta händer med list enheter i nya modeller ofta. Du skapar och testar din modell med en lista entitet, men när du publicerar din modell och tar emot frågor från slut punkten, inser du att din modell är övermatchad på grund av bristande kontext. 

Om du vill matcha ord eller fraser och ta hänsyn till det kan du välja mellan två alternativ. Det första är att använda en enkel entitet som är länkad till en fras lista. Fras listan används inte för matchning, utan hjälper istället att signalera relativt liknande ord (utbytbar lista). Om du måste ha en exakt matchning i stället för en fras listas variationer använder du en List-entitet med en roll, som beskrivs nedan.

### <a name="context-with-non-machine-learned-entities"></a>Kontext med icke-belärt entiteter

Om du vill att kontexten för uttryck ska vara beroende av entiteter som inte kommer från en annan dator bör du använda [roller](luis-concept-roles.md).

Om du har en icke-enhets medveten entitet, till exempel [färdiga entiteter](#prebuilt-entity), [regex](#regular-expression-entity) -entiteter eller [list](#list-entity) enheter, som matchar den instans du vill använda, kan du överväga att skapa en entitet med två roller. En roll fångar vad du letar efter och en roll fångar vad du inte söker efter. Båda versionerna måste etiketteras i exempel yttranden.  

## <a name="composite-entity"></a>Sammansatt entitet

En [sammansatt entitet](reference-entity-composite.md) består av andra entiteter, till exempel färdiga entiteter, enkla, reguljära uttryck och list enheter. Separata entiteter utgör en helhet. 

## <a name="list-entity"></a>Lista entitet

[Lista entiteter](reference-entity-list.md) representerar en fast, avslutad uppsättning relaterade ord tillsammans med deras synonymer. LUIS identifierar inte ytterligare värden för listan över entiteter. Använd den **rekommenderar** funktionen för att se förslag för nya ord baserat på den aktuella listan. Om det finns mer än en entitet i listan med samma värde, returneras varje entitet i frågan slutpunkt. 

## <a name="patternany-entity"></a>Entiteten Pattern.any

[Mönster.](reference-entity-pattern-any.md) det finns en plats hållare med variabel längd som bara används i ett mönsters mall uttryck för att markera var entiteten börjar och slutar.  

## <a name="prebuilt-entity"></a>Fördefinierade entitet

Fördefinierade entiteter är inbyggda typer som representerar vanliga koncept som e-post, URL och telefonnummer. Fördefinierade entitetsnamn är reserverade. [Alla färdiga entiteter](luis-prebuilt-entities.md) som läggs till i programmet returneras i slut punkts förutsägelse frågan om de hittas i uttryck. 

Entiteten passar bra när:

* Data matchar ett vanligt användnings fall som stöds av fördefinierade entiteter för din språk kultur. 

Fördefinierade entiteter kan läggas till och tas bort när som helst.

![Antal fördefinierade entitet](./media/luis-concept-entities/number-entity.png)

[Självstudie](luis-tutorial-prebuilt-intents-entities.md)<br>
[Exempel på JSON-svar för entitet](luis-concept-data-extraction.md#prebuilt-entity-data)

Några av dessa fördefinierade entiteter definieras i ett [text](https://github.com/Microsoft/Recognizers-Text) projekt med öppen källkod. Om din specifika kultur eller entitet inte stöds för närvarande, bidra till projektet. 

### <a name="troubleshooting-prebuilt-entities"></a>Felsöka fördefinierade entiteter

I LUIS-portalen, om en fördefinierad entitet är taggad i stället för din anpassade entitet, har du några alternativ för att åtgärda detta.

De fördefinierade entiteter som läggs till i appen returneras _alltid_ , även om uttryck ska extrahera anpassade entiteter för samma text. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Ändra den taggade entiteten i exempel uttryck

Om den fördefinierade entiteten är samma text eller token som den anpassade entiteten markerar du texten i exemplet uttryck och ändrar den taggade uttryck. 

Om den fördefinierade entiteten är Taggad med mer text eller token än din anpassade entitet har du ett par alternativ för att åtgärda detta:

* [Ta bort exempel uttryck](#remove-example-utterance-to-fix-tagging) Metod
* [Ta bort](#remove-prebuilt-entity-to-fix-tagging) fördefinierad enhets metod

#### <a name="remove-example-utterance-to-fix-tagging"></a>Ta bort exempel uttryck för att åtgärda taggning 

Det första du väljer är att ta bort exemplet uttryck. 

1. Ta bort exemplet uttryck.
1. Träna om appen. 
1. Lägg bara till det ord eller den fras som är entiteten, som är markerad som en fördefinierad entitet, som ett komplett exempel uttryck. Ordet eller frasen har fortfarande den fördefinierade entiteten markerad. 
1. Välj entiteten i exemplet uttryck på sidan **avsikt** och ändra till din anpassade entitet och träna igen. Detta bör förhindra att LUIS markerar den här exakta texten som den färdiga entiteten i ett exempel yttranden som använder den texten. 
1. Lägg till hela det ursprungliga exemplet uttryck tillbaka till avsikten. Den anpassade entiteten bör fortfarande markeras i stället för den fördefinierade entiteten. Om den anpassade entiteten inte har marker ATS, måste du lägga till fler exempel på texten i yttranden.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Ta bort den fördefinierade entiteten för att åtgärda taggning

1. Ta bort den fördefinierade entiteten från appen. 
1. På sidan **avsikt** markerar du den anpassade entiteten i exemplet uttryck.
1. Träna appen.
1. Lägg tillbaka den färdiga entiteten i appen och träna appen. Den här korrigeringen förutsätter att den fördefinierade entiteten inte är en del av en sammansatt entitet.

## <a name="regular-expression-entity"></a>Entitet för reguljära uttryck 

En [entitet för reguljära uttryck](reference-entity-regular-expression.md) extraherar en entitet baserat på ett mönster för reguljära uttryck som du anger.

## <a name="simple-entity"></a>Enkel entitet

En [enkel enhet](reference-entity-simple.md) är ett värde för datorn lärt dig. Det kan vara ett ord eller fraser.
## <a name="entity-limits"></a>Entiteten gränser

Granska [gränser](luis-boundaries.md#model-boundaries) att förstå hur många av varje typ av enhet du kan lägga till en modell.

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Om du behöver mer än det maximala antalet enheter 

Du kan behöva använda sammansatta entiteter i kombination med enhets roller.

Sammansatta entiteter representerar delar av en helhet. En sammansatt entitet med namnet PlaneTicketOrder kan till exempel ha underordnade entiteter flygbolag, mål, DepartureCity, DepartureDate och PlaneTicketClass.

LUIS innehåller också en lista med enhets typer som inte är maskin medveten men som gör att din LUIS-app kan ange en fast lista med värden. Se [LUIS gränser](luis-boundaries.md) referens till granska gränserna för listtypen för entiteten. 

Kontakta supporten om du har övervägt dessa entiteter och fortfarande behöver mer än gränsen. Samla in detaljerad information om datorn för att göra det, går du till den [LUIS](luis-reference-regions.md#luis-website) webbplats och väljer sedan **Support**. Om din Azure-prenumeration innehåller supporttjänster, kontakta [teknisk support för Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Nästa steg

Lär dig mer begrepp om bra [yttranden](luis-concept-utterance.md). 

Se [Lägg till entiteter](luis-how-to-add-entities.md) mer information om hur du lägger till entiteter i din LUIS-app.
