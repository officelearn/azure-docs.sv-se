---
title: Azure Search-prestanda och optimering överväganden | Microsoft Docs
description: Finjustera prestanda för Azure Search och konfigurera optimala skala
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: 89c0352723f1ed00784250b566902028af853d10
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Azure Search-prestanda och optimering överväganden
En bra sökinställningar är en nyckel till slutförd för många mobila och webbprogram. Från fastigheter, för bil marknadsplatser till onlinekataloger, påverkar Snabbsökning och relevanta resultat kundupplevelsen. Det här dokumentet är avsett att hjälp du identifiera bästa praxis för att få ut mesta möjliga av Azure Search, särskilt för avancerade scenarier med avancerad krav för skalbarhet, flerspråkig stöd eller anpassade rangordning.  Dessutom kan det här dokumentet beskrivs internals och täcker metoder fungera effektivt i verkliga kunden appar.

## <a name="performance-and-scale-tuning-for-search-services"></a>Prestanda och skalning justera för Search-tjänster
Vi används alla sökmotorer, till exempel Bing och Google och hög prestanda som de erbjuder.  När kunder använder din sökning-aktiverade webb- eller mobila program, kommer de därför förvänta sig liknande prestandaegenskaper.  När du optimerar prestanda för sökning är på bästa sätt att fokusera på latens, vilket är den tid som en fråga tar att slutföra och returnera resultat.  När du optimerar för sökning fördröjning är det viktigt att:

1. Välj en målfördröjning (eller hur lång tid) som en vanlig sökning begär bör ta för att slutföra.
2. Skapa och testa en verklig arbetsbelastning mot din söktjänst med en realistisk dataset för att mäta dessa latens priser.
3. Börja med ett lågt antal frågor per sekund (QPS) och fortsätter att öka antalet köras i testet förrän svarstiden sjunker under den definierade målfördröjning.  Detta är ett viktigt prestandatest när du planerar för att skala när programmet växer i användning.
4. Om möjligt återanvända HTTP-anslutningar.  Om du använder Azure Search .NET SDK, det innebär att du ska använda en instans eller [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) instans, och om du använder REST API du bör återanvända en enda HttpClient.

När du skapar dessa testa arbetsbelastningar finns det vissa egenskaper för Azure Search att tänka på:

1. Det är möjligt att push så många Sök frågar samtidigt, att de tillgängliga resurserna i Azure Search-tjänsten kommer att för många.  När det händer visas HTTP 503 svarskoder.  Därför är det bäst att börja med olika områden för search-begäranden att granska skillnaderna i svarstid priser när du lägger till flera search-begäranden.
2. Överföring av innehåll till Azure Search påverkar den övergripande prestanda och svarstiden för Azure Search-tjänsten.  Om du förväntar dig att skicka data när användare utför sökningar, är det viktigt att ta hänsyn arbetsbelastningen i dina tester.
3. Inte alla sökfråga utför på samma prestandanivåer.  Till exempel utför ett dokument lookup- eller förslag vanligtvis snabbare än en fråga med ett stort antal aspekter och filter.  Det är bäst att vidta olika frågor som du förväntar dig att se i beräkningen när du skapar dina tester.  
4. Variation av search-begäranden är viktig eftersom om du kör kontinuerligt samma search-begäranden, cachelagring av data kommer att börja kontrollera prestanda bättre än den med flera olika fråga ställer ut.

> [!NOTE]
> [Visual Studio belastningen testning](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) är ett mycket bra sätt att utföra din benchmark tester som du kan köra HTTP-begäranden som du behöver för att köra frågor mot Azure Search och möjliggör parallellisering för förfrågningar.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Skala Azure Search för hög frågan priser och begränsas begäranden
När du tar emot för många begäranden för begränsad eller överskrider dina mål latens priser från en ökad frågebelastningen titta om du vill minska svarstiden priser på något av två sätt:

1. **Öka repliker:** en replik är som en kopia av dina data så att Azure-sökning för att belastningsutjämna förfrågningar mot flera kopior.  Alla belastningsutjämning och replikering av data över repliker hanteras av Azure Search och du kan ändra antalet repliker som allokerats för din tjänst när som helst.  Du kan allokera upp till 12 repliker i en standardsöktjänst och 3 repliker i en grundläggande söktjänst. Repliker kan justeras antingen från den [Azure-portalen](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).
2. **Öka Sök nivå:** Azure Search är en [antal nivåer](https://azure.microsoft.com/pricing/details/search/) och var och en av de här nivåerna ger olika nivåer av prestanda.  I vissa fall kanske du så många frågor du är på nivån inte kan tillhandahålla tillräckligt låg latens priser, även när repliker är överutnyttjade ut.  I så fall måste kanske du vill utnyttja något högre Sök-nivåer, till exempel Azure Search S3-nivå som passar bra för scenarier med stora mängder dokument och mycket hög frågan arbetsbelastningar.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Skala Azure Search för långsam enskilda frågor
En annan orsak varför latens priser kan vara långsam är från en enskild fråga tar för lång tid att slutföra.  I det här fallet förbättrar lägger till repliker inte svarstiden priser.  För det här fallet är finns det två alternativ:

1. **Öka partitioner** en partition är en mekanism för att dela data mellan extra resurser.  Därför när du lägger till en andra partition hämtar data delas in i två.  En tredje partition delar ditt index i tre osv.  Detta har även effekten att i vissa fall långsam frågor utför snabbare på grund av parallellisering för beräkning.  Det finns några exempel på där vi sett att detta parallellisering fungerar mycket bra med frågor som har låg selektivitet frågor.  Det här består av frågor som matchar många dokument eller när faceting måste ange antal över stora mängder dokument.  Eftersom det är mycket bearbetning som krävs för att poängsätta relevans dokument eller för att räkna antalet dokument, kan lägga till extra partitioner bidra till att tillhandahålla ytterligare beräkning.  
   
   Det kan finnas maximalt 12 partitioner i standardsöktjänst och 1 partition i grundläggande search-tjänsten.  Partitioner kan justeras antingen från den [Azure-portalen](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).
2. **Gränsen för hög kardinalitet fält:** en hög kardinalitet fältet består av en facetable eller filtrera fält som har ett stort antal unika värden, och därför tar mycket resurser för att beräkna resultat.   Exempelvis blir anger ett produkt-ID eller beskrivning fält som facetable filtrera för hög kardinalitet eftersom de flesta av värden från dokumentet till dokumentet är unika. Begränsa antalet fält som hög kardinalitet om möjligt.
3. **Öka Sök nivå:** Flytta upp till en högre nivå för Azure Search kan vara ett annat sätt att förbättra prestanda för långsamma frågor.  Varje högre nivå ger också snabbare processor och minne som kan ha en positiv inverkan på prestanda för frågor.

## <a name="scaling-for-availability"></a>Skalning för tillgänglighet
Repliker inte bara minska svarstid men kan också tillåta för hög tillgänglighet.  Med en enskild replik du förväntar dig periodiska avbrott på grund av att servern startas om efter programuppdateringar eller för andra underhållshändelser som inträffar.  Det är därför viktigt att beakta om hög tillgänglighet för sökningar (frågor) samt skrivningar (indexering händelser) krävs för ditt program.  Azure Search erbjuder alternativ för SLA på alla erbjudanden för betald sökningen med följande attribut:

* 2 repliker för hög tillgänglighet för skrivskyddade arbetsbelastningar (frågor)
* 3 eller fler repliker för hög tillgänglighet i läsa / skriva-arbetsbelastningar (frågor och indexering)

Mer information om detta finns i [servicenivåavtal för Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Eftersom repliker är kopior av dina data, om du har flera repliker kan Azure Search till datorn startas om och underhåll mot en replik i taget samtidigt som frågor för att fortsätta att köras mot andra replikerna.  Därför måste du också att tänka på hur den här driftstörningen kan påverka de frågor som har nu till att köras mot en mindre kopia av data.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Skalning fördelade arbetsbelastningar och ge geo-redundans
Du hittar att användare finns är långt från datacentret där är värd för Azure Search-tjänsten har högre latens priser för fördelade arbetsbelastningar.  Därför är det ofta viktigt att ha flera search-tjänster i områden som finns i närmare dessa användare.  Azure Search innehåller för närvarande inte en automatiserad metod för geo-replikering Azure Search index över regioner, men det finns några metoder som kan användas som kan göra den här processen enkel att implementera och hantera. Dessa beskrivs i de följande avsnitten.

Målet med en geodistribuerad uppsättning search-tjänster är att ha två eller flera index som är tillgängliga i två eller flera regioner där en användare kommer att dirigeras till den Azure Search-tjänst som tillhandahåller den lägsta fördröjningen som visas i det här exemplet:

   ![Cross-fliken i tjänster efter region][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Synkronisera data över flera Azure Search-tjänster
Det finns två alternativ för att hålla dina distribuerade söktjänster synkroniserade som består av antingen med hjälp av den [Azure sökindexeraren](search-indexer-overview.md) eller Push-API (kallas även den [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="azure-search-indexers"></a>Azure Search indexerare
Om du använder Azure sökindexeraren importerar du redan dataändringar från en central datalagret, till exempel Azure SQL DB- eller Azure Cosmos DB. När du skapar en ny sökning tjänsten du helt enkelt också skapa en ny Azure-sökindexeraren för tjänsten som pekar på den här samma datalager. På så sätt, när nya ändringar finns i datalagret, indexeras de sedan av olika indexerare.  

Här är ett exempel på hur den arkitekturen skulle se ut.

   ![Enskild datakälla med tjänsten kombinationer och distribuerade indexeraren][2]

### <a name="push-api"></a>Push-API
Om du använder Azure Search Push-API för att [uppdaterar innehållet i ditt Azure Search index](https://docs.microsoft.com/rest/api/searchservice/update-index), du kan synkronisera din olika search-tjänster genom att skicka ändringar till alla search-tjänster när en uppdatering krävs.  När detta är det viktigt att se till att hantera fall där en uppdatering för en search-tjänsten misslyckas och en eller flera uppdateringar lyckas.

## <a name="leveraging-azure-traffic-manager"></a>Utnyttja Azure Traffic Manager
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) kan du vägen förfrågningar till flera geo finns webbplatser som sedan backas upp av flera Azure Search-tjänster.  En fördel med Traffic Manager är att den kan söka Azure Search för att säkerställa att den är tillgänglig och dirigera användare till alternativa söktjänster vid avbrott.  Dessutom kan kan du routning search-begäranden via Azure-webbplatser, Azure Traffic Manager du läser in saldo fall där webbplatsen är igång, men inte Azure Search.  Här är ett exempel på vilken arkitektur som utnyttjar Traffic Manager.

   ![Cross-fliken i tjänster efter region, med central Traffic Manager][3]

## <a name="monitoring-performance"></a>Övervaka prestanda
Azure Search ger dig möjlighet att analysera och övervaka prestanda för din tjänst via [Sök trafik Analytics (STA)](search-traffic-analytics.md). Via STA, kan du eventuellt logga enskilda sökningar samt aggregerade mått till ett Azure Storage-konto som sedan kan bearbetas för analys eller visualiseras i Power BI.  Med hjälp av STA mätvärden, kan du granska prestandastatistik, till exempel Genomsnittligt antal frågor eller svarstider för frågan.  Dessutom kan åtgärden loggning du visa detaljer om specifika sökningar.

STA är ett ovärderligt verktyg för att förstå latens priser som ur Azure Search.  Eftersom frågan prestandamått loggas baseras på överföringstiden för en fråga bearbetas fullständigt i Azure Search (från den tid som begäran till när det skickas), kan du använda detta för att avgöra om problem med nätverkssvarstiden från Azure Search-tjänsten på klientsidan eller utanför tjänsten, som från Nätverksfördröjningen.  

## <a name="next-steps"></a>Nästa steg
Läs mer om prissättning nivåer och tjänster gränserna för vart och ett i [gränser i Azure Search](search-limits-quotas-capacity.md).

Besök [kapacitetsplanering](search-capacity-planning.md) lära dig mer om kombinationer av partition och repliken.

Titta på följande videoklipp för flera nedbrytning på prestanda och för att se vissa demonstration av hur du implementerar optimeringar som beskrivs i den här artikeln:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
