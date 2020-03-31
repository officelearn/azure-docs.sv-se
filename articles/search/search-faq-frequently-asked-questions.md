---
title: Vanliga frågor och svar (FAQ)
titleSuffix: Azure Cognitive Search
description: Få svar på vanliga frågor om Microsoft Azure Cognitive Search-tjänsten, en söktjänst för molnbaserade söktjänster på Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fee74cb6ec5acd5fa0f171eab9769a833f04ad66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792917"
---
# <a name="azure-cognitive-search---frequently-asked-questions-faq"></a>Azure Cognitive Search – vanliga frågor och svar ( Vanliga frågor och svar)

 Hitta svar på vanliga frågor om begrepp, kod och scenarier relaterade till Azure Cognitive Search.

## <a name="platform"></a>Plattform

### <a name="how-is-azure-cognitive-search-different-from-full-text-search-in-my-dbms"></a>Hur skiljer sig Azure Cognitive Search från fulltextsökning i min DBMS?

Azure Cognitive Search stöder flera datakällor, [språklig analys för många språk,](https://docs.microsoft.com/rest/api/searchservice/language-support)anpassad analys för intressanta och ovanliga [datainmatningar, sökrankningskontroller](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)via [bedömningsprofiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)och funktioner för användarupplevelse som typeahead, träffmarkering och aspektbaserad navigering. Den innehåller också andra funktioner, till exempel synonymer och rich query-syntax, men de skiljer i allmänhet inte funktioner.

### <a name="what-is-the-difference-between-azure-cognitive-search-and-elasticsearch"></a>Vad är skillnaden mellan Azure Cognitive Search och Elasticsearch?

När du jämför söktekniker frågar kunderna ofta efter detaljer om hur Azure Cognitive Search jämförs med Elasticsearch. Kunder som väljer Azure Cognitive Search över Elasticsearch för sina sökprogramprojekt gör det vanligtvis eftersom vi har gjort en nyckeluppgift enklare eller de behöver den inbyggda integreringen med andra Microsoft-tekniker:

+ Azure Cognitive Search är en fullständigt hanterad molntjänst med 99,9 % servicenivåavtal (SLA) när det etableras med tillräcklig redundans (2 repliker för läsåtkomst, tre repliker för läs- och skrivning).
+ Microsofts [naturliga språkbehandlare](https://docs.microsoft.com/rest/api/searchservice/language-support) erbjuder främre språkanalys.  
+ [Azure Cognitive Search-indexerare](search-indexer-overview.md) kan genomsöka en mängd Olika Azure-datakällor för inledande och inkrementell indexering.
+ Om du behöver snabba svar på fluktuationer i fråge- eller indexeringsvolymer kan du använda [skjutreglage i Azure-portalen](search-manage.md#scale-up-or-down) eller köra ett [PowerShell-skript](search-manage-powershell.md)och kringgå fragmenthantering direkt.  
+ [Poängsättnings- och justeringsfunktioner](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) ger möjlighet att påverka poäng för sökterrang utöver vad sökmotorn ensam kan ge.

### <a name="can-i-pause-azure-cognitive-search-service-and-stop-billing"></a>Kan jag pausa Azure Cognitive Search-tjänsten och stoppa faktureringen?

Du kan inte pausa tjänsten. Beräknings- och lagringsresurser allokeras för din exklusiva användning när tjänsten skapas. Det går inte att frigöra och återkräva dessa resurser på begäran.

## <a name="indexing-operations"></a>Indexeringsoperationer

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Flytta, säkerhetskopiera och återställa index eller indexögonblicksbilder?

Under utvecklingsfasen kanske du vill flytta indexet mellan söktjänster. Du kan till exempel använda en grundläggande eller kostnadsfri prisnivå för att utveckla indexet och sedan flytta det till standardnivån eller den högre nivån för produktionsanvändning. 

Du kanske vill säkerhetskopiera en indexögonblicksbild till filer som kan användas för att återställa den senare. 

Du kan göra alla dessa saker med exempelkoden **för index-säkerhetskopieringsåterställning** i den här Azure [Cognitive Search .NET-exempelreponyeringen](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

Du kan också [få en indexdefinition](https://docs.microsoft.com/rest/api/searchservice/get-index) när som helst med hjälp av AZURE Cognitive Search REST API.

Det finns för närvarande ingen inbyggd indexutvinning, ögonblicksbild eller säkerhetskopiering-återställning funktion i Azure-portalen. Vi överväger dock att lägga till säkerhetskopierings- och återställningsfunktioner i en framtida version. Om du vill visa ditt stöd för den här funktionen röstar du på [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Kan jag återställa mitt index eller min tjänst när den har tagits bort?

Nej, om du tar bort ett Azure Cognitive Search-index eller -tjänst kan det inte återställas. När du tar bort en Azure Cognitive Search-tjänst tas alla index i tjänsten bort permanent. Om du tar bort en Azure-resursgrupp som innehåller en eller flera Azure Cognitive Search-tjänster tas alla tjänster bort permanent.  

Om du återskapar resurser som index, indexerare, datakällor och kunskaper måste du återskapa dem från koden. 

Om du vill återskapa ett index måste du indexera om data från externa källor. Därför rekommenderar vi att du behåller en huvudkopia eller säkerhetskopia av originaldata i ett annat datalager, till exempel Azure SQL Database eller Cosmos DB.

Som ett alternativ kan du använda exempelkoden **för index-säkerhetskopieringsåterställning** i den här Azure [Cognitive Search .NET-exempelreponeringen](https://github.com/Azure-Samples/azure-search-dotnet-samples) för att säkerhetskopiera en indexdefinition och indexögonblicksbild till en serie JSON-filer. Senare kan du använda verktyget och filerna för att återställa indexet om det behövs.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexers"></a>Kan jag indexera från SQL-databasrepliker (gäller azure [SQL Database indexerare)](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers)

Det finns inga begränsningar för användning av primära eller sekundära repliker som en datakälla när du skapar ett index från grunden. För att uppdatera ett index med inkrementella uppdateringar (baserat på ändrade poster) krävs dock den primära repliken. Det här kravet kommer från SQL Database, som garanterar ändringsspårning endast på primära repliker. Om du försöker använda sekundära repliker för en arbetsbelastning för indexuppdatering finns det ingen garanti för att du får alla data.

## <a name="search-operations"></a>Sökåtgärder

### <a name="can-i-search-across-multiple-indexes"></a>Kan jag söka i flera index?

Nej, den här åtgärden stöds inte. Sökningen begränsas alltid till ett enda index.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Kan jag begränsa sökindexåtkomsten efter användaridentitet?

Du kan implementera `search.in()` [säkerhetsfilter](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) med filter. Filtret komponerar bra med [identitetshanteringstjänster som Azure Active Directory(AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) för att trimma sökresultat baserat på definierat medlemskap i användargrupper.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Varför finns det noll matcher på villkor som jag vet är giltiga?

Det vanligaste fallet är att inte veta att varje frågetyp stöder olika sökbeteenden och nivåer av språkliga analyser. Fulltextsökning, som är den dominerande arbetsbelastningen, innehåller en språkanalysfas som bryter ned termer till rotformulär. Den här aspekten av frågetolning avgjuter ett bredare nät över möjliga matchningar, eftersom den tokeniserade termen matchar ett större antal varianter.

Jokertecken, fuzzy och regex frågor, dock inte analyseras som vanlig term eller fras frågor och kan leda till dålig återkallande om frågan inte matchar den analyserade formen av ordet i sökindex. Mer information om frågetolning och analys finns i [frågearkitektur](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Mina jokerteckensökningar är långsamma.

De flesta jokerteckensökningsfrågor, som prefix, fuzzy och regex, skrivs om internt med matchande termer i sökindexet. Den här extra bearbetningen av genomsökning av sökindexet läggs till svarstiden. Vidare kan breda sökfrågor, till exempel, som `a*` sannolikt kommer att skrivas om med många termer vara mycket långsamma. För performant wildcard-sökningar bör du överväga att definiera en [anpassad analysator](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Varför är sökningen rankas en konstant eller lika poäng på 1,0 för varje träff?

Som standard poängsätts sökresultaten baserat på de [statistiska egenskaperna för matchande termer](search-lucene-query-architecture.md#stage-4-scoring)och beställs högt till lågt i resultatuppsättningen. Vissa frågetyper (jokertecken, prefix, regex) bidrar dock alltid med en konstant poäng till den totala dokumentpoängen. Det här beteendet är avsiktligt. Azure Cognitive Search medför en konstant poäng för att tillåta att matchningar som hittas genom frågeexpansion inkluderas i resultaten, utan att påverka rangordningen.

Anta till exempel att en inmatning av "tour*" i en jokerteckensökning ger matchningar på "turer", "tourettes" och "tourmaline". Med tanke på dessa resultats natur finns det inget sätt att rimligen dra slutsatsen vilka termer som är mer värdefulla än andra. Därför ignorerar vi termfrekvenser när du poängsättning resulterar i frågor om typer av jokertecken, prefix och regex. Sökresultat baserat på en partiell indata får en konstant poäng för att undvika fördomar mot potentiellt oväntade matchningar.

## <a name="design-patterns"></a>Designmönster

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Vilken är den bästa metoden för att genomföra lokaliserad sökning?

De flesta kunder väljer dedikerade fält över en samling när det gäller att stödja olika språk (språk) i samma index. Språkspecifika fält gör det möjligt att tilldela en lämplig analysator. Till exempel tilldela Microsoft French Analyzer till ett fält som innehåller franska strängar. Det förenklar också filtrering. Om du vet att en fråga initieras på en fr-fr-sida kan du begränsa sökresultaten till det här fältet. Du kan också skapa en [poängprofil](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) för att ge fältet mer relativ vikt. Azure Cognitive Search har stöd för över [50 språkanalysatorer](https://docs.microsoft.com/azure/search/search-language-support) att välja mellan.

## <a name="next-steps"></a>Nästa steg

Är din fråga om en saknad funktion? Begär funktionen på [Webbplatsen User Voice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Se även

 [StackOverflow: Azure kognitiv sökning](https://stackoverflow.com/questions/tagged/azure-search)   
 [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)  
 [Vad är Azure Cognitive Search?](search-what-is-azure-search.md)
