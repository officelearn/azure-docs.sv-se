---
title: Fuzzy-sökning
titleSuffix: Azure Cognitive Search
description: Implementera en "menade du" sökupplevelse för att automatiskt korrigera en felstavad term eller stavfel.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262442"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Fuzzy sökning för att korrigera felstavningar och stavfel

Azure Cognitive Search stöder fuzzy sökning, en typ av fråga som kompenserar för stavfel och felstavade termer i indatasträngen. Det gör detta genom att söka efter termer som har en liknande sammansättning. Expanderande sökning för att täcka nära-matcher har effekten av automatisk korrigering av ett stavfel när diskrepansen är bara några felplacerade tecken. 

## <a name="what-is-fuzzy-search"></a>Vad är fuzzy sökning?

Det är en expansion övning som ger en match på villkor som har en liknande sammansättning. När en suddig sökning anges bygger motorn ett diagram (baserat på [deterministisk ändlig automatonteori)](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)av liknande sammansatta termer, för alla hela termer i frågan. Om frågan till exempel innehåller tre termer "University of Washington" skapas `search=university~ of~ washington~` ett diagram för varje term i frågan (det finns ingen borttagning av stoppord i fuzzy search, så "of" får ett diagram).

Diagrammet består av upp till 50 expansioner, eller permutationer, för varje term, fånga både korrekta och felaktiga varianter i processen. Motorn returnerar sedan de mest relevanta matchningarna i svaret. 

För en term som "universitet", kan diagrammet ha "unversty, universty, universitet, universum, omvänd". Alla dokument som matchar dem i diagrammet inkluderas i resultaten. I motsats till andra frågor som analyserar texten för att hantera olika former av samma ord ("möss" och "mus" tas jämförelserna i en suddig fråga till nominellt värde utan någon språklig analys av texten. "Universum" och "omvänd", som är semantiskt olika, kommer att matcha eftersom de syntaktiska skillnaderna är små.

En matchning lyckas om avvikelserna är begränsade till två eller färre redigeringar, där en redigering är ett infogat, borttaget, ersatt eller transponerat tecken. Strängkorrigeringsalgoritmen som anger differensen är [det Metyau-Levenshtein-avståndsmått](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) som beskrivs som "minsta antal operationer (infogningar, borttagningar, substitutioner eller införlivanden av två intilliggande tecken) som krävs för att ändra ett ord till det andra". 

I Azure Cognitive Search:

+ Fuzzy fråga gäller för hela termer, men du kan stödja fraser genom OCH konstruktioner. Till exempel skulle "Unviersty~ av ~ "Wshington ~" matcha på "University of Washington".

+ Standardavståndet för en redigering är 2. Värdet `~0` innebär ingen expansion (endast den exakta termen betraktas som en `~1` matchning), men du kan ange för en grad av skillnad eller en redigering. 

+ En suddig fråga kan expandera en term upp till 50 ytterligare permutationer. Den här gränsen kan inte konfigureras, men du kan effektivt minska antalet expansioner genom att minska redigeringsavståndet till 1.

+ Svaren består av dokument som innehåller en relevant matchning (upp till 50).

Sammantaget skickas diagrammen som matchningsvillkor mot token i indexet. Som du kan föreställa er är fuzzy sökning i sig långsammare än andra frågeformulär. Storleken och komplexiteten i indexet kan avgöra om fördelarna är tillräckliga för att kompensera svarstiden för svaret.

> [!NOTE]
> Eftersom fuzzy sökning tenderar att vara långsam, kan det vara värt att undersöka alternativ såsom n-gram indexering, med dess progression av korta tecken sekvenser (två och tre tecken sekvenser för bigram och trigram tokens). Beroende på språk- och frågeyta kan n-gram ge dig bättre prestanda. Avvägningen är att n-gram indexering är mycket lagringsintensiv och genererar mycket större index.
>
> Ett annat alternativ, som du kan överväga om du vill hantera bara de mest flagranta fall, skulle vara en [synonym karta](search-synonyms.md). Mappa till exempel "sök" till "serach, serch, sarch" eller "retrieve" till "retreive".

## <a name="indexing-for-fuzzy-search"></a>Indexering för fuzzy sökning

Analysatorer används inte under frågebearbetning för att skapa ett expansionsdiagram, men det betyder inte att analysatorer ska ignoreras i suddiga sökscenarier. När allt kommer runt, analysatorer används under indexering för att skapa token som matchning görs, om frågan är fri form, filtrerad sökning, eller en suddig sökning med ett diagram som indata. 

När analysatorer tilldelas per fält baseras beslutet att finjustera analyskedjan i allmänhet på det primära användningsfallet (ett filter eller fulltextsökning) i stället för specialiserade frågeformulär som fuzzy search. Av denna anledning finns det inte en specifik analysator rekommendation för fuzzy sökning. 

Men om testfrågor inte producerar de matchningar du förväntar dig, kan du prova att variera indexeringsanalysatorn och ställa in den på en [språkanalysator](index-add-language-analyzers.md)för att se om du får bättre resultat. Vissa språk, särskilt de med vokalmutationer, kan dra nytta av böjningar och oregelbundna ordformer som genereras av Microsofts naturliga språkprocessorer. I vissa fall kan det göra skillnad att använda rätt språkanalysator om en term tokeniseras på ett sätt som är kompatibelt med det värde som tillhandahålls av användaren.

## <a name="how-to-use-fuzzy-search"></a>Så här använder du fuzzy search

Luddiga frågor skapas med hjälp av den fullständiga Lucene-frågesyntaxen, vilket anropar [Lucene-frågetolken](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Ange den fullständiga Lucene-tolken`queryType=full`på frågan ( ).

1. Du kan också begränsa begäran till specifika`searchFields=<field1,field2>`fält med den här parametern ( ). 

1. Lägg till tilde`~`() operatorn i slutet`search=<string>~`av hela termen ( ).

   Inkludera en valfri parameter, ett tal mellan 0 och 2 (standard), om du vill ange redigeringsavståndet (`~1`). Till exempel skulle "blå~" eller "blå~1" returnera "blå", "blues" och "lim".

I Azure Cognitive Search, förutom termen och avståndet (högst 2), finns det inga ytterligare parametrar att ange på frågan.

> [!NOTE]
> Under frågebearbetningen genomgår inte luddiga frågor [lexikal analys](search-lucene-query-architecture.md#stage-2-lexical-analysis). Frågeindata läggs till direkt i frågeträdet och expanderas för att skapa ett diagram med termer. Den enda omvandling som utförs är lägre hölje.

## <a name="testing-fuzzy-search"></a>Testa fuzzy sökning

För enkel testning rekommenderar vi [Sökutforskare eller](search-explorer.md) [Brevbärare](search-get-started-postman.md) för iterering över ett frågeuttryck. Båda verktygen är interaktiva, vilket innebär att du snabbt kan gå igenom flera varianter av en term och utvärdera svaren som kommer tillbaka.

När resultaten är tvetydiga kan [träffmarkering](search-pagination-page-layout.md#hit-highlighting) hjälpa dig att identifiera matchningen i svaret. 

> [!Note]
> Användningen av träffmarkering för att identifiera suddiga matchningar har begränsningar och fungerar bara för grundläggande fuzzy sökning. Om ditt index har bedömningsprofiler, eller om du lagerför frågan med ytterligare syntax, kan det hända att träffmarkeringen inte kan identifiera matchningen. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Exempel 1: fuzzy sökning med den exakta termen

Anta att följande sträng `"Description"` finns i ett fält i ett sökdokument:`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Börja med en suddig sökning på "speciell" och lägg till träffmarkering i fältet Beskrivning:

    search=special~&highlight=Description

I svaret, eftersom du lagt till träffmarkering, formateran tillämpas på "speciell" som matchande term.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Försök begäran igen, felstavning "speciell" genom att ta ut flera bokstäver ("pe"):

    search=scial~&highlight=Description

Hittills har ingen förändring av svaret. Med hjälp av standardvärdet för 2 grader avstånd, ta bort två tecken "pe" från "speciella" fortfarande möjliggör en lyckad matchning på den termen.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Försöker en begäran, ytterligare ändra söktermen genom att ta ut ett sista tecken för totalt tre borttagningar (från "speciell" till "skala"):

    search=scal~&highlight=Description

Observera att samma svar returneras, men nu istället för att matcha på "special", är den luddiga matchningen på "SQL".

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

Poängen med detta utvidgade exempel är att illustrera den tydlighet som träffmarkering kan ge tvetydiga resultat. I samtliga fall returneras samma dokument. Om du hade förlitat dig på dokument-ID:n för att verifiera en matchning, kanske du har missat övergången från "speciell" till "SQL".

## <a name="see-also"></a>Se även

+ [Så här fungerar fulltextsökning i Azure Cognitive Search (frågetolningsarkitektur)](search-lucene-query-architecture.md)
+ [Sök explorer](search-explorer.md)
+ [Fråga i .NET](search-query-dotnet.md)
+ [Så här frågar du i REST](search-create-index-rest-api.md)
