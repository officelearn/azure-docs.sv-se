---
title: Vanliga frågor och svar (FAQ) – Azure Search
description: Få svar på vanliga frågor om Microsoft Azure Search-tjänst, en söktjänst i molnet på Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9ea4e4ec78e5613758bd9e5ff7a4fbd3273208c6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312824"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Azure Search – vanliga frågor (och svar FAQ)

 Hitta svar på vanliga frågor om koncept, kod och scenarier som rör Azure Search.

## <a name="platform"></a>Plattform

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Hur skiljer sig Azure Search från fulltextsökning i min DBMS?

Azure Search har stöd för flera datakällor, [språklig analys för många språk](https://docs.microsoft.com/rest/api/searchservice/language-support), [anpassade analys för intressanta och ovanliga datainmatningar](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), Sök rangordnas kontroller genom [bedömning profiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), och användarupplevelse funktioner som typeahead, markering av träffar och aspektbaserad navigering. Den har även andra funktioner, till exempel synonymer och omfattande frågesyntax, men de vanligtvis särskiljer inte funktioner.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Vad är skillnaden mellan Azure Search och Elasticsearch?

När du jämför sökteknik ställer kunder ge specifik information på Azure Search i jämförelse med Elasticsearch. Kunder som väljer Azure Search över Elasticsearch för sina search programprojekt vanligtvis inte eftersom vi har gjort en viktig uppgift enklare eller de behöver den inbyggda integreringen med andra Microsoft-tekniker:

+ Azure Search är en fullständigt hanterad molntjänst med 99,9% serviceavtal (SLA) när etablerats med tillräckligt med redundans (2 repliker läsåtkomst, 3 repliker för Läs-och).
+ Microsofts [naturligt språk processorer](https://docs.microsoft.com/rest/api/searchservice/language-support) erbjuder avancerade lingvistiska.  
+ [Azure Search-indexerare](search-indexer-overview.md) kan crawla en mängd olika Azure-datakällor för inledande och inkrementella indexering.
+ Om du behöver snabbt svar på grund av variationer i frågan eller indexering volymer, kan du använda [skjutreglagen](search-manage.md#scale-up-or-down) i Azure portal eller kör en [PowerShell-skript](search-manage-powershell.md), vilket kringgår fragmenthanterings direkt.  
+ [Bedömning och justeringsfunktioner](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) ger metoder för att påverka Sök rangordning utöver vad enbart sökmotorn kan ge.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Kan jag pausar Azure Search-tjänst och stoppa faktureringen?

Du kan pausa tjänsten. Databaserad- och lagringsresurser som ska allokeras till din exklusiv användning när tjänsten skapas. Det går inte att frigöra och frigöra de resurser på begäran.

## <a name="indexing-operations"></a>Indexeringsåtgärder

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>Säkerhetskopiera och återställa (eller hämta och flytta) index eller index ögonblicksbilder?

Även om du kan [få en Indexdefinition](https://docs.microsoft.com/rest/api/searchservice/get-index) när som helst, det finns ingen index extrahering, ögonblicksbild eller säkerhetskopiering – återställning av funktionen för att ladda ned en *fylls* index som körs i molnet till ett lokalt system eller Flytta den till en annan Azure Search-tjänst.

Index byggs och ifylld från kod som du skriver och bara köras på Azure Search i molnet. Normalt kunder som vill flytta ett index till en annan tjänst gör du genom att redigera koden för att använda en ny slutpunkt och kör sedan indexering. Om du vill kunna ta en ögonblicksbild eller ett index för säkerhetskopiering, omvandla en röst [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Kan jag återställa Mina index eller tjänsten när den har tagits bort?

Nej, du kan inte återställa index eller tjänster. Om du tar bort ett Azure Search-index åtgärden är slutgiltiga och indexet kan inte återställas. När du tar bort en Azure Search-tjänst, tas alla index i tjänsten bort permanent. Även om du tar bort en Azure-resursgrupp som innehåller en eller flera Azure Search-tjänsterna, tas alla tjänster bort permanent.  

Återställning av resurser, till exempel index, indexerare, datakällor och kompetens måste du återskapa dem från kod. När det gäller index, måste du indexera data från externa källor. Därför rekommenderas att du behåller en huvudkopian eller en säkerhetskopia av den ursprungliga informationen i ett annat datalager, till exempel Azure SQL Database eller Cosmos DB.

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Kan jag indexera från repliker för SQL database (gäller för [Azure SQL Database indexerare](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Det finns inga restriktioner gällande användningen av primära eller sekundära repliker som en datakälla när du skapar ett index från början. Uppdatera ett index med inkrementella uppdateringar (baserat på ändrade poster) kräver dock den primära repliken. Det här kravet kommer från SQL-databas, vilket garanterar ändringsspårning på primära repliker. Om du använder sekundära repliker för en arbetsbelastning för uppdatering av index, är det inte säkert som du får alla data.

## <a name="search-operations"></a>Sökåtgärder

### <a name="can-i-search-across-multiple-indexes"></a>Kan jag söka över flera index?

Nej, den här åtgärden stöds inte. Sökningen är alltid begränsade till ett enda index.

### <a name="can-i-restrict-search-corpus-access-by-user-identity"></a>Kan jag begränsa sökningen Kristi åtkomst av användarens identitet?

Du kan implementera [säkerhetsfilter](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) med `search.in()` filter. Filtret composes bra med [identity management-tjänster som Azure Active Directory](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) att trimma sökresultat baserat på användarens gruppmedlemskap som definierats.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Varför finns det noll matchar på villkor som jag vet att gälla?

I de flesta fall är inte veta att varje frågetyp stöder olika sökbeteenden och nivåer av lingvistisk analys. Fulltextsökning, vilket är den dominerande arbetsbelastningen, innehåller en analysfasen för språk som bryter mot villkoren till rot-formulär. Den här delen av frågan parsning kastar ett bredare net över möjliga matchningar eftersom principfilerna termen matchar ett större antal varianter.

Jokertecken, fuzzy och regex-frågor, men analyseras inte som vanlig term eller fras frågor och kan leda till dålig återkallande om frågan inte matchar analyserade form av ordet i sökindexet. Mer information om frågan parsning och analys, finns i [fråga arkitektur](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Min jokertecken är långsamma.

De flesta jokertecken sökfrågor som prefix, fuzzy och regex, är har skrivits internt med matchande termer i sökindexet. Den här extra bearbetningen av genomsökning sökindexet lägger till svarstid. Ytterligare, bred sökning frågor som `a*` till exempel som är sannolikt att skrivas med många villkor kan vara mycket långsamt. Överväg att definiera för högpresterande jokertecken, en [anpassat analysverktyg](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Varför är sökrangordningen ett konstant eller lika med resultat på 1.0 för varje träffar?

Som standard sökresultat poängsätts baserat på den [statistiska egenskaper för matchar villkoren](search-lucene-query-architecture.md#stage-4-scoring), och sorterad uppsättning högt till lågt i resultatet. Men vissa frågetyperna (med jokertecken, prefix, regex) alltid bidra med ett konstant värde till den övergripande dokument poängen. Det här beteendet är avsiktligt. Azure Search inför ett konstant värde för att tillåta matchningar hittades via fråga expansion ska tas med i resultatet, utan att påverka rangordning.

Anta exempelvis att indata för ”rundtur *” i en sökning med jokertecken producerar matchningar på ”visningar”, ”tourettes” och ”tourmaline”. Naturen för de här resultaten returneras, går det inte att härleda rimligen vilket är mer värdefulla än andra. Därför ignorera vi termen frekvenser när bedömning resultat i frågor för typer med jokertecken, prefix och regex. Sökresultat baserat på en partiell indata ges ett konstant värde att undvika att rikta mot potentiellt oväntat matchningar.

## <a name="design-patterns"></a>Designmönster

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Vad är den bästa metoden för att implementera lokaliserade search?

De flesta kunder välja dedikerade fält över en samling när det gäller stöd för olika nationella inställningar (språk) i samma index. Språkspecifika fält gör det möjligt att tilldela en lämplig analyzer. Till exempel tilldela Microsoft franska Analyzer till ett fält som innehåller franska strängar. Det förenklar också filtrering. Om du vet att en fråga har initierats på en sida för fr-fr, kan du begränsa sökresultaten till det här fältet. Eller skapa en [bedömningsprofil](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) att ge mer relativa viktade för fältet. Azure Search har stöd för över [50 språkanalysverktyg](https://docs.microsoft.com/azure/search/search-language-support) att välja bland.

## <a name="next-steps"></a>Nästa steg

Är en fråga om en funktion eller funktionalitet som saknas? Installera funktionen på den [User Voice-webbplatsen](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Se också

 [StackOverflow: Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Hur Fullständig textsökning fungerar i Azure Search](search-lucene-query-architecture.md)  
 [Vad är Azure Search?](search-what-is-azure-search.md)
