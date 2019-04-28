---
title: Distribution och bästa praxis för att optimera prestanda – Azure Search
description: Lär dig tekniker och bästa praxis för att justera prestanda för Azure Search och konfigurera optimal skala.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 8a07657d04cee57cb69c9f5f7862fed3e7965716
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283597"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Distribution och bästa praxis för att optimera prestanda för Azure Search

Den här artikeln beskriver Metodtips för avancerade scenarier med avancerade krav för skalbarhet och tillgänglighet. 

## <a name="develop-baseline-numbers"></a>Utveckla baslinje siffror
När du optimerar för search-prestanda, bör du fokusera på att minska frågetiden för körning. Gör att behöver du veta hur vanliga frågebelastning ser ut. Följande riktlinjer kan hjälpa dig att komma fram till baslinje fråga siffror.

1. Välj en målfördröjning (eller den längsta tid) som en typisk sökning begär bör ta för att slutföra.
2. Skapa och testa en verklig arbetsbelastning mot din söktjänst med en realistisk datauppsättning för att mäta priserna svarstid.
3. Börja med ett lågt antal frågor per sekund (QPS) och gradvis öka antalet som körs i testet tills frågesvarstiden sjunker under den definierade målfördröjning. Detta är ett viktigt prestandatest när du planerar för att skala allteftersom programmet växer i användning.
4. Om möjligt återanvända HTTP-anslutningar. Om du använder Azure Search .NET SDK, innebär det att du bör återanvända en instans eller [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) instans, och om du använder REST API, bör du återanvänder en enda HttpClient.
5. Variera ämnet för frågebegäranden så att sökningen görs via olika delar av ditt index. Variation är viktigt eftersom om du kör kontinuerligt samma sökförfrågningar, cachelagring av data ska börja visa prestanda bättre än den kan med en mer olika fråga inställd ut.
6. Variera strukturen för frågebegäranden så att du får olika typer av frågor. Inte alla sökfråga utför på samma nivå. Till exempel är en dokumentet lookup- eller Sök förslaget normalt snabbare än en fråga med ett stort antal fasetter och filter. Testa sammansättning bör innehålla olika frågor i ungefär samma förhållanden som förväntat i produktion.  

När du skapar dessa testarbetsbelastningar finns det vissa egenskaper för Azure Search att tänka på:

+ Det är möjligt överbelasta tjänsten genom att trycka på för många sökfrågor i taget. När detta inträffar visas HTTP 503-svarskoder. För att undvika en 503 under testningen kan du börja med olika områden i sökbegäranden att se skillnaderna i priserna för fördröjning när du lägger till flera sökförfrågningar.

+ Azure Search körs inte indexering uppgifter i bakgrunden. Om din tjänst hanterar fråge- och indexeringsarbetsbelastningar samtidigt, beakta detta genom att antingen introducera indexeringsjobb i din fråga tester eller genom att utforska alternativ för att köra indexerade jobb under Lågbelastningstider.

> [!NOTE]
> [Visual Studio Load testning](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) är ett mycket bra sätt att utföra dina benchmark testar eftersom den låter dig att köra HTTP-begäranden som du skulle behöva för att köra frågor mot Azure Search och aktiverar parallellisering av begäranden.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Skalning för stora frågearbetsbelastningar volym och begränsade begäranden
När du tar emot för många begränsade begäranden eller överskrider dina mål svarstid priser från en ökad frågebelastning, kan du se ut om du vill minska svarstiden priserna på något av två sätt:

1. **Öka repliker:**  En replik är som en kopia av dina data så att Azure Search för att belastningsutjämna förfrågningar mot flera kopior.  Alla belastningsutjämning och replikering av data i repliker som hanteras av Azure Search och du kan ändra antalet repliker som allokerats för din tjänst när som helst.  Du kan allokera upp till 12 repliker i en standardsöktjänst och 3 repliker i en grundläggande söktjänst. Repliker kan vara justeras antingen från den [Azure-portalen](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).
2. **Öka Search-nivå:**  Azure Search kommer in en [antal nivåer](https://azure.microsoft.com/pricing/details/search/) och var och en av de här nivåerna erbjuder olika nivåer av prestanda.  I vissa fall kan du ha så många frågor att den nivå du har inte kan ge tillräckligt med låg latens avgifter, även när repliker är överutnyttjade ut. I det här fallet kan du överväga att använda en av de högsta nivåerna för sökning, till exempel Azure Search S3-nivå som passar bra för scenarier med stort antal dokument och extremt hög frågearbetsbelastningar.

## <a name="scaling-for-slow-individual-queries"></a>Skalning för långsam enskilda frågor
En annan orsak till långa svarstider priser som anges är en enskild fråga tar för lång tid att slutföra. I det här fallet hjälper att lägga till repliker inte. Två alternativ möjliga alternativ som kan hjälpa till att inkludera följande:

1. **Öka partitioner** en partition är en mekanism för att dela data mellan extra resurser. Att lägga till en andra partitionen delar upp data i två, en tredje partition delar upp den i tre och så vidare. Ett positivt sidoeffekt är att långsammare frågor ibland utföra snabbare på grund av parallell datorbearbetning. Vi ha antecknat parallellisering på låg selektivitet frågor, till exempel frågor som matchar många dokument eller fasetterna som ger antalet över ett stort antal dokument. Eftersom betydande beräkning krävs för att bedöma relevans dokument eller för att räkna antalet dokument, lägga till extra partitioner hjälper till att slutföra frågor snabbare.  
   
   Det kan vara upp till 12 partitioner i Standard-söktjänst och 1 partition i grundläggande search-tjänsten.  Partitioner kan vara justeras antingen från den [Azure-portalen](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).

2. **Gräns för hög kardinalitet fält:** Ett fält med hög kardinalitet består av en fasettbar eller filtrerbara fält som har ett stort antal unika värden och därmed förbrukar betydande resurser när du beräknar resultatet. Exempelvis skulle anger ett fält som produkt-ID eller beskrivning som fasettbar/filtrerbara räknas som hög kardinalitet eftersom de flesta av värden från dokument till dokument är unika. Om möjligt begränsar du antalet hög kardinalitet fält.

3. **Öka Search-nivå:**  Flytta upp till kan en högre nivå för Azure Search vara ett annat sätt att förbättra prestanda för långsamma frågor. Varje högre nivån ger snabbare processorer och mer minne, som båda har en positiv inverkan på prestanda för frågor.

## <a name="scaling-for-availability"></a>Skalning för tillgänglighet
Repliker inte bara att minska svarstid men kan också tillåta för hög tillgänglighet. Med en enskild replik kan du förväntar dig periodiska stilleståndstid på grund av att servern startas om efter programuppdateringar eller för andra underhållshändelser som inträffar.  Det är därför viktigt att tänka på om programmet kräver hög tillgänglighet för sökningar (frågor) samt skrivningar (indexering händelser). Azure Search erbjuder alternativ för SLA på alla betalda search-erbjudanden med följande attribut:

* 2 repliker för hög tillgänglighet för skrivskyddade arbetsbelastningar (frågor)
* 3 eller fler repliker för hög tillgänglighet för skrivskyddade arbetsbelastningar (frågor och indexering)

Mer information om detta finns i [serviceavtal för Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Eftersom repliker är kopior av dina data, du har flera repliker kan Azure Search för machine omstarter och -underhåll mot en replik samtidigt som Frågekörningen kan fortsätta på andra repliker. Däremot om du tar bort replikerna kommer debiteras prestandaförsämring i fråga, förutsatt att dessa repliker har en outnyttjade resurs.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Skalning för geo-distribuerad arbetsbelastningar och geo-redundans
För geo-distribuerad arbetsbelastningar och har användare som är placerade är långt från datacentret som är värd för Azure Search högre latens priser. En lösning är att etablera flera söktjänster i regioner med närmare dessa användare. Azure Search ger för närvarande inte en automatiserad metod för Azure Search-index med geo-replikering i flera regioner, men det finns vissa tekniker som kan användas som kan göra den här processen enkel att implementera och hantera. Dessa beskrivs i nästa avsnitt.

Målet med en geo-distribuerad uppsättning söktjänster är att ha två eller flera index som är tillgängliga på två eller fler regioner där en användare dirigeras till Azure Search-tjänst som tillhandahåller den lägsta svarstiden som visas i det här exemplet:

   ![Cross-fliken tjänster efter region][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Synkronisera data över flera Azure Search-tjänster
Det finns två alternativ för att hålla dina distribuerade söktjänster synkroniserade som består av antingen med hjälp av den [Azure Search-indexeraren](search-indexer-overview.md) eller Push-API (kallas även den [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Använd indexerare för att uppdatera innehållet på flera tjänster

Om du redan använder indexerare på en tjänst kan konfigurera du en andra indexerare på en andra tjänst för att använda samma datakällobjektet som hämtar data från samma plats. Varje tjänst i varje region har sin egen indexeraren och ett målindex (din sökkorpus inte delas, vilket innebär att data dupliceras), men varje indexerare refererar till samma datakälla.

Här är ett övergripande visuellt objekt på den arkitekturen skulle se ut.

   ![Enskild datakälla med distribuerade indexerare och kombinationer av tjänsten][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Använda REST API: er för att skicka uppdateringar av innehållet på flera tjänster
Om du använder Azure Search REST API till [Skicka innehåll i ditt Azure Search-index](https://docs.microsoft.com/rest/api/searchservice/update-index), du kan synkronisera din olika söktjänster genom att skicka ändringar till alla söktjänster när en uppdatering krävs. Se till att hantera fall där en uppdatering till en söktjänst misslyckas men inte för andra söktjänster i din kod.

## <a name="leverage-azure-traffic-manager"></a>Utnyttja Azure Traffic Manager
[Med Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) gör att du kan dirigera begäranden till flera geografiskt placerade webbplatser som sedan backas upp av flera Azure Search-tjänsterna. En fördel av Traffic Manager är att den kan söka i Azure Search för att se till att den är tillgänglig och dirigera användare till alternativa söktjänster i händelse av avbrott. Dessutom kan kan du routning sökförfrågningar via Azure Web Sites, Azure Traffic Manager att läsa in saldo fall där webbplatsen är igång men inte Azure Search. Här är ett exempel på vilken arkitektur som utnyttjar Traffic Manager.

   ![Cross-fliken tjänster efter region, med central Traffic Manager][3]

## <a name="monitor-performance"></a>Övervaka prestanda
Azure Search ger dig möjlighet att analysera och övervaka prestanda för din tjänst via [söktrafikanalys](search-traffic-analytics.md). När du aktiverar den här funktionen och lägger till instrumentering i klientappen, kan du också logga enskilda sökningar samt aggregerade mätvärden till ett Azure Storage-konto som sedan kan bearbetas för analys eller visualiseras i Power BI. Mått insamlingar vis tillhandahålla prestandastatistik, till exempel genomsnittlig många frågor eller frågesvarstiderna. Dessutom kan åtgärden loggning du visa detaljer om specifika sökningar.

Trafikanalys är användbart för att förstå svarstid priset för det Azure Search-perspektivet. Eftersom frågan prestandamått loggas baseras på den tid som en fråga tar bearbetas fullständigt i Azure Search (från den tidpunkt som den begärs till när det skickas), kan du använda detta för att avgöra om det är problem med nätverkssvarstiden från Azure Search-tjänsten på klientsidan eller detaljer IDE av tjänsten, till exempel från Nätverksfördröjningen.  

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
