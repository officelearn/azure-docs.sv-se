---
title: Distributions strategier och bästa praxis för att optimera prestanda
titleSuffix: Azure Cognitive Search
description: Lär dig mer om teknik och bästa praxis för att justera Azure Kognitiv sökning prestanda och konfigurera den optimala skalningen.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 15557a437732ee15c3c6dada7b2d9fe1d397dc5a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793428"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-cognitive-search"></a>Distributions strategier och bästa praxis för att optimera prestanda på Azure Kognitiv sökning

Den här artikeln beskriver metod tips för avancerade scenarier med avancerade krav på skalbarhet och tillgänglighet. 

## <a name="develop-baseline-numbers"></a>Utveckla bas linje nummer
När du optimerar Sök prestanda bör du fokusera på att minska frågans körnings tid. Om du vill göra det måste du veta vad en vanlig fråga-belastning ser ut. Följande rikt linjer kan hjälpa dig att komma till bas linje frågor.

1. Välj en mål svars tid (eller den längsta tid) som en typisk sökbegäran ska ta att slutföra.
2. Skapa och testa en verklig arbets belastning mot din Sök tjänst med en realistisk data uppsättning för att mäta de här latens priserna.
3. Börja med ett lågt antal frågor per sekund (frågor per sekund) och öka antalet utförda gradvis i testet tills svars tiden sjunker under den definierade mål fördröjningen. Detta är ett viktigt mått för att hjälpa dig att planera för skalning när ditt program växer i användning.
4. Återanvända HTTP-anslutningar närhelst det är möjligt. Om du använder Azure Kognitiv sökning .NET SDK innebär det att du bör återanvända en instans eller [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) -instans och om du använder REST API bör du återanvända en enda httpclient.
5. Variera ämnet för förfrågningar så att sökningen sker över olika delar av ditt index. Variationen är viktig eftersom om du kontinuerligt kör samma Sök begär Anden börjar cachelagring av data att öka prestandan bättre än den kan ha en mer detaljerad frågegrupp.
6. Variera strukturen för fråge förfrågningar så att du får olika typer av frågor. Alla Sök frågor utförs inte på samma nivå. Till exempel är ett dokuments öknings-eller Sök förslag vanligt vis snabbare än en fråga med ett stort antal ansikte och filter. Test kompositionen bör innehålla olika frågor, i ungefär samma förhållande som du förväntar sig i produktionen.  

När du skapar de här test arbets belastningarna finns det vissa egenskaper för Azure Kognitiv sökning att tänka på:

+ Det är möjligt att du överbelastar din tjänst genom att skicka för många Sök frågor vid ett tillfälle. När detta inträffar visas HTTP 503-svars koder. Om du vill undvika en 503 under testningen börjar du med olika intervall av Sök begär Anden för att se skillnaderna mellan svars tider när du lägger till fler Sök begär Anden.

+ Azure Kognitiv sökning kör inte indexerings aktiviteter i bakgrunden. Om tjänsten hanterar fråge-och indexerings arbets belastningar samtidigt tar du hänsyn till detta genom att antingen introducera indexerings jobb i dina testtester eller genom att undersöka alternativen för att köra indexerings jobb under låg belastnings tid.

> [!NOTE]
> [Belastnings testning i Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) är ett bra sätt att utföra benchmark-tester eftersom det gör att du kan köra HTTP-begäranden som du behöver för att köra frågor mot Azure kognitiv sökning och möjliggör parallellisering av begär Anden.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Skalning för hög frågans volym och begränsade begär Anden
När du får för många begränsade begär Anden eller överskrider din mål svars tid från en ökad läsar fråga, kan du se till att minska svars tiderna på något av följande sätt:

1. **Öka repliker:**  En replik är som en kopia av dina data så att Azure Kognitiv sökning att belastningsutjämna begär Anden mot flera kopior.  All belastnings utjämning och replikering av data över repliker hanteras av Azure Kognitiv sökning och du kan ändra antalet repliker som har allokerats för din tjänst när som helst.  Du kan allokera upp till 12 repliker i en standard Sök tjänst och tre repliker i en grundläggande Sök tjänst. Repliker kan justeras antingen från [Azure Portal](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).
2. **Öka Sök nivån:**  Azure Kognitiv sökning finns på ett [antal nivåer](https://azure.microsoft.com/pricing/details/search/) och var och en av dessa nivåer erbjuder olika prestanda nivåer.  I vissa fall kan du ha så många frågor att nivån som du är på inte kan tillhandahålla tillräckligt med låg latens taxa, även när repliker är överutnyttjade. I det här fallet kanske du vill överväga att använda en av de högre Sök nivåerna, till exempel Azure Kognitiv sökning S3-nivån som passar bäst för scenarier med ett stort antal dokument och mycket hög fråge arbets belastningar.

## <a name="scaling-for-slow-individual-queries"></a>Skalning för långsamma enskilda frågor
En annan orsak till hög latens frekvenser är att en fråga tar för lång tid att slutföra. I det här fallet kommer du inte att kunna lägga till repliker. Två möjliga alternativ som kan vara till hjälp kan vara följande:

1. **Öka partitioner** En partition är en mekanism för att dela data mellan extra resurser. Om du lägger till en andra partition delas data upp i två, en tredje partition delar den i tre, och så vidare. En positiv sido effekt är att långsammare frågor ibland presterar snabbare på grund av parallell dator användning. Vi har noterat parallellisering för frågor med låg selektivitet, till exempel frågor som stämmer överens med många dokument, eller till att det finns ett stort antal dokument. Eftersom det krävs en betydande beräkning för att räkna med relevanta i dokumenten, eller för att räkna antalet dokument, så kan du lägga till extra partitioner snabbare.  
   
   Det får finnas högst 12 partitioner i standard Sök tjänsten och 1 partition i den grundläggande Sök tjänsten.  Partitioner kan justeras antingen från [Azure Portal](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).

2. **Begränsa fält för högsta kardinalitet:** Ett högt kardinalitet-fält består av ett fasettable-eller filter bara fält som har ett stort antal unika värden och som därför förbrukar betydande resurser när du beräknar resultat. Om du till exempel anger ett produkt-ID eller ett beskrivnings fält som ett fasettable/filterbar, räknas det som hög kardinalitet eftersom de flesta av värdena från dokument till dokument är unika. När det är möjligt begränsar du antalet fält för hög kardinalitet.

3. **Öka Sök nivån:**  Att flytta upp till en högre Azure Kognitiv sökning-nivå kan vara ett annat sätt att förbättra prestandan för långsamma frågor. Varje högre nivå ger snabbare processorer och mer minne, som båda har en positiv inverkan på frågans prestanda.

## <a name="scaling-for-availability"></a>Skalning för tillgänglighet
Repliker bidrar inte bara till att minska svars tiden men kan även tillåta hög tillgänglighet. Med en enda replik bör du förvänta dig periodiska avbrott på grund av omstart av servern efter program uppdateringar eller för andra underhålls händelser som kommer att inträffa.  Därför är det viktigt att tänka på om ditt program kräver hög tillgänglighet för sökningar (frågor) och skrivningar (indexerings händelser). Azure Kognitiv sökning erbjuder SLA-alternativ för alla betalda Sök erbjudanden med följande attribut:

* 2 repliker för hög tillgänglighet för skrivskyddade arbets belastningar (frågor)
* 3 eller flera repliker för hög tillgänglighet av Läs-och skriv arbets belastningar (frågor och indexering)

Mer information finns på [Azure Kognitiv sökning serviceavtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Eftersom repliker är kopior av dina data kan du med flera repliker tillåta Azure Kognitiv sökning att starta om datorn och underhåll mot en replik samtidigt som du tillåter att frågekörningen fortsätter på andra repliker. Om du däremot tar bort repliker tar du en fråga om prestanda försämring, förutsatt att dessa repliker var en underutnyttjad resurs.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Skalning för geo-distribuerade arbets belastningar och GEO-redundans
För geo-distribuerade arbets belastningar kommer användare som finns långt från data Center som är värd för Azure Kognitiv sökning ha högre latens. En minskning är att etablera flera Sök tjänster i regioner med närmare närhet till dessa användare. Azure Kognitiv sökning tillhandahåller för närvarande inte en automatiserad metod för geo-replikering av Azure Kognitiv sökning-index över flera regioner, men det finns vissa tekniker som kan användas för att göra den här processen enkel att implementera och hantera. Dessa beskrivs i följande avsnitt.

Målet för en geo-distribuerad uppsättning Sök tjänster är att två eller fler index är tillgängliga i två eller flera regioner där en användare dirigeras till den Azure Kognitiv sökning-tjänst som ger den lägsta svars tiden som visas i det här exemplet:

   ![Globala flikar för tjänster efter region][1]

### <a name="keeping-data-in-sync-across-multiple-azure-cognitive-search-services"></a>Hålla data synkroniserade över flera Azure Kognitiv sökning-tjänster
Det finns två alternativ för att hålla dina distribuerade Sök tjänster synkroniserade, som består av antingen med hjälp av [azure kognitiv sökning-indexeraren](search-indexer-overview.md) eller push-API: et (kallas även [azure-kognitiv sökning REST API](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Använd indexerare för att uppdatera innehåll på flera tjänster

Om du redan använder indexerare på en tjänst kan du konfigurera en andra indexerare på en andra tjänst för att använda samma data käll objekt, hämta data från samma plats. Varje tjänst i varje region har sin egen indexerare och ett mål index (ditt Sök index är inte delat, vilket innebär att data är duplicerade), men varje indexerare refererar samma data källa.

Här är ett visuellt visuellt objekt av vad arkitekturen skulle se ut.

   ![Enkel data källa med distribuerade indexerare och tjänst kombinationer][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Använd REST-API: er för att överföra innehålls uppdateringar på flera tjänster
Om du använder Azure-Kognitiv sökning REST API för att [skicka innehåll i ditt Azure kognitiv sökning-index](https://docs.microsoft.com/rest/api/searchservice/update-index)kan du behålla dina olika Sök tjänster synkroniserade genom att skicka ändringar till alla Sök tjänster när en uppdatering krävs. I din kod ser du till att hantera fall där en uppdatering av en Sök tjänst Miss lyckas men fungerar för andra Sök tjänster.

## <a name="leverage-azure-traffic-manager"></a>Utnyttja Azure Traffic Manager
Med [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) kan du dirigera begär anden till flera geo-placerade webbplatser som sedan backas upp av flera Sök tjänster. En fördel med Traffic Manager är att den kan avsöka Azure-Kognitiv sökning för att säkerställa att den är tillgänglig och dirigera användare till alternativa Sök tjänster i händelse av drift stopp. Om du dirigerar Sök förfrågningar via Azure Web Sites kan du dessutom använda Azure-Traffic Manager för att läsa in balans fall där webbplatsen är igång men inte Azure Kognitiv sökning. Här är ett exempel på vad arkitekturen som utnyttjar Traffic Manager.

   ![Över-fliken av tjänster efter region, med central Traffic Manager][3]

## <a name="next-steps"></a>Nästa steg
Mer information om pris nivåer och tjänst begränsningar för var och en finns i [tjänst begränsningar i Azure kognitiv sökning](search-limits-quotas-capacity.md).

Besök [kapacitets planering](search-capacity-planning.md) för att lära dig mer om kombinationer av partitioner och repliker.

Om du vill ha mer information om prestanda och se några demonstrationer av hur du implementerar Optimeringarna som beskrivs i den här artikeln kan du titta på följande video:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
