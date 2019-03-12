---
title: Fulltext-motor (Lucene) sökarkitekturen – Azure Search
description: Förklaring av Lucene fråga uppgiftshantering och hämtning begrepp för fulltextsökning, som är relaterade till Azure Search.
manager: jlembicz
author: yahnoosh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: d504635121c5153367cd0b89ce593b093bb3cd39
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537252"
---
# <a name="how-full-text-search-works-in-azure-search"></a>Hur Fullständig textsökning fungerar i Azure Search

Den här artikeln är för utvecklare som behöver en djupare förståelse för hur Lucene fulltextsökning fungerar i Azure Search. Azure Search kommer leverera förväntat resultat i de flesta fall för textfrågor, men ibland kan du få ett resultat som ser ut ”off” på något sätt. I sådana fall är att ha en bakgrund i fyra faser i Lucene Frågekörningen (fråga parsning lexikal analys, dokumentera matchning, bedömning) hjälper dig att identifiera specifika ändringar till frågeparametrar eller index-konfiguration som ger önskad resultatet. 

> [!Note] 
> Azure Search använder Lucene för fulltextsökning, men Lucene-integrering är inte komplett. Vi selektivt exponera och utöka Lucene-funktioner för att aktivera scenarierna som är viktigt att Azure Search. 

## <a name="architecture-overview-and-diagram"></a>Översikt över arkitekturen och diagram

Bearbetning av en sökfråga för fulltext börjar med texten för frågan för att extrahera söktermer. Sökmotorn använder ett index för att hämta dokument med matchande termer. Enskilda sökord är ibland uppdelade och färdigställts till nya samarbetsformer att omvandla ett bredare net över vad övervägas som möjliga matchning. En resultatmängd sorteras sedan efter relevansgradering som tilldelats varje enskilt matchande dokument. De överst i rankad lista returneras till det anropande programmet.

Frågekörningen består räknas om, av fyra steg: 

1. Fråga parsning 
2. Lexikal analys 
3. Hämta dokument 
4. Bedömning 

Diagrammet nedan illustrerar de komponenter som används för att bearbeta en sökbegäran. 

 ![Arkitekturdiagram för Lucene-fråga i Azure Search][1]


| Nyckelkomponenter | Funktionsbeskrivning | 
|----------------|------------------------|
|**Fråga Parser** | Separera sökord från frågeoperatorer och skapa frågestrukturen (ett träd med frågan) som ska skickas till sökmotorn. |
|**Analysverktyg** | Analysera lexikal sökord. Den här processen kan omfatta att omvandla, ta bort eller expandering av sökord. |
|**Index** | En effektiv datastruktur som används för att lagra och organisera sökbara villkor som extraheras från indexerade dokumenten. |
|**Sökmotor** | Hämtar och poängsätter matchande dokument baserat på innehållet i vägar i inverterad indexet. |

## <a name="anatomy-of-a-search-request"></a>Uppbyggnad av en sökbegäran

En sökbegäran är en fullständig specifikation av vad som ska returneras i en resultatuppsättning. I enklaste form är det en tom fråga med några villkor av något slag. En mer realistisk exemplet innehåller parametrar, flera sökord kanske är begränsade till vissa fält med eventuellt ett filteruttryck och ordning regler.  

I följande exempel är en sökbegäran som du kan skicka till Azure Search med hjälp av den [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2017-11-11 
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
~~~~

Denna begäran gör sökmotorn följande:

1. Filtrerar ut dokument där priset är minst $60 och mindre än 300 USD.
2. Kör frågan. I det här exemplet sökfrågan består av fraser och villkor: `"Spacious, air-condition* +\"Ocean view\""` (användarna vanligtvis inte anger skiljetecken, men inkludera den i det här exemplet kan vi förklara hur analysverktyg hanterar det). Den här frågan är sökmotorn söker igenom beskrivningen och rubrikfält anges i `searchFields` för dokument som innehåller ”Ocean view”, och dessutom på termen ”stora” eller på villkor som börjar med prefixet ”air-condition”. Den `searchMode` används för att matcha i något villkor (standard) eller alla, i de fall där en term inte krävs uttryckligen (`+`).
3. Beställningar den resulterande Hotellen som angetts av närhet till en viss geografisk plats och sedan tillbaka till det anropande programmet. 

Merparten av den här artikeln handlar om bearbetningen av den *sökfråga*: `"Spacious, air-condition* +\"Ocean view\""`. Filtrering och sortering ligger utanför omfånget. Mer information finns i den [Search API-referensdokumentation](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Steg 1: Fråga parsning 

Enligt vad som anges, är den första raden för begäran i frågesträngen: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

Frågeparsern skiljer operatörer (till exempel `*` och `+` i det här exemplet) från sökning villkor och deconstructs sökfrågan i *underfrågor* av en typ som stöds: 

+ *Termen fråga* för fristående villkor (t.ex. stora)
+ *frasfråga* för citerade villkor (till exempel havet view)
+ *prefixet fråga* för termer följt av ett prefix-operatorn `*` (t.ex. air-condition)

En fullständig lista över stöds frågetyper finns i [Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operatörer som är associerade med en underfråga avgöra om frågan ”måste vara” eller ”ska vara” uppfyllt för ett dokument för att anses vara en matchning. Till exempel `+"Ocean view"` är ”måste” på grund av den `+` operator. 

Frågeparsern omstrukturerar underfrågor i en *fråga trädet* (en interna strukturen som representerar frågan) överförs till sökmotorn. I det första steget för att fråga tolka trädet fråga ser ut så här.  

 ![Boolesk fråga searchmode alla][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Stöds Parser: Enkel och fullständig Lucene 

 Azure Search visar två olika frågespråk `simple` (standard) och `full`. Genom att ange den `queryType` parameter med din begäran du berätta frågeparsern vilka frågespråk du väljer så att den vet hur du tolkar operatorer och syntax. Den [enkel frågespråk](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) är intuitivt och robust, ofta lämpligt att tolka indata från användaren som – utan bearbetning på klientsidan. Det stöder frågeoperatorer som är välbekanta från sökmotorer. Den [frågespråk för fullständig Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), som du får genom att ange `queryType=full`, utökar standardspråk för enkel fråga genom att lägga till stöd för flera operatorer och frågetyper som jokertecken, fuzzy, regex och fältbegränsade frågor. Till exempel skulle ett reguljärt uttryck som skickas i enkla frågesyntaxen tolkas som en frågesträng och inte ett uttryck. Exempelbegärande i den här artikeln använder det fullständiga Lucene-frågespråket.

### <a name="impact-of-searchmode-on-the-parser"></a>Effekten av searchMode i tolken 

En annan begäran sökparameter som påverkar parsning är den `searchMode` parametern. Den kontrollerar operatorn standard för booleska frågor: alla (standard) eller alla.  

När `searchMode=any`, som är standard, utrymme avgränsare mellan stora och air-condition är eller (`||`), vilket gör att frågan exempeltext motsvarar: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Explicit operatörer som `+` i `+"Ocean view"`, är entydiga i booleskt frågekonstruktion (termen *måste* matchar). Mindre uppenbara är så här tolkar du återstående villkoren: stora och air-condition. Bör sökmotorn hitta matchningar för havet vyn *och* stora *och* air-condition? Eller bör hitta havet visa plus *antingen ett* av de återstående villkoren? 

Som standard (`searchMode=any`), sökmotorn förutsätter bredare tolkning. Något av fälten *bör* matchas, vilket speglar ”eller”-semantik. Det första frågan trädet illustreras tidigare, med två ”bör” operations, visas standardinställningarna för.  

Anta att vi nu ställer `searchMode=all`. I det här fallet tolkas området som ett ”and”-åtgärd. Var och en av de återstående villkoren måste båda vara finns i dokumentet för att utgöra en matchning. Den resulterande exempelfråga skulle tolkas enligt följande: 

~~~~
+Spacious,+air-condition*+"Ocean view"
~~~~

Ett ändrade frågan träd för den här frågan är enligt följande, där ett matchande dokument är skärningspunkten för alla tre underfrågor: 

 ![Boolesk fråga searchmode alla][3]

> [!Note] 
> Välja `searchMode=any` över `searchMode=all` sitt bästa erhålls genom att köra representativa frågor. Användare som är sannolikt att inkludera operatorer (delad när sökning dokumentet lagrar) kanske resultat mer intuitiv användning om `searchMode=all` informerar booleskt fråga konstruktioner. Mer information om samspelet mellan `searchMode` och operatörer, se [enkla frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Steg 2: Lexikal analys 

Lexikalisk processen *term frågor* och *fras frågor* när frågan trädet är strukturerad. En analyzer accepterar Textinmatningar tilldelas som parsern, bearbetar text och sedan skickar tillbaka tokeniserad villkoren i trädet fråga. 

Den vanligaste formen av lexikal analys är *lingvistiska* som transformeringar skicka frågor till villkor baserat på regler som är specifika för ett visst språk: 

* Minska en frågeterm till roten form av ett ord 
* Ta bort irrelevanta ord (stoppord, till exempel ”den” eller ”och” på engelska) 
* Dela upp ett sammansatta ord i komponenter 
* Lägre skiftläge ett ord med versal 

Alla dessa åtgärder tenderar att radera skillnaderna mellan textinmatning som anges av användaren och de villkor som lagras i indexet. Sådana åtgärder utöver text bearbetning och kräver djupare kunskap för språket som själva. Om du vill lägga till den här lager med språkliga medvetenhet, Azure Search har stöd för en lång lista med [språkanalysverktyg](https://docs.microsoft.com/rest/api/searchservice/language-support) från både Lucene och Microsoft.

> [!Note]
> Analys av kraven kan variera mellan minimal till avancerade beroende på ditt scenario. Du kan styra komplexitet lexikal analys genom att välja en av de fördefinierade analysverktyg eller genom att skapa din egen [anpassat analysverktyg](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analysverktyg är begränsade till sökbara fält och anges som en del av fältdefinitionen. På så sätt kan du variera lexikal analys på basis av per fält. Angivet används den *standard* analysverktyget från Lucene används.

I vårt exempel före analys, har det första frågan trädet termen ”Spacious” med en versal ”S” och kommatecken som frågeparsern tolkar som en del av frågetermen (kommatecken inte anses vara en operator för query language).  

När standard analysatorn bearbetar termen, kommer den gemener ”ocean view” och ”stora” och ta bort kommatecken. Trädet ändrade frågan ska se ut så här: 

 ![Booleska fråga med analyserade villkor][4]

### <a name="testing-analyzer-behaviors"></a>Testa analyzer beteenden 

Beteendet för en analyzer kan testas med hjälp av den [analysera API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Ange text som du vill analysera för att se villkor anges analyzer genererar. Om du vill se hur standard analysatorn skulle bearbeta den texten ”air-condition”, till exempel kan du utfärda följande begäran:

~~~~
{
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

Standard analysatorn delar upp indatatexten i följande två token genom att kommentera dem med attribut som start och end förskjutningarna (används för markering av träffar) samt deras placering (som används för matchning av frasen):

~~~~
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
~~~~

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Undantag till lexikal analys 

Lexikal analys gäller enbart för frågetyper som kräver fullständiga villkor – en term fråga eller en frasfråga. Det gäller inte till frågetyper med ofullständiga villkor – prefix fråga, jokerteckenfråga, regex fråga – eller till en fuzzy fråga. De frågetyperna, inklusive prefix-fråga med termen `air-condition*` i vårt exempel läggs direkt till trädet frågan, vilket kringgår det analysis-steget. Endast transformeringen utförs på sökord dessa typer av platsargumentet.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Steg 3: Hämta dokument 

Hämta dokument refererar till att söka efter dokument med matchande termer i indexet. Det här skedet har förstått bäst igenom ett exempel. Låt oss börja med ett hotell index som har följande enkla schema: 

~~~~
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
~~~~

Anta vidare att det här indexet innehåller följande fyra dokument: 

~~~~
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
~~~~

**Hur villkoren indexeras**

För att förstå hämtning, hjälper det att du vet lite av grunderna om indexering. Lagringsenheten är ett vägar i inverterad index, en för varje sökbara fält. Är en sorterad lista över alla termer från alla dokument inom ett vägar i inverterad index. Varje term mappar i listan över dokument där sådan uppstår, som tydligt i exemplet nedan.

För att skapa villkoren i ett vägar i inverterad index, utför sökmotorn lexikal analys över innehållet i dokumenten, liknar vad som händer under frågebearbetning:

1. *Textinmatningar* skickas till en analyzer, lägre bokstäver, demontering av interpunktion och så vidare, beroende på hur analyzer. 
2. *Token* är utdata för textanalys.
3. *Allmänna* läggs till i indexet.

Det är vanligt, men är inget krav att använda samma analysverktyg för sökning och indexering åtgärder så att sökord titta mer som villkor i indexet.

> [!Note]
> Azure Search kan du ange olika analysverktyg för indexering och söka via ytterligare `indexAnalyzer` och `searchAnalyzer` fältet parametrar. Om inget anges analysatorn anges med den `analyzer` egenskapen används för både indexering och sökning.  

**Vägar i inverterad index för dokument**

Tillbaka till vårt exempel för den **rubrik** fält, vägar i inverterad indexet ser ut så här:

| Period | Listan över standarddokument |
|------|---------------|
| atman | 1 |
| stranden | 2 |
| hotell | 1, 3 |
| havet | 4  |
| playa | 3 |
| utväg | 3 |
| retreat | 4 |

I rubrikfältet endast *hotell* dyker upp i två dokument: 1, 3.

För den **beskrivning** fält, indexet är följande:

| Period | Listan över standarddokument |
|------|---------------|
| AIR | 3
| och | 4
| stranden | 1
| villkor | 3
| nöjd | 3
| avstånd | 1
| island | 2
| kauaʻi | 2
| finns | 2
| Nord | 2
| havet | 1, 2, 3
| av | 2
| på |2
| tyst | 4
| rum  | 1, 3
| secluded | 4
| land | 2
| stora | 1
| den | 1, 2
| till | 1
| vy | 1, 2, 3
| walking | 1
| med  | 3


**Matchar frågetermen mot indexerade ord**

Med de vägar i inverterad index som ovan kan vi gå tillbaka till exempel frågan och se hur matchande dokument hittades för vår exempelfråga. Kom ihåg att trädet slutlig ser ut så här: 

 ![Booleska fråga med analyserade villkor][4]

Vid körning av fråga körs enskilda frågor mot de sökbara fälten oberoende av varandra. 

+ ”Stora” matchningar TermQuery, dokumentera 1 (hotell Atman). 

+ PrefixQuery ”, air-condition *”, matchar inte några dokument. 

  Det här är ett beteende som ibland confuses utvecklare. Även om termen luftkonditionerad finns i dokumentet, upp den i två termer av standard-analyzer. Kom ihåg att prefixet frågor som innehåller partiella villkor, inte har analyserats. Därför villkor med prefixet ”air-condition” letas upp i vägar i inverterad indexet och hittades inte.

+ PhraseQuery, ”ocean view”, letar upp villkoren ”ocean” och ”visa” och kontrollerar närhet av villkoren i det ursprungliga dokumentet. Dokument 1, 2 och 3 matchar den här frågan i beskrivningsfältet. Observera dokumentet 4 har termen havet i rubriken men inte anses vara en matchning som vi söker efter frasen ”ocean view” i stället för enskilda ord. 

> [!Note]
> En sökfråga körs oberoende mot alla sökbara fält i Azure Search-index om du inte begränsar fälten med den `searchFields` parameter, såsom illustreras i exemplet sökbegäran. Dokument som matchar i någon av de markerade fälten returneras. 

På hela är dokument som matchar för frågan i fråga 1, 2, 3. 

## <a name="stage-4-scoring"></a>Steg 4: Bedömning  

Alla dokument i ett sökresultat tilldelas relevansgradering. Funktionen för relevans poängen är att högre de dokument som bäst svar på en fråga för användaren som uttryckt genom sökfrågan. Poängen beräknas utifrån statistiska egenskaper för termer som matchas. Kärnan i bedömnings formeln är [TF/IDF (termen frekvens inverterade dokumentet frekvens)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). I frågor som innehåller sällsynta och vanliga termer, främjar TF/IDF resultat med sällsynta termen. Till exempel i ett hypotetiskt index med alla Wikipedia-artiklar från dokument som matchar frågan *ordförande*, dokument som matchar på *president* anses vara mer relevant än dokument matcha mot *den*.


### <a name="scoring-example"></a>Bedömning exempel

Återkalla de tre dokument som matchar vår exempelfråga:
~~~~
search=Spacious, air-condition* +"Ocean view"  
~~~~
~~~~
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
~~~~

Dokumentet 1 matchar frågan bäst eftersom båda termen *stora* och nödvändiga frasen *hav visa* sker i beskrivningsfältet. Följande två dokument matchar endast frasen *hav visa*. Det kan vara så konstigt att relevansgradering för dokument 2 och 3 är olika, även om de matchande frågan på samma sätt. Det beror på att bedömnings-formeln har mer än bara TF/IDF-komponenter. I det här fallet har dokumentet 3 tilldelats en något högre poäng eftersom dess beskrivning är kortare. Lär dig mer om [Lucenes praktiska bedömning formeln](https://lucene.apache.org/core/4_0_0/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) att förstå hur fältlängden och andra faktorer kan påverka relevansgradering.

Vissa frågetyperna (med jokertecken, prefix, regex) alltid bidra med ett konstant värde till den övergripande dokument poängen. På så sätt kan matchningar hittades via fråga expansion ska tas med i resultatet, men utan att påverka rangordning. 

Ett exempel visar varför det är viktigt. Jokertecken, inklusive prefix sökningar är tvetydiga per definition eftersom indata är en partiell sträng med möjliga matchningar på ett mycket stort antal olika villkor (beakta indata av ”rundtur *”, med matchningar hittades på ”visningar”, ”tourettes”, och ” tourmaline ”). Naturen för de här resultaten returneras, går det inte att härleda rimligen vilket är mer värdefulla än andra. Därför ignorera vi termen frekvenser när bedömning resultat i frågor av typer med jokertecken, prefix och regex. Resultat från partiella indata ingår i en flerdelad search-begäran som innehåller begränsade och fullständiga villkor, med ett konstant värde att undvika att rikta mot potentiellt oväntat matchningar.

### <a name="score-tuning"></a>Poäng justering

Det finns två sätt att finjustera relevans poäng i Azure Search:

1. **Poängprofiler** marknadsföra dokument i rankad lista med resultat baserat på en uppsättning regler. I vårt exempel kan vi fundera på att dokument som matchade i rubrikfältet mer relevant än de dokument som matchas i beskrivningsfältet. Om indexet har haft ett fält för varje hotell, kan vi dessutom Flytta upp dokument med lägre pris. Lär dig mer [lägga till bedömning profiler till ett search-index.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Termförstärkning** (endast tillgänglig i den fullständiga Lucene-frågesyntaxen) tillhandahåller en felkällorna operator `^` som kan tillämpas på någon del av frågan trädet. I vårt exempel, i stället för att söka på prefixet *air-condition*\*, något gick Sök efter någon exakt termen *air-condition* eller prefix, men dokument som matchar på exakt termen är rangordnas högre genom att använda boost termen frågan: * air villkor ^ 2 || AIR-condition **. Läs mer om [termförstärkning](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Bedömning i ett distribuerat index

Alla index i Azure Search automatiskt är uppdelade i flera shards, vilket gör att vi kan snabbt distribuera indexet bland flera noder under skalning av tjänst upp eller ned. När en sökbegäran utfärdas mot varje shard oberoende av varandra. Resultaten från varje shard sedan samman och sorterade efter poäng (om ingen ordning har definierats). Det är viktigt att veta att bedömnings funktionen vikterna fråga termen frekvens mot frekvensen inverterade dokumentet i alla dokument inom fragmentet inte över alla shards!

Det innebär att relevansgradering *kan* vara olika för identiska dokument om de finns på olika shards. Som tur är kan tenderar sådana skillnader att försvinna när antalet dokument i indexet växer på grund av flera termen fördelas jämnt. Det går inte att anta på vilket fragment alla dokumentet kommer att placeras. Dock kommer under förutsättning att en dokumentnyckeln inte ändras, den alltid att tilldelas till samma fragment.

I allmänhet är dokumentet poäng inte det bästa attributet för ordning dokument om ordning stabilitet är viktigt. Till exempel finns med två dokument med en identisk poäng kan det ingen garanti vilken som visas först i efterföljande körningar av samma fråga. Dokumentet poäng bör endast ger en allmän uppfattning om dokumentet relevans i förhållande till andra dokument i resultatuppsättningen.

## <a name="conclusion"></a>Sammanfattning

Framgången för internet sökmotorer har signalerat förväntningar för fulltextsökning över privata data. För nästan alla typer av sökupplevelse planerar vi nu motor för att förstå vår avsikt, även om villkoren är felstavat eller är ofullständig. Vi tror även matchingar baserat på nära ekvivalenta termer eller synonymer som vi angav aldrig faktiskt.

Fulltextsökning är mycket komplexa kräver avancerade lingvistiska och systematiskt bearbetning på ett sätt som Omformulera, expandera och transformera sökord att leverera ett relevanta resultat från en teknisk synvinkel. Med de inbyggda komplexiteten kan finns det många olika faktorer som kan påverka resultatet av en fråga. Därför erbjuder lagt tid att förstå säkerhetsnivån fulltextsökning faktiska fördelar vid försök att gå igenom oväntade resultat.  

Den här artikeln utforskat fulltextsökning i samband med Azure Search. Vi hoppas att den ger tillräcklig förutsättningarna att kunna identifiera möjliga orsaker och lösningar för att hantera vanliga frågeproblem med. 

## <a name="next-steps"></a>Nästa steg

+ Skapa exempelindexet, prova olika frågor och granska resultatet. Anvisningar finns i [bygga och köra frågor mot ett index i portalen](search-get-started-portal.md#query-index).

+ Prova ytterligare frågesyntax från den [söka efter dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) exemplet eller från [enkla frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) i Sökutforskaren i portalen.

+ Granska [poängprofiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) om du vill justera rangordnas i ditt sökprogram.

+ Lär dig hur du använder [språkspecifika Lexikalisk](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurera anpassade analysverktyg](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) för minimal bearbetning eller specialiserade bearbetning på specifika fält.

+ [Jämför standard- och engelska analysverktyg](https://alice.unearth.ai/)) sida vid sida på den här demo-webbplatsen. 

## <a name="see-also"></a>Se också

[Söka efter dokument REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Fullständig Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Hantera sökresultat](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
