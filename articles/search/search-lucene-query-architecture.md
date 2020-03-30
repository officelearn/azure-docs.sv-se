---
title: Arkitektur för fulltextfråga och indexering av motor (Lucene)
titleSuffix: Azure Cognitive Search
description: Undersöker Lucene-frågebearbetning och dokumenthämtningsbegrepp för fulltextsökning, som relaterade till Azure Cognitive Search.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d46d0309b3d2ffb638016e88ba022e49009eedf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282944"
---
# <a name="how-full-text-search-works-in-azure-cognitive-search"></a>Så här fungerar fulltextsökning i Azure Cognitive Search

Den här artikeln är för utvecklare som behöver en djupare förståelse för hur Lucene fulltextsökning fungerar i Azure Cognitive Search. För text frågor ger Azure Cognitive Search sömlöst förväntade resultat i de flesta fall, men ibland kan du få ett resultat som ser lite konstigt ut. I dessa fall är det bra att känna till de fyra stegen i Lucene-frågekörning (frågeparsning, lexikal analys, dokumentmatchning och poängsättning) när du vill identifiera vissa ändringar av frågeparametrar eller indexkonfiguration som ger önskat resultat. 

> [!Note] 
> Azure Cognitive Search använder Lucene för fulltextsökning, men Lucene-integrering är inte uttömmande. Vi exponerar och utökar lucene-funktionen selektivt för att aktivera scenarier som är viktiga för Azure Cognitive Search. 

## <a name="architecture-overview-and-diagram"></a>Arkitekturöversikt och diagram

Bearbetning av en fulltextsökfråga börjar med att frågetexten analyseras för att extrahera söktermer. Sökmotorn använder ett index för att hämta dokument med matchande termer. Enskilda frågetermer delas ibland upp och ombildas i nya former för att kasta ett bredare nät över vad som kan betraktas som en potentiell matchning. En resultatuppsättning sorteras sedan efter en relevanspoäng som tilldelas varje enskilt matchande dokument. De högst upp på den rankade listan returneras till det anropande programmet.

Omräknat har frågekörning fyra steg: 

1. Frågetolsning 
2. Lexikal analys 
3. Hämtning av dokument 
4. Resultat 

Diagrammet nedan illustrerar de komponenter som används för att bearbeta en sökbegäran. 

 ![Lucene frågearkitekturdiagram i Azure Cognitive Search][1]


| Nyckelkomponenter | Funktionell beskrivning | 
|----------------|------------------------|
|**Frågetolkare** | Separera frågetermer från frågeoperatorer och skapa frågestrukturen (ett frågeträd) som ska skickas till sökmotorn. |
|**Analysverktyg** | Utför lexikal analys på frågetermer. Den här processen kan innebära att frågetermer omvandlas, tas bort eller utökas. |
|**Index** | En effektiv datastruktur som används för att lagra och organisera sökbara termer som extraherats från indexerade dokument. |
|**Sökmotor** | Hämtar och poängmatchar dokument baserat på innehållet i det inverterade indexet. |

## <a name="anatomy-of-a-search-request"></a>Anatomy of a Search Request

En sökbegäran är en fullständig specifikation av vad som ska returneras i en resultatuppsättning. I enklast form är det en tom fråga utan några villkor av något slag. Ett mer realistiskt exempel innehåller parametrar, flera frågetermer, kanske begränsade till vissa fält, med eventuellt ett filteruttryck och ordningsregler.  

Följande exempel är en sökbegäran som du kan skicka till Azure Cognitive Search med [REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents).  

~~~~
POST /indexes/hotels/docs/search?api-version=2019-05-06
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

1. Filtrerar bort dokument där priset är minst $ 60 och mindre än $ 300.
2. Kör frågan. I det här exemplet består sökfrågan av `"Spacious, air-condition* +\"Ocean view\""` fraser och termer: (användare anger vanligtvis inte skiljetecken, men genom att inkludera den i exemplet kan vi förklara hur analysatorer hanterar den). För den här frågan söker sökmotorn igenom beskrivnings- och rubrikfälten som anges i `searchFields` dokument som innehåller "Ocean view", och dessutom på termen "rymlig", eller på termer som börjar med prefixet "luftkonditionering". Parametern `searchMode` används för att matcha på alla termer (standard) eller alla av`+`dem, för fall där en term inte uttryckligen krävs ( ).
3. Beställer den resulterande uppsättningen hotell efter närhet till en viss geografiplats och returneras sedan till det anropande programmet. 

Majoriteten av den här artikeln handlar om `"Spacious, air-condition* +\"Ocean view\""`bearbetning av *sökfrågan:*. Filtrering och beställning är utanför omfånget. Mer information finns i [referensdokumentationen för sök-API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Steg 1: Tolkning av frågor 

Som nämnts är frågesträngen den första raden i begäran: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

Frågetolken separerar operatorer (till `*` `+` exempel och i exemplet) från söktermer och dekonstruerar sökfrågan till *underfrågor* av en typ som stöds: 

+ *termfråga* för fristående termer (som rymliga)
+ *frasfråga* för citerade termer (som havsvy)
+ *prefixfråga* för termer följt av `*` en prefixoperatör (t.o.d. luftkonditionering)

En fullständig lista över frågetyper som stöds finns [i syntaxen för Frågan för Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)

Operatorer som är associerade med en underfråga avgör om frågan "måste vara" eller "ska vara" nöjd för att ett dokument ska betraktas som en matchning. Till exempel `+"Ocean view"` är "måste" `+` på grund av operatören. 

Frågetolken omstrukturerar underfrågorna till ett *frågeträd* (en intern struktur som representerar frågan) som skickas vidare till sökmotorn. I det första steget i frågetolningen ser frågeträdet ut så här.  

 ![Boolesk fråga sökmode någon][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Tolkar som stöds: Enkel och fullständig Lucene 

 Azure Cognitive Search visar två `simple` olika frågespråk `full`(standard) och . Genom att `queryType` ställa in parametern med din sökbegäran anger du frågetolken vilket frågespråk du väljer så att den vet hur operatorerna och syntaxen ska tolkas. Det [enkla frågespråket](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) är intuitivt och robust, ofta lämpligt att tolka användarindata som är utan bearbetning på klientsidan. Den stöder frågeoperatörer som är bekanta från sökmotorer. [Frågespråket Full Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), som `queryType=full`du får genom att ange , utökar standardspråket Enkel fråga genom att lägga till stöd för fler operatorer och frågetyper som jokertecken, fuzzy, regex och fältomfattade frågor. Ett reguljärt uttryck som skickas i syntaxen enkel fråga tolkas till exempel som en frågesträng och inte ett uttryck. Exempelbegäran i den här artikeln använder frågespråket Full Lucene.

### <a name="impact-of-searchmode-on-the-parser"></a>Inverkan av sökningMode på tolken 

En annan sökbegäran parameter som `searchMode` påverkar tolkning är parametern. Den styr standardoperatorn för booleska frågor: alla (standard) eller alla.  

När `searchMode=any`, som är standard, utrymmet avgränsare mellan rymliga och`||`luftkonditionering är ELLER ( ), vilket gör exempelfrågetexten motsvarar: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Explicita operatorer, `+` `+"Ocean view"`till exempel i , är entydiga i booleska frågekonstruktion (termen *måste* matcha). Mindre uppenbart är hur man ska tolka de återstående termerna: rymliga och luftkonditionering. Bör sökmotorn hitta tändstickor på havsutsikt *och* rymliga *och* luftkonditionering? Eller ska den hitta havsutsikt plus *antingen en* av de återstående villkoren? 

Som standard`searchMode=any`( ) antar sökmotorn den bredare tolkningen. Båda *fälten ska* matchas, vilket återspeglar "eller" semantik. Det första frågeträdet som illustrerats tidigare, med de två "bör"-åtgärderna, visar standardvärdet.  

Anta att vi `searchMode=all`nu har ställt in . I det här fallet tolkas utrymmet som en "och"-åtgärd. Var och en av de återstående villkoren måste båda finnas i dokumentet för att kvalificera sig som en matchning. Den resulterande exempelfrågan skulle tolkas på följande sätt: 

~~~~
+Spacious,+air-condition*+"Ocean view"
~~~~

Ett modifierat frågeträd för den här frågan skulle vara följande, där ett matchande dokument är skärningspunkten mellan alla tre underfrågorna: 

 ![Boolesk fråga sökmode alla][3]

> [!Note] 
> Att `searchMode=any` `searchMode=all` välja över är ett beslut som bäst uppnås genom att köra representativa frågor. Användare som sannolikt kommer att inkludera operatorer (vanliga när du `searchMode=all` söker i dokumentarkiv) kan hitta resultat mer intuitiva om de informerar booleska frågekonstruktioner. Mer information om samspelet mellan `searchMode` och operatorerna finns i Enkel [frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Steg 2: Lexikal analys 

Lexikala analysatorer bearbetar *termfrågor* och *frasfrågor* när frågeträdet har strukturerats. En analysator accepterar de textindata som ges till den av tolken, bearbetar texten och skickar sedan tillbaka tokeniserade termer som ska infogas i frågeträdet. 

Den vanligaste formen av lexikal analys är *språklig analys* som omvandlar frågetermer baserat på regler som är specifika för ett visst språk: 

* Minska en frågeterm till rotformen för ett ord 
* Ta bort icke-väsentliga ord (stopwords, till exempel "den" eller "och" på engelska) 
* Dela upp ett sammansatt ord i komponenter 
* Sänk höljet i versaler 

Alla dessa åtgärder tenderar att radera skillnader mellan textindata som tillhandahålls av användaren och de termer som lagras i indexet. Sådana åtgärder går utöver textbehandling och kräver fördjupade kunskaper i själva språket. För att lägga till det här lagret av språklig medvetenhet stöder Azure Cognitive Search en lång lista med [språkanalysatorer](https://docs.microsoft.com/rest/api/searchservice/language-support) från både Lucene och Microsoft.

> [!Note]
> Analyskraven kan variera från minimal till detaljerade beroende på ditt scenario. Du kan styra komplexiteten i lexikal analys genom att välja en av de fördefinierade analysatorerna eller genom att skapa din egen [anpassade analysator](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analysatorer är begränsade till sökbara fält och anges som en del av en fältdefinition. På så sätt kan du variera lexikal analys per fält. Ospecificerad *standard* används lucenanalysatorn.

I vårt exempel, före analys, har det första frågeträdet termen "Rymlig", med ett versaler "S" och ett kommatecken som frågetolkaren tolkar som en del av frågetermen (ett kommatecken betraktas inte som en frågespråksoperator).  

När standardanalysatorn bearbetar termen, kommer det att ge gemener "havsvy" och "rymliga", och ta bort kommatecknet tecken. Det ändrade frågeträdet ser ut så här: 

 ![Boolesk fråga med analyserade termer][4]

### <a name="testing-analyzer-behaviors"></a>Testa analysatorbeteenden 

Beteendet för en analysator kan testas med hjälp av [Analysera API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). Ange den text du vill analysera för att se vilka termer som ges analysator kommer att generera. Om du till exempel vill se hur standardanalysatorn skulle bearbeta texten "luftkonditionering" kan du utfärda följande begäran:

~~~~
{
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

Standardanalysatorn bryter indatatexten i följande två tokens och kommenterar dem med attribut som start- och slutförskjutningar (används för träffmarkering) samt deras position (används för frasmatchning):

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

### <a name="exceptions-to-lexical-analysis"></a>Undantag från lexikal analys 

Lexikal analys gäller endast frågetyper som kräver fullständiga termer – antingen en termfråga eller en frasfråga. Det gäller inte frågetyper med ofullständiga termer – prefixfråga, jokerteckenfråga, regex-fråga – eller en suddig fråga. Dessa frågetyper, inklusive prefixfrågan `air-condition*` med termen i vårt exempel, läggs till direkt i frågeträdet och kringgår analysfasen. Den enda omvandling som utförs på frågevillkor för dessa typer är att sänka.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Steg 3: Hämtning av dokument 

Med dokumenthämtning avses att hitta dokument med matchande termer i indexet. Detta skede förstås bäst genom ett exempel. Låt oss börja med ett hotellindex som har följande enkla schema: 

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

Anta vidare att detta index innehåller följande fyra dokument: 

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

**Hur termer indexeras**

För att förstå hämtning, hjälper det att veta några grunderna om indexering. Lagringsenheten är ett inverterat index, ett för varje sökbart fält. Inom ett inverterat index finns en sorterad lista över alla termer från alla dokument. Varje term mappas till listan över dokument där den förekommer, vilket framgår av exemplet nedan.

För att producera termerna i ett inverterat index utför sökmotorn lexikal analys över innehållet i dokument, liknande vad som händer under frågebearbetning:

1. *Textindata* skickas till en analysator, gemener, fråntagna skiljetecken och så vidare, beroende på analysatorkonfigurationen. 
2. *Tokens* är utdata från textanalys.
3. *Villkor* läggs till i indexet.

Det är vanligt, men inte obligatoriskt, att använda samma analysatorer för sök- och indexeringsåtgärder så att frågetermer ser mer ut som termer i indexet.

> [!Note]
> Med Azure Cognitive Search kan du ange olika `indexAnalyzer` analysatorer för indexering och sökning via ytterligare parametrar och `searchAnalyzer` fältparametrar. Om det inte anges används `analyzer` analysatoruppsättningen med egenskapen för både indexering och sökning.  

**Inverterat index till exempel dokument**

För att återgå till **title** vårt exempel ser det inverterade indexet ut så här:

| Period | Dokumentlista |
|------|---------------|
| Atman | 1 |
| Beach | 2 |
| Hotel | 1, 3 |
| Ocean | 4  |
| Playa | 3 |
| Resort | 3 |
| Retreat | 4 |

I titelfältet visas endast *hotell* i två dokument: 1, 3.

För **beskrivningsfältet** är indexet följande:

| Period | Dokumentlista |
|------|---------------|
| Luft | 3
| och | 4
| Beach | 1
| Villkorade | 3
| Bekväma | 3
| avstånd | 1
| Ön | 2
| kaua'i | 2
| Belägna | 2
| north | 2
| Ocean | 1, 2, 3
| av | 2
| på |2
| Lugnt | 4
| Rum  | 1, 3
| Avskilda | 4
| Stranden | 2
| Rymliga | 1
| Det | 1, 2
| till | 1
| visa | 1, 2, 3
| Promenader | 1
| med | 3


**Matcha frågetermer mot indexerade termer**

Med tanke på de inverterade indexen ovan ska vi gå tillbaka till exempelfrågan och se hur matchande dokument hittas för vår exempelfråga. Kom ihåg att det slutliga frågeträdet ser ut så här: 

 ![Boolesk fråga med analyserade termer][4]

Under frågekörning körs enskilda frågor mot sökbara fält oberoende av detta. 

+ TermQuery, "rymlig", matchar dokument 1 (Hotel Atman). 

+ Prefixquery, "luftkonditionering*", matchar inte några dokument. 

  Detta är ett beteende som ibland förvirrar utvecklare. Även om termen luftkonditionerade finns i dokumentet, är det uppdelad i två termer av standardanalysatorn. Återkalla att prefixfrågor, som innehåller partiella termer, inte analyseras. Därför termer med prefixet "luftkonditionering" är tittade upp i inverterade index och inte hittas.

+ Frasquery, "havsutsikt", slår upp termerna "ocean" och "visa" och kontrollerar närheten av termer i det ursprungliga dokumentet. Dokument 1, 2 och 3 matchar den här frågan i beskrivningsfältet. Meddelande dokument 4 har termen havet i titeln men anses inte vara en match, eftersom vi letar efter "ocean view" fras snarare än enskilda ord. 

> [!Note]
> En sökfråga körs oberoende av alla sökbara fält i Azure Cognitive Search-indexet `searchFields` om du inte begränsar fälten som angetts med parametern, vilket illustreras i exempelsökbegäran. Dokument som matchar i något av de markerade fälten returneras. 

På det hela taget, för frågan i fråga, de dokument som matchar är 1, 2, 3. 

## <a name="stage-4-scoring"></a>Steg 4: Poängsättning  

Varje dokument i en sökresultatuppsättning tilldelas ett relevansresultat. Relevanspoängens funktion är att rangordna högre de dokument som bäst besvarar en användarfråga som uttrycks av sökfrågan. Poängen beräknas baserat på statistiska egenskaper för termer som matchade. Kärnan i bedömningsformeln är [TF/IDF (termfrekvens-omvänd dokumentfrekvens)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). I frågor som innehåller sällsynta och vanliga termer, TF/IDF främjar resultat som innehåller den sällsynta termen. Till exempel, i ett hypotetiskt index med alla Wikipedia artiklar, från dokument som matchade frågan *presidenten*, dokument som matchar på *president* anses vara mer relevanta än dokument som matchar *på*.


### <a name="scoring-example"></a>Exempel på poängsättning

Återkalla de tre dokument som matchade vår exempelfråga:
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

Dokument 1 matchade frågan bäst eftersom både termen *rymlig* och den fras som krävs *visas* i beskrivningsfältet. De följande två dokumenten matchar bara frasen *havsutsikt*. Det kan vara förvånande att relevanspoängen för dokument 2 och 3 är annorlunda även om de matchade frågan på samma sätt. Det beror på att bedömningsformeln har fler komponenter än bara TF/IDF. I det här fallet tilldelades dokument 3 en något högre poäng eftersom beskrivningen är kortare. Lär dig mer om [Lucenes praktiska poängformel](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) för att förstå hur fältlängd och andra faktorer kan påverka relevanspoängen.

Vissa frågetyper (jokertecken, prefix, regex) bidrar alltid med en konstant poäng till den totala dokumentpoängen. Detta gör att matchningar som hittas genom frågeexpansionen inkluderas i resultaten, men utan att det påverkar rankningen. 

Ett exempel illustrerar varför detta är viktigt. Jokertecken sökningar, inklusive prefix sökningar, är tvetydiga per definition eftersom ingången är en partiell sträng med potentiella matcher på ett mycket stort antal olika termer (överväga en inmatning av "turné *", med matcher som finns på "turer", "tourettes", och " tourmaline"). Med tanke på dessa resultats natur finns det inget sätt att rimligen dra slutsatsen vilka termer som är mer värdefulla än andra. Därför ignorerar vi termfrekvenser när du poängsättning resulterar i frågor om typer av jokertecken, prefix och regex. I en sökbegäran i flera delar som innehåller partiella och fullständiga termer, ingår resultaten från den partiella indata med en konstant poäng för att undvika partiskhet mot potentiellt oväntade matchningar.

### <a name="score-tuning"></a>Poängjustering

Det finns två sätt att justera relevanspoäng i Azure Cognitive Search:

1. **Poängsättningsprofiler** marknadsför dokument i den rangordnade resultatlistan baserat på en uppsättning regler. I vårt exempel kan vi betrakta dokument som matchade i rubrikfältet mer relevanta än dokument som matchade i beskrivningsfältet. Dessutom, om vårt index hade ett prisfält för varje hotell, kunde vi främja dokument med lägre pris. Läs mer om hur du lägger till [poängprofiler i ett sökindex.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Term öka** (endast tillgänglig i fullständig Lucene fråga `^` syntax) ger en öka operator som kan tillämpas på någon del av frågeträdet. I vårt exempel, istället för att söka på prefixet *luftkonditionering,*\*kan man söka efter antingen den exakta termen *luftkonditionering* eller prefixet, men dokument som matchar på den exakta termen rankas högre genom att tillämpa uppsving på termen frågan: * luftkonditionering ^ 2 || luftkonditionering**. Läs mer om [term öka](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Poängsättning i ett distribuerat index

Alla index i Azure Cognitive Search delas automatiskt upp i flera shards, så att vi snabbt kan distribuera indexet mellan flera noder under serviceuppskalning upp eller skala ner. När en sökbegäran utfärdas utfärdas den mot varje fragment oberoende av varandra. Resultaten från varje shard slås sedan samman och sorteras efter poäng (om ingen annan ordning har definierats). Det är viktigt att veta att poängfunktionen vikter frågetermfrekvens mot dess omvända dokumentfrekvens i alla dokument inom fragmentet, inte över alla shards!

Detta innebär att en *relevanspoäng kan* vara annorlunda för identiska dokument om de finns på olika shards. Lyckligtvis tenderar sådana skillnader att försvinna när antalet dokument i indexet ökar på grund av jämnare termfördelning. Det går inte att anta vilken fragment som ett visst dokument ska placeras. Om du antar att en dokumentnyckel inte ändras tilldelas den alltid samma fragment.

I allmänhet är dokumentpoäng inte det bästa attributet för att beställa dokument om ordningsstabilitet är viktigt. Om du till exempel får två dokument med en identisk poäng finns det ingen garanti för vilken som visas först i efterföljande körningar av samma fråga. Dokumentpoäng bör endast ge en allmän känsla av dokumentrelevans i förhållande till andra dokument i resultatuppsättningen.

## <a name="conclusion"></a>Slutsats

Framgången för sökmotorer på Internet har höjt förväntningarna på fulltextsökning via privata data. För nästan alla typer av sökupplevelse, förväntar vi oss nu att motorn ska förstå vår avsikt, även när termerna är felstavade eller ofullständiga. Vi kan även förvänta oss matcher baserat på nästan likvärdiga termer eller synonymer som vi aldrig faktiskt anges.

Ur teknisk synvinkel är fulltextsökning mycket komplex, vilket kräver sofistikerad språkanalys och ett systematiskt tillvägagångssätt för bearbetning på ett sätt som destillerar, expanderar och omvandlar frågetermer för att leverera ett relevant resultat. Med tanke på den inneboende komplexiteten finns det många faktorer som kan påverka resultatet av en fråga. Av denna anledning, investera tid att förstå mekaniken i fulltext sökning erbjuder påtagliga fördelar när man försöker arbeta igenom oväntade resultat.  

I den här artikeln utforskades fulltextsökning i samband med Azure Cognitive Search. Vi hoppas att det ger dig tillräcklig bakgrund för att känna igen potentiella orsaker och lösningar för att ta itu med vanliga frågeproblem. 

## <a name="next-steps"></a>Nästa steg

+ Skapa exempelindex, prova olika frågor och granska resultat. Instruktioner finns [i Skapa och fråga ett index i portalen](search-get-started-portal.md#query-index).

+ Prova ytterligare frågesyntax från exempelavsnittet [Sök dokument](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) eller från [Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) i Sökutforskaren i portalen.

+ Granska [bedömningsprofiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) om du vill ställa in rankningen i sökprogrammet.

+ Läs om hur du använder [språkspecifika lexikala analysatorer](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Konfigurera anpassade analysatorer](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) för antingen minimal bearbetning eller specialiserad bearbetning på specifika fält.

## <a name="see-also"></a>Se även

[REST API för dokumentsökning](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Enkel frågesyntax](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Fullständig Lucene-frågesyntax](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Hantera sökresultat](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
