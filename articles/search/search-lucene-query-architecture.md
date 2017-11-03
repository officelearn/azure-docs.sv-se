---
title: "Fullständig text search engine (Lucene)-arkitekturen i Azure Search | Microsoft Docs"
description: "Förklaring av Lucene frågan bearbetning och dokumentet hämtning begrepp för textsökning som rör Azure Search."
services: search
manager: jhubbard
author: yahnoosh
documentationcenter: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/06/2017
ms.author: jlembicz
ms.openlocfilehash: 0b2e66cd40c1b49832b865e5bf59edcf78996eb8
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="how-full-text-search-works-in-azure-search"></a>Hur full textsökning fungerar i Azure Search

Den här artikeln är för utvecklare som behöver en bättre förståelse av hur Lucene fulltextsökning fungerar i Azure Search. Azure Search levererar sömlöst förväntat resultat i de flesta scenarier för textfrågor, men ibland kan du få ett resultat som verkar vara ”off” på något sätt. I dessa fall har en bakgrund i fyra faser i Lucene Frågekörningen (fråga tolkning lexikala analys, dokumentera matchar bedömningen) kan hjälpa dig att identifiera specifika ändringar av frågeparametrar eller Indexkonfigurationen som ger det önskade resultatet. 

> [!Note] 
> Azure Search använder Lucene för textsökning men Lucene integrering är inte komplett. Vi selektivt exponera och utöka Lucene funktioner för att aktivera scenarierna som är viktigt att Azure Search. 

## <a name="architecture-overview-and-diagram"></a>Översikt över arkitektur och diagram

Bearbetning av en fulltext-sökning börjar med texten frågan om du vill extrahera sökvillkoren. Sökmotorn använder ett index för att hämta dokument med matchande villkor. Enskilda sökord är ibland uppdelade och färdigställts till nya formulär för att omvandla ett bredare net över vad kan betraktas som en möjlig matchning. Resultatet sorteras sedan efter en relevans poäng som tilldelats varje enskilt matchande dokument. De överst i rankningslista returneras till det anropande programmet.

Frågekörningen har räknas, fyra steg: 

1. Frågan parsning 
2. Lexikaliskt analys 
3. Hämta dokument 
4. Resultatfunktioner 

Diagrammet nedan illustrerar de komponenter som används för att bearbeta en sökbegäran. 

 ![Arkitekturdiagram för Lucene frågan i Azure Search][1]


| Nyckelkomponenter | Funktionsbeskrivning | 
|----------------|------------------------|
|**Frågan Parser** | Separata sökord från frågeoperatorer och skapa fråga-struktur (en fråga trädet) som ska skickas till sökmotorn. |
|**Analyzers** | Analysera lexikala sökord. Den här processen kan omfatta Omforma, ta bort eller expandering av sökord. |
|**Index** | En effektiv datastruktur som används för att lagra och organisera sökbara villkoren som har extraherats från indexerade dokument. |
|**Sökmotor** | Hämtar och poäng matchande dokument baserat på innehållet i det omvända indexet. |

## <a name="anatomy-of-a-search-request"></a>En sökbegäran uppbyggnad

En sökbegäran är en fullständig specifikation av vad som ska returneras i en resultatuppsättning. I enklaste form är en tom fråga med några villkor av något slag. En mer realistisk exempel innehåller parametrar, flera sökord kanske är begränsade till vissa fält med eventuellt filteruttrycket och ordning regler.  

Följande exempel är en sökbegäran som du kan skicka till Azure Search med hjälp av den [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2016-09-01 
{  
    "search": "Spacious, air-condition* +\"Ocean view\"",  
    "searchFields": "description, title",  
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",  
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
 } 
~~~~

För denna begäran gör sökmotorn följande:

1. Filtrerar ut dokument där priset är minst $60 och mindre än 300 USD.
2. Kör frågan. I det här exemplet sökfrågan består av fraser och villkor: `"Spacious, air-condition* +\"Ocean view\""` (användarna vanligtvis inte ange skiljetecken, men inklusive exempel kan vi förklarar hur analyzers hantera). För den här frågan sökmotorn igenom beskrivningen och rubrikfält anges i `searchFields` för dokument som innehåller ”oceanen vyn”, och dessutom på termen ”stora”, eller på villkor som börjar med prefixet ”air-condition”. Den `searchMode` används för att matcha något villkor (standard) eller alla, i de fall där en term som inte krävs uttryckligen (`+`).
3. Sorterar den resulterande uppsättningen hotell av närhet till en viss geografisk plats och sedan tillbaka till det anropande programmet. 

Merparten av den här artikeln handlar om bearbetningen av den *sökfråga*: `"Spacious, air-condition* +\"Ocean view\""`. Filtrera och sortera ligger utanför omfånget. Mer information finns i [Sök API-referensdokumentation](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Steg 1: Frågan parsning 

Enligt nedanstående är frågesträngen den första raden för begäran: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

Frågeparsern avgränsar operatorer (t.ex `*` och `+` i exemplet) från sökning termer och deconstructs frågan i *underfrågor* av en typ som stöds: 

+ *Termen frågan* för fristående villkor (till exempel stora)
+ *frasen frågan* för angiven villkor (till exempel visa oceanen)
+ *prefixet frågan* för termer följt av ett prefix-operatorn `*` (t.ex. air-condition)

En fullständig lista över stöds frågetyper finns [Lucene frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operatörer som är associerade med en underfråga avgöra om frågan ”måste vara” eller ”ska vara” uppfyllas för att ett dokument för att ses som en matchning. Till exempel `+"Ocean view"` är ”måste” på grund av att den `+` operator. 

Frågeparsern omstrukturerar underfrågor i en *frågan trädet* (en intern struktur som representerar frågan) förmedlar sökmotor. I det första steget i frågan parsning trädet frågan ser ut så här.  

 ![Boolesk fråga searchmode alla][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Stöd för Parser: enkelt och fullständig Lucene 

 Azure Search visar två olika frågespråk `simple` (standard) och `full`. Genom att ange den `queryType` parameter med din begäran, anger du frågeparsern vilka frågespråket du väljer så att den vet hur du tolkar operatorer och syntax. Den [enkel frågespråket](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) är intuitivt och stabil, ofta lämpligt att tolka indata från användaren som-är utan klientsidan bearbetning. Det stöder frågeoperatorer bekant från sökmotorer. Den [fullständig Lucene frågespråk](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), som du får genom att ange `queryType=full`, utökar standardspråk för enkel fråga genom att lägga till stöd för flera operatorer och frågetyper som jokertecken, fuzzy regex och fältet omfång frågor. Till exempel skulle ett reguljärt uttryck som skickas i enkla frågesyntaxen tolkas som en frågesträng och inte ett uttryck. Exempelbegäran i den här artikeln använder frågespråket fullständig Lucene.

### <a name="impact-of-searchmode-on-the-parser"></a>Effekten av searchMode i tolken 

En annan begäran sökparameter som påverkar parsning är den `searchMode` parameter. Den styr operatorn standard för booleskt frågor: en (standard) eller alla.  

När `searchMode=any`, som är standard, utrymme avgränsare mellan stora och air-condition är eller (`||`), gör frågan exempeltexten motsvarar: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Explicit operatorer som `+` i `+"Ocean view"`, är entydigt i Boolesk fråga konstruktion (termen *måste* matchar). Lika självklara är hur du tolkar återstående villkoren: stora och air-condition. Bör sökfunktionen hitta matchningar för oceanen vyn *och* stora *och* air-condition? Eller bör hitta oceanen visa plus *antingen en* återstående villkor? 

Som standard (`searchMode=any`), sökmotorn förutsätter bredare tolkning. Antingen fältet *bör* matchas, reflektion ”eller” semantik. Första fråga trädet visas tidigare, med två ”bör” operations, visas standardinställningarna.  

Anta att vi nu ställer `searchMode=all`. I det här fallet tolkas området som en ”och”-åtgärd. Var och en av de återstående villkor måste båda vara finns i dokumentet så att en matchning. Den resulterande exempelfråga skulle tolkas enligt följande: 

~~~~
+Spacious,+air-condition*+"Ocean view"  
~~~~

Ett ändrade frågan träd för den här frågan skulle vara följande, där en matchande dokumentet är skärningspunkten för alla tre underfrågor: 

 ![Boolesk fråga searchmode alla][3]

> [!Note] 
> Om du väljer `searchMode=any` över `searchMode=all` beslut bästa erhålls genom att köra representativt frågor. Användare som är sannolikt att innefatta operatorer (common när sökning dokumentet lagrar) kan vara resultatet intuitivt om `searchMode=all` informerar Boolesk fråga konstruktioner. Mer information om samspelet mellan `searchMode` och operatorer, se [enkel frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Steg 2: Lexikaliskt analys 

Lexikaliskt analyzers processen *term frågor* och *fras frågor* när frågan trädet är strukturerad. En analyzer accepterar text indata som anges av tolken, bearbetar text och sedan skickar tillbaka tokeniserad villkoren ingå i trädet frågan. 

Den vanligaste formen av lexikala analys är *språkliga analysis* som transformeringar fråga villkor baserat på regler som är specifika för ett visst språk: 

* Att minska en frågeterm rot form av ett ord. 
* Ta bort irrelevanta ord (stoppord, till exempel ”i” eller ”och” på engelska) 
* Dela upp ett sammansatt ord i komponenter 
* Lägre skiftläge ett ord med versal 

Alla dessa åtgärder tenderar att radera skillnader mellan text indata som angetts av användaren och villkoren lagras i indexet. Dessa åtgärder utöver text bearbetning och kräver avancerade kunskaper om språket sig själv. Om du vill lägga till det här lagret av språkliga medvetenhet Azure Search har stöd för en lång lista med [språkanalys](https://docs.microsoft.com/rest/api/searchservice/language-support) från både Lucene och Microsoft.

> [!Note]
> Analys-kraven kan variera mellan minimal till avancerade beroende på ditt scenario. Du kan styra komplexitet lexikala analys genom att välja något av de fördefinierade analyzers eller skapa egna [anpassade analyzer](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analyzers är begränsade till sökbara fält och anges som en del av en fältdefinition av. På så sätt kan du variera lexikala analys på grundval av per fält. Måste den *standard* Lucene analyzer används.

I vårt exempel före analys, har trädet första fråga termen ”Spacious” med versaler ”S” och ett kommatecken frågeparsern tolkas som en del av frågeterm (kommatecken inte anses vara ansvarig query language).  

När standard analyzer bearbetar termen, kommer den gemener ”oceanen view” och ”stora” och ta bort tecknet kommatecken. Trädet ändrade frågan ska se ut så här: 

 ![Boolesk fråga med analyserade villkor][4]

### <a name="testing-analyzer-behaviors"></a>Testa analyzer beteenden 

Beteendet för en analyzer kan testas med hjälp av den [analysera API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Ange den text som du vill analysera för att se villkoren som angetts analyzer genererar. Om du vill se hur standard analyzer skulle bearbetas i texten ”air-condition”, till exempel kan du utfärda följande begäran:

~~~~
{ 
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

Standard analyzer delar indatatexten i följande två variabler, lägga till anteckningar med attribut som start och end förskjutningarna (används för träffar syntaxmarkering) samt deras position (används för frasen matchar):

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

### <a name="exceptions-to-lexical-analysis"></a>Undantag till lexikala analys 

Lexikaliskt analys gäller enbart för frågetyper som kräver fullständiga villkor – en term fråga eller en frasfråga. Den gäller inte frågetyper med ofullständiga villkor – prefix fråga, jokerteckenfråga med, regex frågan – eller en fuzzy fråga. De fråga typer, inklusive prefix frågan med termen *air-condition\**  i vårt exempel läggs direkt till trädet frågan kringgå fasen analys. Endast omvandling utförs på sökord dessa typer av lowercasing.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Steg 3: Hämta för dokument 

Hämta dokument refererar till att söka efter dokument med matchande termer i indexet. Det här steget är att förstå bäst genom ett exempel. Låt oss börja med ett hotell index med följande enkla schema: 

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

Anta vidare att indexet innehåller följande fyra dokument: 

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

För att förstå hämtning, hjälper det att du känner till några grunderna om indexering. Lagringsenheten är en inverterad index, en för varje sökbara fält. Är en sorterad lista över alla villkor från alla dokument i ett inverterad index. Varje term mappar i listan över dokument som det uppstår, som tydligt i exemplet nedan.

För att skapa villkoren i inverterad index utför sökmotorn lexikala analys över innehållet i dokument som liknar vad som händer under frågebearbetningen:

1. *Text indata* skickas till en analyzer lägre-alltid demontering av skiljetecken och så vidare, beroende på hur analyzer. 
2. *Token* är resultatet av text analys.
3. *Villkoren* läggs till i indexet.

Det är vanligt, men krävs inte, att använda samma analyzers för sökning och indexering åtgärder så att sökord påminner mer villkoren i indexet.

> [!Note]
> Azure Search kan du ange olika analyzers för indexering och söka via ytterligare `indexAnalyzer` och `searchAnalyzer` fältet parametrar. Om inget anges analyzer anges med den `analyzer` egenskapen används för både indexering och sökning.  

**Omvända index för dokument**

Gå tillbaka till våra exempel, för den **rubrik** fältet inverterad indexet ser ut så här:

| Period | Listan över |
|------|---------------|
| atman | 1 |
| stranden | 2 |
| hotell | 1, 3 |
| oceanen | 4  |
| playa | 3 |
| utväg | 3 |
| Återställ format | 4 |

I rubrikfältet, endast *hotell* visas i två dokument: 1, 3.

För den **beskrivning** fältet indexet är följande:

| Period | Listan över |
|------|---------------|
| luften | 3
| och | 4
| stranden | 1
| villkor | 3
| fria | 3
| Avstånd | 1
| dataö | 2
| kauaʻi | 2
| finns | 2
| Nord | 2
| oceanen | 1, 2, 3
| av | 2
| på |2
| Tyst | 4
| lokaler  | 1, 3
| secluded | 4
| land | 2
| stora | 1
| den | 1, 2
| till | 1
| vy | 1, 2, 3
| Gå | 1
| med | 3


**Matchande sökord mot indexerade ord**

Omvända indexen ovan, vi gå tillbaka till exempel frågan och se hur matchande dokument hittades för våra exempelfråga. Kom ihåg att trädet slutlig ser ut så här: 

 ![Boolesk fråga med analyserade villkor][4]

Vid körning av fråga körs enskilda frågor mot sökbara fält oberoende av varandra. 

+ TermQuery, ”stora” matchar dokumentera 1 (hotell Atman). 

+ PrefixQuery ”, air-condition *”, matchar inte några dokument. 

  Det här är ett beteende som ibland confuses utvecklare. Även om termen luftkonditionerad finns i dokumentet, är den uppdelad i två termer som standard analyzer. Återkalla inte analyseras prefix frågor som innehåller partiella villkoren. Därför med prefixet ”air-condition” letas upp i inverterad indexet och det gick inte att hitta.

+ PhraseQuery, ”oceanen vyn”, letar upp villkoren ”oceanen” och ”visa” och kontrollerar närheten av villkoren i det ursprungliga dokumentet. Den här frågan i beskrivningsfältet överensstämmer dokument 1, 2 och 3. Lägg märke till dokumentet 4 har termen oceanen i rubriken men inte anses vara en matchning som vi letar efter frasen ”oceanen view” i stället för enskilda ord. 

> [!Note]
> En sökning körs oberoende mot alla sökbara fält i Azure Search-index om du inte begränsar de fält som anges med den `searchFields` parameter, enligt beskrivningen i sökbegäran exempel. Dokument som matchar i någon av de markerade fälten returneras. 

På hela är dokument som matchar för den aktuella frågan 1, 2, 3. 

## <a name="stage-4-scoring"></a>Steg 4: poängsättning  

Alla dokument i ett sökresultat tilldelas en relevans poäng. Funktionen för den relevanta poängen är att högre de dokument som bäst svarar på en fråga användaren återgavs av frågan. Poängsättningen beräknas utifrån statistiska egenskaper för termer som matchas. Kärnan i bedömningsprofil formeln är [TF/IDF (termen frekvens inversen dokumentet frekvens)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). I frågor som innehåller sällsynt och gemensamma villkor, främjar TF/IDF resultat som innehåller sällsynta termen. Till exempel i ett hypotetiskt index med alla Wikipedia artiklar från dokument som matchade frågan *VD*, dokument som matchar på *VD* anses vara mer relevant än dokumenten matchar på *i*.


### <a name="scoring-example"></a>Bedömningsprofil exempel

Återkalla tre dokument som matchade våra exempelfråga:
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

Dokumentet 1 matchar bäst frågan eftersom båda termen *stora* och nödvändiga frasen *oceanen visa* uppstå i beskrivningsfältet. Följande två dokument matchar endast frasen *oceanen visa*. Du kanske konstigt att relevanta poängsättningen för dokument 2 och 3 är olika trots att de matchar frågan på samma sätt. Det beror på att bedömningsprofil formeln har fler komponenter än bara TF/IDF. I det här fallet har dokumentet 3 tilldelats en något högre poäng eftersom dess beskrivning är kortare. Lär dig mer om [Lucenes praktiska bedömningen formeln](https://lucene.apache.org/core/4_0_0/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) att förstå hur fältlängden och andra faktorer kan påverka poängsättningen betydelse.

Vissa fråga typer (med jokertecken, prefix, regex) alltid bidra med en konstant poäng att hela dokumentet poängen. Detta gör att matchningar via frågan expansion ska tas med i resultatet, men utan att påverka rangordning. 

Ett exempel visar varför det är viktigt. Jokertecken, inklusive prefix sökningar är tvetydig per definition eftersom indata är en partiell sträng med potentiella matchar på ett mycket stort antal olika villkor (överväga indata av ”rundtur *” med matchningar hittades för ”visningar”, ”tourettes”, och ” tourmaline ”). Eftersom de här resultaten returneras, går det inte att härleda rimligen vilket är mer värdefull än andra. Därför bör Ignorera vi termen frekvenser när poängberäkningen resultat i frågor för typer med jokertecken, prefix och regex. I en flerdelade sökbegäran som innehåller begränsade och fullständiga termer inbyggda resultat från partiella indata med en konstant poäng att undvika att rikta mot potentiellt oväntat matchar.

### <a name="score-tuning"></a>Poäng justera

Det finns två sätt att justera relevans resultat i Azure Search:

1. **Bedömningen profiler** befordra dokument i rankningslista över resultat baserat på en uppsättning regler. I vårt exempel kan vi anser att dokument som matchade mer relevant än de dokument som matchas i beskrivningsfältet fält. Om indexet har ett fält för varje hotell, kan vi dessutom befordra dokument med lägre pris. Lär dig mer [lägga till bedömningen profiler i en sökindex.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Term den** (endast tillgängligt i frågesyntaxen fullständig Lucene) innehåller en operatorn som `^` som kan tillämpas på någon del av trädet frågan. I vårt exempel, i stället för att söka i prefixet *air-condition*\*, en kan söka efter antingen exakta termen *air-condition* eller prefix, men dokument som matchar exakt termen är rangordnas högre genom att använda förstärkningen frågan termen: *luften villkoret ^ 2. AIR-condition**. Lär dig mer om [term den](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Poängberäkningen i en distribuerad index

Alla index i Azure Search automatiskt är uppdelade i flera delar, så att oss att snabbt distribuera index mellan flera noder under tjänsten skala upp eller ned. När en sökbegäran utfärdas mot varje Fragmentera oberoende av varandra. Resultaten från varje Fragmentera sedan samman och sorterade efter poäng (om ingen ordning har definierats). Det är viktigt att veta att bedömningsprofil funktionen vikterna fråga termen frekvens mot inverterade dokumentet frekvensen i alla dokument i Fragmentera, inte på alla shards!

Detta innebär en relevans poäng *kan* vara olika för identiska dokument om de finns på olika delar. Lyckligtvis tenderar skillnaderna att försvinna när antalet dokument i indexet växer på grund av flera termen fördelas jämnt. Det går inte att anta på vilka Fragmentera alla dokumentet kommer att placeras. Dock tilldelas förutsatt en Dokumentnyckel inte ändras den alltid samma Fragmentera.

I allmänhet är dokumentet poäng inte det bästa attributet för ordning dokument om stabiliteten för ordning är viktig. Till exempel är anges två dokument med en identisk poäng, det inte säkert vilken visas först i efterföljande körningar av samma fråga. Dokumentet poäng bör bara ge en allmän uppfattning om dokumentet betydelse i förhållande till andra dokument i resultatuppsättningen av.

## <a name="conclusion"></a>Slutsats

Genomförandet av sökmotorer på internet har signalerat förväntningar för textsökning över privata data. För nästan alla typer av sökinställningar planerar vi nu motorn att förstå våra avsikt, även när villkoren är felstavat eller är ofullständig. Vi tror även matchar baserat på nära motsvarande uttryck eller synonymer som vi aldrig faktiskt har angetts.

Fulltextsökning är mycket komplex som kräver avancerad språkliga analys och systematiskt för bearbetning på ett sätt som destillera, expandera och transformera sökord att leverera relevant resultat från en teknisk synvinkel. Inbyggd svårigheter får finns det många faktorer som kan påverka resultatet av en fråga. Därför erbjuder investera reda på säkerhetsnivån fulltextsökning faktiska fördelar när du försöker använda oväntade resultat.  

Den här artikeln utforskade fulltextsökning i samband med Azure Search. Vi hoppas att du får tillräckligt bakgrund att identifiera möjliga orsaker och lösningar för adressering vanliga problem med frågan. 

## <a name="next-steps"></a>Nästa steg

+ Skapa exempel index, prova olika frågor och granska resultatet. Instruktioner finns i [bygga och fråga ett index i portalen](search-get-started-portal.md#query-index).

+ Försök ytterligare frågesyntaxen från den [Sök dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) exempel avsnittet eller från [enkel frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) i Sök explorer i portalen.

+ Granska [bedömningen profiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) om du vill justera rangordning i sökprogram.

+ Lär dig hur du använder [språkspecifika lexikala analyzers](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurera anpassade analyzers](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) för minimal bearbetning eller särskilda bearbetning på specifika fält.

+ [Jämför standard och engelska analyzers](http://alice.unearth.ai/)) sida vid sida på den här demo-webbplatsen. 

## <a name="see-also"></a>Se även

[Sök dokument REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Fullständig Lucene frågesyntaxen](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Hantera sökresultat](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
