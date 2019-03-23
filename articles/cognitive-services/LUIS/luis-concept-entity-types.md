---
title: Entitetstyper
titleSuffix: Language Understanding - Azure Cognitive Services
description: 'Entiteter extrahera data från uttryck. Entitetstyper ger förutsägbar extrahering av data. Det finns två typer av enheter: datorn lärt dig och icke-machine-lärt dig. Det är viktigt att veta vilken typ av enhet som du arbetar med i yttranden.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: diberry
ms.openlocfilehash: d12ea20f9f510b0e2d3d3512d8d8c71a3fb96eec
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372530"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Entitetstyper och deras syften i LUIS

Entiteter extrahera data från uttryck. Entitetstyper ger förutsägbar extrahering av data. Det finns två typer av enheter: datorn lärt dig och icke-machine-lärt dig. Det är viktigt att veta vilken typ av enhet som du arbetar med i yttranden. 

## <a name="entity-compared-to-intent"></a>Entiteten jämfört med avsikt

Entiteten representerar ett ord eller en fras i uttryck som du vill extrahera. Ett uttryck kan innehålla många entiteter eller inga alls. En entitet representerar en klass, inklusive en samling liknande objekt (platser, saker, personer, händelser eller begrepp). Entiteter beskriver information som är relevanta för avsikten och ibland de är viktiga för din app för att utföra sitt uppdrag. En nyhetssökning app kan exempelvis innehålla entiteter, till exempel ”ämne”, ”källa”, ”nyckelordet” och ”publiceringsdatum”, som är viktiga data till Sök efter nyheter. Är viktig information för flygning bokning (relevant till avsikt ”boka flygning”) i en app för bokning av resor, ”plats”, ”datum”, ”flygbolag”, ”resa class” och ”biljetter”.

Jämförelsevis har representerar avsikten förutsägelser av hela uttryck. 

## <a name="entities-help-with-data-extraction-only"></a>Entiteter hjälp med endast extrahering av data

Etiketter eller markera enheter för att entiteten extrahering kan it hjälper inte med avsikt förutsägelse.

## <a name="entities-represent-data"></a>Entiteter som representerar data

Entiteter är data som du vill hämta från uttryck. Detta kan vara ett namn, datum, produktnamn och grupper av orden. 

|Yttrande|Entitet|Data|
|--|--|--|
|Köpa 3 biljetter New York|Fördefinierade tal<br>Location.Destination|3<br>New York|
|Köp en biljett från New York till London 5 mars|Location.Origin<br>Location.Destination<br>Fördefinierade datetimeV2|New York<br>London<br>5 mars 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Entiteter är valfritt men rekommenderas

Avsikter är obligatoriska, är entiteter valfria. Du behöver inte skapa entiteter för varje begrepp i din app, men endast för de som krävs för klientprogram att vidta åtgärder. 

Om din yttranden inte har information om din robot måste fortsätta, behöver du inte att lägga till dem. När din app utvecklas, kan du lägga till dem senare. 

Om du inte är säker på hur du använder informationen kan du lägga till några vanliga förskapade entiteter som [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordningstal](luis-reference-prebuilt-ordinal.md), [e-post](luis-reference-prebuilt-email.md), och [telefonnummer ](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Etikett för word betydelse

Om word val eller word placering är samma, men inte betyda samma sak, du inte märker den med entiteten. 

Följande uttryck, ordet `fair` är en homografiska. Det har stavats samma, men har en annan betydelse:

|Yttrande|
|--|
|Vilken typ av region mässor sker i Seattle-området den här sommaren?|
|Är den aktuella klassificeringen för Seattle-granskning fair?|

Om du vill att en händelse entitet för att hitta alla händelsedata kan etikettera ordet `fair` i den första uttryck, men inte i andra.

## <a name="entities-are-shared-across-intents"></a>Entiteter som är gemensamma för avsikter

Entiteter delas avsikter. De tillhör inte någon enskild avsikt. Avsikter och entiteter kan associeras semantiskt men det är inte en exklusiv relation.

I uttryck ”boka mig en biljett till Paris”, ”Paris” är en entitet som refererar till platsen. Genom att fokusera på de entiteter som nämns i användarens uttryck, hjälper LUIS klientprogrammet välja vilka åtgärder att vidta för att uppfylla användarens begäran.

## <a name="mark-entities-in-none-intent"></a>Markera entiteter i ingen avsikt

Alla avsikter, inklusive den **ingen** avsikt, bör ha markerats entiteter när det är möjligt. På så sätt kan LUIS mer information om var entiteterna finns i talade och ord är runt entiteterna. 

## <a name="entity-status-for-predictions"></a>Status för enheter för förutsägelser

LUIS-portalen meddelar när entiteten i en exempel-uttryck är antingen skiljer sig från den markerade entiteten eller är för nära till en annan entitet och därför oklart. Detta visas med en röd understrykning i exempel-uttryck. 

Mer information finns i [Status för enheter förutsägelser](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Typer av enheter

LUIS erbjuder många typer av enheter. Välja den entitet som baseras på hur data ska extraheras och hur den ska representeras när det ska extraheras.

Entiteter kan extraheras med machine learning, vilket gör att LUIS för att lära dig mer om hur entiteten visas i uttryck. Entiteter kan extraheras utan maskininlärning, matchar exakt text eller ett reguljärt uttryck. Entiteter i mönster kan extraheras med en blandad implementering. 

När entiteten har extraherat entitetsdata representeras som en enhet av information eller kombineras med andra entiteter för att bilda en enhet med information att klientprogrammet kan använda.

|Machine-learned|Kan markera|Självstudier|Exempel<br>Svar|Entitetstyp|Syfte|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Sammansatta**](#composite-entity)|Gruppering av enheter, oavsett typ av enhet.|
|✔|✔|[✔](luis-quickstart-intent-and-hier-entity.md)|[✔](luis-concept-data-extraction.md#hierarchical-entity-data)|[**Hierarkisk**](#hierarchical-entity)|Gruppering av enkla enheter.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**List**](#list-entity)|Lista med objekt och deras synonymer extraheras med exakt denna matchning.|
|Blandad||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|Enheten där det är svårt att avgöra att slutet av entiteten.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Prebuilt**](#prebuilt-entity)|Redan tränats att extrahera olika typer av data.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Reguljärt uttryck**](#regular-expression-entity)|Använder reguljärt uttryck för att matcha texten.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Simple**](#simple-entity)|Innehåller ett enda koncept i ord eller fraser.|

Endast datorn lärt dig enheter måste markeras i exempel-uttryck för varje avsikt. Dator-lärt dig entiteter fungerar bäst när testas [endpoint frågor](luis-concept-test.md#endpoint-testing) och [granska endpoint yttranden](luis-how-to-review-endoint-utt.md). 

Pattern.any entiteter måste markeras i den [mönstret](luis-how-to-model-intent-pattern.md) mallexempel, inte avsikt användaren exemplen. 

Blandade entiteterna används en kombination av entiteten identifieringsmetoder.

## <a name="composite-entity"></a>Sammansatt entitet

En sammansatt entitet består av andra enheter, till exempel förskapade entiteter enkel, reguljära uttryck, lista och hierarkisk entiteter. Separata entiteter utgör en helhet. 

Den här entiteten är ett bra passar när data:

* Är relaterade till varandra. 
* De är relaterade till varandra i yttrandet.
* Använda en mängd olika typer av enheter.
* Måste vara grupperade och bearbetas av klientprogrammet som en enhet av information.
* Har en mängd olika användare yttranden som kräver maskininlärning.

![Sammansatt entitet](./media/luis-concept-entities/composite-entity.png)

[Självstudie](luis-tutorial-composite-entity.md)<br>
[Exempel-JSON-svar för entitet](luis-concept-data-extraction.md#composite-entity-data)<br>

## <a name="hierarchical-entity"></a>Hierarkisk entitet

En hierarkisk entitet är en viss kategori av sammanhangsmässigt inlärda enkla enheter som kallas underordnade.

Den här entiteten är ett bra passar när data:

* De är enkla entiteter.
* De är relaterade till varandra i yttrandet.
* Använd specifika ord val för att ange varje underordnad entitet. Exempel på sådana ord: från/till, lämnar/ska till, bort från/till.
* Underordnade är ofta i samma uttryck. 
* Båda måste grupperas och bearbetas av klientappen som en informationsenhet.

Använd inte om:

* Du behöver en entitet som har exakt denna matchningar för underordnade oavsett kontexten. Använd en [lista entitet](#list-entity) i stället. 
* Du behöver en entitet för en överordnad-underordnad-relation med andra typer av enheter. Använd den [sammansatt entitet](#composite-entity).

![hierarkisk entitet](./media/luis-concept-entities/hierarchical-entity.png)

[Självstudie](luis-quickstart-intent-and-hier-entity.md)<br>
[Exempel-JSON-svar för entitet](luis-concept-data-extraction.md#hierarchical-entity-data)<br>

### <a name="roles-versus-hierarchical-entities"></a>Roller jämfört med hierarkisk entiteter

[Roller](luis-concept-roles.md#roles-versus-hierarchical-entities) lösa samma problem för ett mönster som hierarkisk entiteter men gäller för alla typer av enheter. Roller är för närvarande endast tillgängliga i ett mönster. Roller är inte tillgängliga i avsikter exempel yttranden.  

## <a name="list-entity"></a>Lista entitet

Lista över entiteter representerar en fast, stängda uppsättning närstående ord tillsammans med deras synonymer. LUIS identifierar inte ytterligare värden för listan över entiteter. Använd den **rekommenderar** funktionen för att se förslag för nya ord baserat på den aktuella listan. Om det finns mer än en entitet i listan med samma värde, returneras varje entitet i frågan slutpunkt. 

Entiteten är ett bra passar när textdata:

* Är en känd uppsättning.
* Uppsättningen inte överskrider de högsta [gränserna](luis-boundaries.md) för LUIS för den här entitetstypen.
* Texten i yttrandet stämmer exakt med en synonym eller det kanoniska namnet. LUIS använder inte listan för något mer än exakta textmatchningar. Ordstamsigenkänning, plural eller andra ändringar matchas inte med en entitet i listan. Om du vill hantera variationer bör du överväga att använda ett [mönster](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) med valfri textsyntax.

![lista entitet](./media/luis-concept-entities/list-entity.png)

[Självstudie](luis-quickstart-intent-and-list-entity.md)<br>
[Exempel-JSON-svar för entitet](luis-concept-data-extraction.md#list-entity-data)

## <a name="patternany-entity"></a>Entiteten Pattern.any

Pattern.any är en platshållare för variabel längd som används endast i ett mönster mall uttryck för att markera där entiteten börjar och slutar.  

Entiteten är ett bra passar när:

* Slut på entiteten kan blandas ihop med återstående texten för uttryck. 
[Självstudie](luis-tutorial-pattern.md)<br>
[Exempel-JSON-svar för entitet](luis-concept-data-extraction.md#patternany-entity-data)

**Exempel**  
Ett klientprogram som söker för böcker baserat på rubriken får extraherar pattern.any fullständig rubriken. En mall-uttryck med hjälp av pattern.any för den här boken sökningen är `Was {BookTitle} written by an American this year[?]`. 

I tabellen nedan har varje rad två versioner av uttryck. Främsta uttryck är hur LUIS visas först i uttryck, där det är oklart med rubriken boken börjar och slutar. Nedre uttryck är hur LUIS vet Bokens titel när det finns ett mönster för extrahering. 

|Yttrande|
|--|
|The Man som Misstog His FRU för en Hat och andra kliniska Tales skrevs av en American år?<br>Var **The Man som Misstog His FRU för en Hat och andra kliniska Tales** skrivits av en American år?|
|Var halva vilande i Frog Pajamas som skrivits av en American år?<br>Var **halva vilande i Frog Pajamas** skrivits av en American år?|
|Var särskilt sorg av Matt enkelt: En bok som skrivits av en American år?<br>Var **viss sorg av Matt enkelt: En bok** skrivits av en American år?|
|Var det finns en Wocket i min Pocket! skrivits av en American år?<br>Var **det finns en Wocket i min Pocket!** skrivits av en American år?|

## <a name="prebuilt-entity"></a>Fördefinierade entitet

Fördefinierade entiteter finns inbyggda typer som representerar vanliga begrepp som e-post, URL: en och telefonnummer. Fördefinierade entitetsnamn är reserverade. [Alla förskapade entiteter](luis-prebuilt-entities.md) som läggs till programmet returneras i förutsägelsefråga slutpunkt om de finns i uttryck. 

Entiteten är ett bra passar när:

* Informationen som matchar ett vanligt användningsfall som stöds av färdiga entiteter för språk-kultur. 

Fördefinierade entiteter kan har lagts till och tas bort när som helst.

![Antal fördefinierade entitet](./media/luis-concept-entities/number-entity.png)

[Självstudie](luis-tutorial-prebuilt-intents-entities.md)<br>
[Exempel-JSON-svar för entitet](luis-concept-data-extraction.md#prebuilt-entity-data)

Några av de här fördefinierade entiteter har definierats i öppen källkod [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text) projekt. Om din specifika kultur eller entitet inte stöds för närvarande, bidra till projektet. 

### <a name="troubleshooting-prebuilt-entities"></a>Felsökning av fördefinierade entiteter

LUIS-portalen om en fördefinierade entitet markeras i stället för din anpassade entitet har du några alternativ för hur detta kan åtgärdas.

Fördefinierade entiteter har lagts till i appen kommer _alltid_ returneras, även om uttryck bör extrahera anpassade entiteter för samma text. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Ändra taggade entitet i exempel-uttryck

Om den fördefinierade entiteten är samma text eller token som den anpassade entiteten, markerar du texten i exempel-uttryck och ändra taggade uttryck. 

Om entiteten fördefinierade märks med mer text eller token än din anpassade entitet, har du ett par alternativ för hur du åtgärdar detta:

* [Ta bort exempel uttryck](#remove-example-utterance-to-fix-tagging) metod
* [Ta bort fördefinierade entitet](#remove-prebuilt-entity-to-fix-tagging) metod

#### <a name="remove-example-utterance-to-fix-tagging"></a>Ta bort exempel uttryck för att åtgärda taggning 

Ditt första val är att ta bort exempel uttryck och träna om appen. Lägg tillbaka till bara ordet eller fras som är enheten som en exempel-uttryck och sedan markera de entiteter och träna. Lägg nu till tillbaka fördefinierade entiteten och den ursprungliga exempel-uttryck. Den anpassade entiteten ska fortsätta att markeras i stället för den fördefinierade entiteten. 

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Ta bort fördefinierade entitet för att åtgärda taggning

I andra hand är att ta bort entiteten färdiga appen, sedan tagga den anpassade entiteten i exempel-uttryck och sedan lägga till färdiga entiteten i appen. Den här snabbkorrigeringen förutsätter fördefinierade entiteten är inte en del av en sammansatt entitet. 

## <a name="regular-expression-entity"></a>Entitet för reguljära uttryck 

Ett reguljärt uttryck som passar bäst för raw uttryck text. Det är inte skiftlägeskänslig och ignorerar kulturella variant.  Matchning med reguljära uttryck tillämpas efter ändringar av stavningskontroll på teckennivån, inte på token-nivå. Om det reguljära uttrycket är för komplext, till exempel med många hakparenteser kan du inte kan lägga till uttrycket i modellen. Använder en del men inte alla de [.NET Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) biblioteket. 

Entiteten är ett bra passar när:

* Data formateras konsekvent med alla variationer som också är konsekvent.
* Det reguljära uttrycket behöver inte mer än 2 nivåer av inkapsling. 

![Entitet för reguljära uttryck](./media/luis-concept-entities/regex-entity.png)

[Självstudie](luis-quickstart-intents-regex-entity.md)<br>
[Exempel-JSON-svar för entitet](luis-concept-data-extraction.md#regular-expression-entity-data)<br>

## <a name="simple-entity"></a>Enkel entitet 

En enkel enhet är en allmän entitet som beskriver ett enda koncept och lära sig från kontexten som datorn lärt dig. Eftersom enkla enheter är allmänt namn, till exempel företagets namn, produktnamn eller andra typer av namn, lägga till en [frasen lista](luis-concept-feature.md) när du använder en enkel enhet för att öka signalen från de namn som används. 

Entiteten är ett bra passar när:

* Data inte är konsekvent formaterade men anger samma sak. 

![enkel enhet](./media/luis-concept-entities/simple-entity.png)

[Självstudie](luis-quickstart-primary-and-secondary-data.md)<br/>
[Exempel på ett svar för entitet](luis-concept-data-extraction.md#simple-entity-data)<br/>

## <a name="entity-limits"></a>Entiteten gränser

Granska [gränser](luis-boundaries.md#model-boundaries) att förstå hur många av varje typ av enhet du kan lägga till en modell.

## <a name="composite-vs-hierarchical-entities"></a>Sammansatta vs hierarkisk entiteter

Sammansatta entiteter och hierarkisk entiteter både har överordnade och underordnade relationer och är datorn lärt dig. Machine learning kan LUIS för att förstå entiteter baserat på olika kontexter (placering av ord). Sammansatta entiteter är mer flexibla eftersom de låter olika enhetstyper som underordnade. En hierarkisk entitet underordnade är bara enkla enheter. 

|Typ|Syfte|Exempel|
|--|--|--|
|Hierarkisk|Överordnad-underordnad av enkla enheter|Location.Origin=New York<br>Location.Destination=London|
|Sammansatta|Överordnade-underordnade entiteter: fördefinierade, lista, enkel, hierarkisk| Antal = 3<br>lista = första klass<br>prebuilt.datetimeV2=March 5|

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Om du behöver mer än det maximala antalet enheter 

Du kan behöva använda hierarkisk och sammansatta entiteter. Hierarkisk entiteter visas relation mellan entiteter som har egenskaper eller som är medlemmar i en kategori. Underordnade entiteter är alla medlemmar i deras överordnade kategorin. En hierarkisk entitet med namnet PlaneTicketClass kan till exempel ha underordnade entiteter EconomyClass och bästa. Hierarkin omfattar endast en nivå med djup.  

Sammansatta entiteter representerar delar av en helhet. En sammansatt entitet med namnet PlaneTicketOrder kan till exempel ha underordnade entiteter flygbolag, mål, DepartureCity, DepartureDate och PlaneTicketClass. Du skapar en sammansatt entitet från befintliga enkla enheter, underordnade hierarkisk entiteter eller fördefinierade entiteter.  

LUIS innehåller också listtypen för entiteten som inte datorn lärt dig men kan din LUIS-app att ange en fast lista med värden. Se [LUIS gränser](luis-boundaries.md) referens till granska gränserna för listtypen för entiteten. 

Om du har tänkt hierarkisk, sammansatta, och lista över entiteter och behöver mer än gränsen, kontakta supporten. Samla in detaljerad information om datorn för att göra det, går du till den [LUIS](luis-reference-regions.md#luis-website) webbplats och väljer sedan **Support**. Om din Azure-prenumeration innehåller supporttjänster, kontakta [teknisk support för Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Nästa steg

Lär dig mer begrepp om bra [yttranden](luis-concept-utterance.md). 

Se [Lägg till entiteter](luis-how-to-add-entities.md) mer information om hur du lägger till entiteter i din LUIS-app.
