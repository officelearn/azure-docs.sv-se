---
title: Vanliga frågor och svar (FAQ) – Azure Search
description: Få svar på vanliga frågor om Microsoft Azure Search Service, en moln värds Sök tjänst på Microsoft Azure.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 600c619134cae18e69b5a200cb03fbebd82dee0f
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719890"
---
# <a name="azure-search---frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure Search

 Få svar på vanliga frågor om begrepp, kod och scenarier som är relaterade till Azure Search.

## <a name="platform"></a>Plattform

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Hur skiljer sig Azure Search från full texts ökning i mitt DBMS?

Azure Search stöder flera data källor, [språklig analys för många språk](https://docs.microsoft.com/rest/api/searchservice/language-support), [anpassad analys för intressanta och ovanliga data inmatningar](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search), Sök ranknings kontroller via [bedömnings profiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)och användar upplevelse funktioner som typeahead, träff markering och fasett-navigering. Den innehåller också andra funktioner, till exempel synonymer och RTF-frågesyntax, men de är i allmänhet inte särskiljande funktioner.

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Vad är skillnaden mellan Azure Search och ElasticSearch?

När du jämför Sök tekniker ber kunderna ofta om information om hur Azure Search jämför med ElasticSearch. Kunder som väljer Azure Search över ElasticSearch för sina sökprogram gör det vanligt vis därför att vi har gjort en viktig uppgift eller behöver inbyggd integrering med andra Microsoft-tekniker:

+ Azure Search är en fullständigt hanterad moln tjänst med 99,9% service avtal (SLA) när den tillhandahålls med tillräcklig redundans (2 repliker för Läs behörighet, tre repliker för Läs-och Skriv behörighet).
+ Microsofts [naturliga språk processorer](https://docs.microsoft.com/rest/api/searchservice/language-support) erbjuder inledande Edge-analys.  
+ [Azure Search indexerare](search-indexer-overview.md) kan crawla flera olika Azure-datakällor för inledande och stegvis indexering.
+ Om du behöver snabba svar på fluktuationer i fråge-eller indexerings volymer kan du använda [skjutreglagen](search-manage.md#scale-up-or-down) i Azure Portal eller köra ett PowerShell- [skript](search-manage-powershell.md)och kringgå Shard-hanteringen direkt.  
+ Med [bedömnings-och justerings funktionerna](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) får du möjlighet att påverka Sök ranknings poängen utöver vad sökmotorn kan ge.

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>Kan jag pausa Azure Search tjänst och avbryta faktureringen?

Det går inte att pausa tjänsten. Beräknings-och lagrings resurser allokeras för exklusiv användning när tjänsten skapas. Det går inte att frigöra och frigöra dessa resurser på begäran.

## <a name="indexing-operations"></a>Indexerings åtgärder

### <a name="move-backup-and-restore-indexes-or-index-snapshots"></a>Flytta, säkerhetskopiera och Återställ index eller index ögonblicks bilder?

Under utvecklings fasen kanske du vill flytta ditt index mellan Sök tjänster. Du kan till exempel använda en grundläggande eller kostnads fri pris nivå för att utveckla ditt index och sedan vill flytta det till standard nivån eller den högre nivån för produktions användning. 

Eller så kanske du vill säkerhetskopiera en index ögonblicks bild till filer som kan användas för att återställa dem senare. 

Du kan göra allt detta med exempel koden **index-Backup-Restore** i detta [Azure Search .net-exempel lagrings platsen](https://github.com/Azure-Samples/azure-search-dotnet-samples). 

Du kan också [Hämta en index definition](https://docs.microsoft.com/rest/api/searchservice/get-index) när som helst med hjälp av Azure Search REST API.

Det finns för närvarande ingen inbyggd funktion för extrahering av index, ögonblicks bilder eller säkerhets kopierings återställning i Azure Portal. Vi överväger dock att lägga till säkerhets kopierings-och återställnings funktionen i en framtida version. Om du vill visa ditt stöd för den här funktionen, omvandla en röst på [användarens röst](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index).

### <a name="can-i-restore-my-index-or-service-once-it-is-deleted"></a>Kan jag återställa mitt index eller tjänst när den tas bort?

Nej, om du tar bort ett Azure Search-index eller en tjänst kan den inte återställas. När du tar bort en Azure Search-tjänst tas alla index i tjänsten bort permanent. Om du tar bort en Azure-resurs grupp som innehåller en eller flera Azure Search tjänster tas alla tjänster bort permanent.  

Att återskapa resurser som index, indexerare, data källor och färdighetsuppsättningar kräver att du återskapar dem från kod. 

Om du vill skapa ett index igen måste du indexera om data från externa källor. Därför rekommenderar vi att du behåller en huvud kopia eller en säkerhets kopia av original data i ett annat data lager, till exempel Azure SQL Database eller Cosmos DB.

Alternativt kan du använda exempel koden **index-Backup-Restore** i den här [Azure Search .net-lagrings platsen](https://github.com/Azure-Samples/azure-search-dotnet-samples) för att säkerhetskopiera en index definition och en index ögonblicks bild till en serie JSON-filer. Senare kan du använda verktyget och filerna för att återställa indexet, om det behövs.  

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>Kan jag indexera från SQL Database-repliker (gäller [Azure SQL Database indexerare](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

Det finns inga begränsningar för användningen av primära eller sekundära repliker som data källa när du skapar ett index från grunden. Att uppdatera ett index med stegvisa uppdateringar (baserat på ändrade poster) kräver dock den primära repliken. Detta krav kommer från SQL Database, som endast garanterar ändrings spårning på primära repliker. Om du försöker använda sekundära repliker för att uppdatera arbets belastningen för index uppdatering finns det ingen garanti för att du får alla data.

## <a name="search-operations"></a>Sök åtgärder

### <a name="can-i-search-across-multiple-indexes"></a>Kan jag söka i flera index?

Nej, den här åtgärden stöds inte. Sökningen är alltid begränsad till ett enda index.

### <a name="can-i-restrict-search-index-access-by-user-identity"></a>Kan jag begränsa Sök index åtkomsten genom att använda användar identiteten?

Du kan implementera [säkerhets filter](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) med `search.in()`-filter. Filtret skriver bra med [identitets hanterings tjänster som Azure Active Directory (AAD)](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search-with-aad) för att trimma Sök resultat baserat på definierade användar grupp medlemskap.

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>Varför finns det ingen matchning på de villkor jag vet att vara giltiga?

Det vanligaste fallet är att du inte vet att varje frågetyp stöder olika Sök beteenden och nivåer av språkliga analyser. Full texts ökning, som är den dominerande arbets belastningen, innehåller en språk analys fas som delar upp villkoren för rot formulär. Den här aspekten av frågans parsing skickar ett bredare netto över möjliga matchningar, eftersom den token termen matchar ett större antal varianter.

Jokertecken, fuzzy och regexfrågor analyseras dock inte som vanliga term-eller fras frågor och kan leda till dåligt återkallande om frågan inte överensstämmer med den analyserade formen av ordet i Sök indexet. Mer information om fråga om parsning och analys finns i [fråga om arkitektur](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

### <a name="my-wildcard-searches-are-slow"></a>Mina sökningar i jokertecken är långsamma.

De flesta Sök frågor för jokertecken, t. ex. prefix, fuzzy och regex, skrivs om internt med matchande termer i Sök indexet. Den här extra bearbetningen av genomsökningen av Sök indexet lägger till i svars tid. Dessutom kan breda Sök frågor, t. ex. `a*`, till exempel, som troligen kommer att skrivas över med många villkor, vara mycket långsamt. För att genomföra jokertecken kan du definiera en [anpassad analys](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search).

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>Varför är sökningen ranka en konstant eller samma poäng på 1,0 för varje träff?

Som standard beräknas Sök resultaten utifrån de [statistiska egenskaperna för matchande villkor](search-lucene-query-architecture.md#stage-4-scoring)och beställs högt till lågt i resultat uppsättningen. Vissa frågetyper (jokertecken, prefix, regex) bidrar dock alltid till en konstant poäng till den övergripande dokument poängen. Det här beteendet är avsiktligt. Azure Search översätter en konstant Poäng för att tillåta matchningar som upptäckts via frågans expansion för att inkluderas i resultaten, utan att rangordningen påverkas.

Anta till exempel att du skriver in "guidad visning *" i en sökning med jokertecken som genererar matchningar på "visningar", "Tourettes" och "Tourmaline". Med hänsyn till arten av dessa resultat går det inte att på ett rimligt sätt härleda vilka termer som är mer värdefulla än andra. Av den anledningen ignorerar vi term frekvenser när Poäng resultat visas i frågor av typen jokertecken, prefix och regex. Sök resultat som baseras på en partiell indatamängd får en konstant Poäng för att undvika förskjutning mot potentiellt oväntade matchningar.

## <a name="design-patterns"></a>Designmönster

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>Vad är det bästa sättet att implementera lokaliserad sökning?

De flesta kunder väljer dedikerade fält över en samling när den kommer att stödja olika språk (språk) i samma index. Språkspecifika fält gör det möjligt att tilldela en lämplig analys. Du kan till exempel tilldela Microsoft French Analyzer till ett fält som innehåller franska strängar. Det fören klar också filtreringen. Om du vet att en fråga har initierats på en fr-fr-sida kan du begränsa Sök resultaten till det här fältet. Eller skapa en [bedömnings profil](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) för att ge fältet mer relativ vikt. Azure Search stöder fler än [50 språk analys](https://docs.microsoft.com/azure/search/search-language-support) verktyg för att välja bland.

## <a name="next-steps"></a>Nästa steg

Är din fråga om en funktion eller funktionalitet som saknas? Begär funktionen på [användarens röst webbplats](https://feedback.azure.com/forums/263029-azure-search).

## <a name="see-also"></a>Se också

 [StackOverflow: Azure Search](https://stackoverflow.com/questions/tagged/azure-search)   
 [Hur full texts ökning fungerar i Azure Search](search-lucene-query-architecture.md)  
 [Vad är Azure Search?](search-what-is-azure-search.md)
