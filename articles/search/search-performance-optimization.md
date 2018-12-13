---
title: Azure Search prestanda och optimering överväganden – Azure Search
description: Lär dig tekniker och bästa praxis för att justera prestanda för Azure Search och konfigurera optimal skala.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/01/2017
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 0a98e7f05e766d47a5ea9293409a74a6fafbf837
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310272"
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Azure Search-prestanda och optimering överväganden
En bra sökfunktion är en nyckel till slutförd för många mobila och webbprogram. Från fastigheter, som användas bil marknadsplatser till onlinekataloger, påverkar Snabbsökning och relevanta sökresultat kundupplevelsen. Det här dokumentet är avsett att hjälp dig identifiera stöd för bästa praxis för hur du får ut mest av Azure Search, särskilt för avancerade scenarier med avancerade krav för skalbarhet, flera språk eller anpassade rangordning.  Dessutom kan det här dokumentet beskriver internals och täcker metoder som fungerar effektivt i verkliga appar.

## <a name="performance-and-scale-tuning-for-search-services"></a>Prestanda- och skala justering för Search-tjänster
Vi används alla sökmotorer som Bing, Google och höga prestanda som de erbjuder.  När kunder använder din search-baserade webbprogram eller mobila program, kommer de därför förväntar sig liknande prestandaegenskaper.  När du optimerar för search-prestanda är en av de bästa metoderna att fokusera på svarstid, vilket är den tid som en fråga tar att slutföra och returnerar resultat.  Det är viktigt att när du optimerar för svarstid för sökning:

1. Välj en målfördröjning (eller den längsta tid) som en typisk sökning begär bör ta för att slutföra.
2. Skapa och testa en verklig arbetsbelastning mot din söktjänst med en realistisk datauppsättning för att mäta priserna svarstid.
3. Börja med ett lågt antal frågor per sekund (QPS) och fortsätter att öka antalet som körs i testet tills frågesvarstiden sjunker under den definierade målfördröjning.  Detta är ett viktigt prestandatest när du planerar för att skala allteftersom programmet växer i användning.
4. Om möjligt återanvända HTTP-anslutningar.  Om du använder Azure Search .NET SDK, innebär det att du bör återanvända en instans eller [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) instans, och om du använder REST API, bör du återanvänder en enda HttpClient.

När du skapar dessa testarbetsbelastningar finns det vissa egenskaper för Azure Search att tänka på:

1. Det är möjligt att skicka så många search frågor samtidigt, att de tillgängliga resurserna i Azure Search-tjänsten kommer att bli överhopade.  När detta inträffar visas HTTP 503-svarskoder.  Därför är det bäst att börja med olika områden i sökbegäranden att se skillnaderna i priserna för fördröjning när du lägger till flera sökförfrågningar.
2. Överföring av innehåll till Azure Search kommer att påverka övergripande prestanda och svarstider för Azure Search-tjänsten.  Om du förväntar dig att skicka data medan användare utför sökningar, är det viktigt att ta hänsyn till arbetsbelastningen i dina tester.
3. Inte alla sökfråga utför på samma prestandanivå.  Till exempel utför ett dokument lookup- eller Sök förslag vanligtvis snabbare än en fråga med ett stort antal fasetter och filter.  Det är bäst att vidta olika frågor som du förväntar dig att se hänsyn till när du skapar dina tester.  
4. Variant av sökförfrågningar är viktigt eftersom om du kör kontinuerligt samma sökförfrågningar, cachelagring av data ska börja visa prestanda bättre än den kan med en mer olika fråga inställd ut.

> [!NOTE]
> [Visual Studio Load testning](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) är ett mycket bra sätt att utföra dina benchmark testar eftersom den låter dig att köra HTTP-begäranden som du skulle behöva för att köra frågor mot Azure Search och aktiverar parallellisering av begäranden.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Skala Azure Search för stora frågearbetsbelastningar priser och begränsade begäranden
När du tar emot för många begränsade begäranden eller överskrider dina mål svarstid priser från en ökad frågebelastning, kan du se ut om du vill minska svarstiden priserna på något av två sätt:

1. **Öka repliker:**  En replik är som en kopia av dina data så att Azure Search för att belastningsutjämna förfrågningar mot flera kopior.  Alla belastningsutjämning och replikering av data i repliker som hanteras av Azure Search och du kan ändra antalet repliker som allokerats för din tjänst när som helst.  Du kan allokera upp till 12 repliker i en standardsöktjänst och 3 repliker i en grundläggande söktjänst. Repliker kan vara justeras antingen från den [Azure-portalen](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).
2. **Öka Search-nivå:**  Azure Search kommer in en [antal nivåer](https://azure.microsoft.com/pricing/details/search/) och var och en av de här nivåerna erbjuder olika nivåer av prestanda.  I vissa fall kan du ha så många frågor att den nivå du har inte kan ge tillräckligt med låg latens avgifter, även när repliker är överutnyttjade ut.  I det här fallet kan du överväga att använda en av de högsta nivåerna för sökning, till exempel Azure Search S3-nivå som passar bra för scenarier med stort antal dokument och extremt hög frågearbetsbelastningar.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Skala Azure Search för långsam enskilda frågor
En annan orsak varför svarstid priserna kan vara långsam är från en enskild fråga tar för lång tid att slutföra.  I det här fallet förbättras att lägga till repliker inte priserna för svarstid.  För det här fallet finns det två alternativ:

1. **Öka partitioner** en partition är en mekanism för att dela data mellan extra resurser.  Därför när du lägger till en andra partitionen hämtar dina data dela i två.  En tredje partition delar ditt index i tre osv.  Detta också innebär att i vissa fall kan långsamt frågor utförs snabbare på grund av parallellisering av beräkning.  Det finns några exempel på där vi har sett den här parallellisering fungerar mycket bra med frågor som har låg selektivitet frågor.  Det här består av frågor som matchar många dokument eller när fasettering måste ange antal över stort antal dokument.  Eftersom det inte finns en massa beräkning som behövs för att bedöma relevans dokument eller för att räkna antalet dokument, kan att lägga till extra partitioner bidra till att tillhandahålla ytterligare beräkning.  
   
   Det kan vara upp till 12 partitioner i Standard-söktjänst och 1 partition i grundläggande search-tjänsten.  Partitioner kan vara justeras antingen från den [Azure-portalen](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).
2. **Gräns för hög kardinalitet fält:** Ett fält med hög kardinalitet består av en fasettbar eller filtrerbara fält som har ett stort antal unika värden och därför tar mycket resurser att beräkna resultat.   Exempelvis blir anger ett fält som produkt-ID eller beskrivning som fasettbar/filtrerbara för hög kardinalitet eftersom de flesta av värden från dokument till dokument är unika. Om möjligt begränsar du antalet hög kardinalitet fält.
3. **Öka Search-nivå:**  Flytta upp till kan en högre nivå för Azure Search vara ett annat sätt att förbättra prestanda för långsamma frågor.  Varje högre nivå innehåller också snabbare processor och mer minne som kan ha en positiv inverkan på prestanda för frågor.

## <a name="scaling-for-availability"></a>Skalning för tillgänglighet
Repliker inte bara att minska svarstid men kan också tillåta för hög tillgänglighet.  Med en enskild replik kan du förväntar dig periodiska stilleståndstid på grund av att servern startas om efter programuppdateringar eller för andra underhållshändelser som inträffar.  Det är därför viktigt att tänka på om programmet kräver hög tillgänglighet för sökningar (frågor) samt skrivningar (indexering händelser).  Azure Search erbjuder alternativ för SLA på alla betalda search-erbjudanden med följande attribut:

* 2 repliker för hög tillgänglighet för skrivskyddade arbetsbelastningar (frågor)
* 3 eller fler repliker för hög tillgänglighet för skrivskyddade arbetsbelastningar (frågor och indexering)

Mer information om detta finns i [serviceavtal för Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Eftersom repliker är kopior av dina data, du har flera repliker kan Azure Search för machine omstarter och -underhåll mot en replik i taget samtidigt som frågor för att fortsätta köras mot andra repliker.  Därför måste du också att tänka på hur det här driftstoppet kan påverka de frågor som har nu som ska köras mot en mindre kopia av data.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Skala geo-distribuerad arbetsbelastningar och ange geo-redundans
Du kommer märka att användare som finns långt från datacentret där Azure Search-tjänsten finns har högre latens priserna för geo-distribuerad arbetsbelastningar.  Därför är det ofta viktigt att ha flera söktjänster i regioner som ligger i närmare dessa användare.  Azure Search ger för närvarande inte en automatiserad metod för Azure Search-index med geo-replikering i flera regioner, men det finns vissa tekniker som kan användas som kan göra den här processen enkel att implementera och hantera. Dessa beskrivs i nästa avsnitt.

Målet med en geo-distribuerad uppsättning söktjänster är att ha två eller flera index som är tillgängliga på två eller fler regioner där en användare kommer att dirigeras till Azure Search-tjänst som tillhandahåller den lägsta svarstiden som visas i det här exemplet:

   ![Cross-fliken tjänster efter region][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Synkronisera data över flera Azure Search-tjänster
Det finns två alternativ för att hålla dina distribuerade söktjänster synkroniserade som består av antingen med hjälp av den [Azure Search-indexeraren](search-indexer-overview.md) eller Push-API (kallas även den [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="azure-search-indexers"></a>Azure Search-indexerare
Om du använder Azure Search-indexeraren måste importerar du redan dataändringar från en central datalager som Azure SQL DB eller Azure Cosmos DB. När du skapar en ny sökning tjänsten, du helt enkelt även skapa en ny Azure Search-indexeraren för tjänsten som pekar på den här samma datalager. På så sätt kan när nya ändringar kommer till datalager, kommer de sedan att indexeras av olika indexerare.  

Här är ett exempel på hur den arkitekturen skulle se ut.

   ![Enskild datakälla med distribuerade indexerare och kombinationer av tjänsten][2]

### <a name="push-api"></a>Push-API
Om du använder Azure Search Push-API för att [uppdatera innehåll i ditt Azure Search-index](https://docs.microsoft.com/rest/api/searchservice/update-index), du kan synkronisera din olika söktjänster genom att skicka ändringar till alla söktjänster när en uppdatering krävs.  När detta är det viktigt att se till att hantera fall där en uppdatering till en söktjänst misslyckas och en eller flera uppdateringar lyckas.

## <a name="leveraging-azure-traffic-manager"></a>Utnyttja Azure Traffic Manager
[Med Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) gör att du kan dirigera begäranden till flera geografiskt placerade webbplatser som sedan backas upp av flera Azure Search-tjänsterna.  En fördel av Traffic Manager är att den kan söka i Azure Search för att se till att den är tillgänglig och dirigera användare till alternativa söktjänster i händelse av avbrott.  Dessutom kan kan du routning sökförfrågningar via Azure Web Sites, Azure Traffic Manager att läsa in saldo fall där webbplatsen är igång men inte Azure Search.  Här är ett exempel på vilken arkitektur som utnyttjar Traffic Manager.

   ![Cross-fliken tjänster efter region, med central Traffic Manager][3]

## <a name="monitoring-performance"></a>Övervaka prestanda
Azure Search ger dig möjlighet att analysera och övervaka prestanda för din tjänst via [Search Traffic Analytics (STA)](search-traffic-analytics.md). Via STA, kan du också logga enskilda sökningar samt aggregerade mätvärden till ett Azure Storage-konto som sedan kan bearbetas för analys eller visualiseras i Power BI.  Med hjälp av STA mätvärden, kan du granska prestandastatistik, till exempel genomsnittlig många frågor eller frågesvarstiderna.  Dessutom kan åtgärden loggning du visa detaljer om specifika sökningar.

STA är ett ovärderligt verktyg för att förstå svarstid priset för det Azure Search-perspektivet.  Eftersom frågan prestandamått loggas baseras på den tid som en fråga tar bearbetas fullständigt i Azure Search (från den tidpunkt som den begärs till när det skickas), kan du använda detta för att avgöra om det är problem med nätverkssvarstiden från Azure Search-tjänsten på klientsidan eller detaljer IDE av tjänsten, till exempel från Nätverksfördröjningen.  

## <a name="next-steps"></a>Nästa steg
Läs mer om prissättning nivåer och tjänster gränserna för vart och ett i [tjänstbegränsningar i Azure Search](search-limits-quotas-capacity.md).

Besök [kapacitetsplanering](search-capacity-planning.md) mer information om kombinationer av partition och repliken.

Titta på följande videoklipp för mer nedbrytning på prestanda och för att se några demonstrationer av hur du implementerar optimeringar som beskrivs i den här artikeln:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
