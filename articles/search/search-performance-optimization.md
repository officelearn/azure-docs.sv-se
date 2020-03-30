---
title: Skala för prestanda
titleSuffix: Azure Cognitive Search
description: Lär dig tekniker och metodtips för att justera Azure Cognitive Search-prestanda och konfigurera optimal skala.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212377"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Skala för prestanda på Azure Cognitive Search

I den här artikeln beskrivs metodtips för avancerade scenarier med avancerade krav på skalbarhet och tillgänglighet.

## <a name="start-with-baseline-numbers"></a>Börja med originalplansnummer

Innan du utför en större distributionsinsats ska du se till hur en typisk frågebelastning ser ut. Följande riktlinjer kan hjälpa dig att komma fram till baslinjefrågenummer.

1. Välj en målfördröjning (eller maximal tid) som en typisk sökbegäran ska ta att slutföra.

1. Skapa och testa en verklig arbetsbelastning mot din söktjänst med en realistisk datauppsättning för att mäta dessa svarstidshastigheter.

1. Börja med ett lågt antal frågor per sekund (QPS) och öka sedan gradvis antalet som körs i testet tills frågefördröjningen sjunker under det fördefinierade målet. Detta är ett viktigt riktmärke som hjälper dig att planera för skala när ditt program växer i användning.

1. Återanvänd HTTP-anslutningar om det är möjligt. Om du använder Azure Cognitive Search .NET SDK innebär det att du bör återanvända en instans eller [SearchIndexClient-instans,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) och om du använder REST API bör du återanvända en enda HttpClient.

1. Variera innehållet i frågebegäranden så att sökning sker över olika delar av indexet. Variation är viktigt eftersom om du kontinuerligt kör samma sökbegäranden börjar cachelagring av data att göra prestanda bättre än det kan med en mer olika frågeuppsättning.

1. Variera strukturen för frågebegäranden så att du får olika typer av frågor. Alla sökfrågor fungerar inte på samma nivå. Ett dokumentsöknings- eller sökförslag är till exempel vanligtvis snabbare än en fråga med ett stort antal aspekter och filter. Testsammansättning bör innehålla olika frågor, i ungefär samma förhållanden som du förväntar dig i produktionen.  

När du skapar dessa testarbetsbelastningar finns det vissa egenskaper hos Azure Cognitive Search att tänka på:

+ Det är möjligt att överbelasta din tjänst genom att trycka för många sökfrågor på en gång. När detta händer visas HTTP 503-svarskoder. Undvik 503 under testningen genom att börja med olika intervall av sökbegäranden för att se skillnaderna i svarstid när du lägger till fler sökbegäranden.

+ Azure Cognitive Search kör inte indexeringsuppgifter i bakgrunden. Om tjänsten hanterar fråge- och indexeringsarbetsbelastningar samtidigt, ta hänsyn till detta genom att antingen införa indexering av jobb i frågetesterna eller genom att utforska alternativ för att köra indexeringsjobb under lågtrafik.

> [!Tip]
> Du kan simulera en realistisk frågebelastning med hjälp av belastningstestverktyg. Prova [att läsa in testning med Azure DevOps](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) eller använd något av dessa [alternativ](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives).

## <a name="scale-for-high-query-volume"></a>Skala för hög frågevolym

En tjänst överbelastas när frågor tar för lång tid eller när tjänsten börjar släppa begäranden. Om detta händer kan du lösa problemet på ett av två sätt:

+ **Lägga till repliker**  

  Varje replik är en kopia av dina data, vilket gör att tjänsten kan läsa in aldobegäranden mot flera kopior.  All belastningsutjämning och replikering av data hanteras av Azure Cognitive Search och du kan ändra antalet repliker som allokerats för din tjänst när som helst. Du kan allokera upp till 12 repliker i en standardsöktjänst och 3 repliker i en grundläggande söktjänst. Repliker kan justeras antingen från [Azure-portalen](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).

+ **Skapa en ny tjänst på en högre nivå**  

  Azure Cognitive Search finns på ett [antal nivåer](https://azure.microsoft.com/pricing/details/search/) och var och en erbjuder olika prestandanivåer. I vissa fall kan du ha så många frågor att nivån du är på inte kan ge tillräcklig vändning, även när repliker är maxade ut. I det här fallet bör du överväga att flytta till en högre prestandanivå, till exempel Standard S3-nivån, utformad för scenarier med ett stort antal dokument och extremt höga frågearbetsbelastningar.

## <a name="scale-for-slow-individual-queries"></a>Skala för långsamma enskilda frågor

En annan orsak till hög latenshastighet är en enskild fråga som tar för lång tid att slutföra. I det här fallet hjälper det inte att lägga till repliker. Två möjliga alternativ som kan vara till hjälp är följande:

+ **Öka partitioner**

  En partition delar upp data över extra datorresurser. Två partitioner dela data på mitten, en tredje partition delar upp den i tredjedelar, och så vidare. En positiv bieffekt är att långsammare frågor ibland fungerar snabbare på grund av parallell databehandling. Vi har noterat parallellisering på låga selektivitetsfrågor, till exempel frågor som matchar många dokument eller aspekter som ger räknas över ett stort antal dokument. Eftersom betydande beräkning krävs för att få in relevansen av dokumenten, eller för att räkna antalet dokument, hjälper lägga till extra partitioner frågor att slutföra snabbare.  
   
  Det kan finnas högst 12 partitioner i standardsöktjänsten och 1 partition i den grundläggande söktjänsten. Partitioner kan justeras antingen från [Azure-portalen](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).

+ **Begränsa fält med hög kardinalitet**

  Ett fält med hög kardinalitet består av ett fasanvänt eller filterbart fält som har ett stort antal unika värden och som ett resultat förbrukar betydande resurser när du beräknar resultat. Om du till exempel anger ett produkt-ID- eller Beskrivningsfält som facetable/filterable räknas det som hög kardinalitet eftersom de flesta värden från dokument till dokument är unika. Om möjligt begränsar du antalet fält med hög kardinalitet.

+ **Öka söknivån**  

  Att flytta upp till en högre Azure Cognitive Search-nivå kan vara ett annat sätt att förbättra prestanda för långsamma frågor. Varje högre nivå ger snabbare processorer och mer minne, som båda har en positiv inverkan på frågeprestanda.

## <a name="scale-for-availability"></a>Skala för tillgänglighet

Repliker bidrar inte bara till att minska frågefördröjningen, utan kan också möjliggöra hög tillgänglighet. Med en enda replik bör du förvänta dig periodiska driftstopp på grund av omstarter av servern efter programuppdateringar eller andra underhållshändelser som inträffar. Därför är det viktigt att överväga om ditt program kräver hög tillgänglighet för sökningar (frågor) samt skrivningar (indexeringshändelser). Azure Cognitive Search erbjuder SLA-alternativ för alla betalda sökerbjudanden med följande attribut:

+ Två repliker för hög tillgänglighet för skrivskyddade arbetsbelastningar (frågor)

+ Tre eller flera repliker för hög tillgänglighet för läs-skriv-arbetsbelastningar (frågor och indexering)

Mer information om detta finns i [Azure Cognitive Search Service Level Agreement](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Eftersom repliker är kopior av dina data, med flera repliker kan Azure Cognitive Search göra omstarter av datorer och underhåll mot en replik, medan frågekörning fortsätter på andra repliker. Omvänt, om du tar bort repliker, kommer du att ådra fråga prestandaförsämring, förutsatt att dessa repliker var en underutnyttjad resurs.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Skala för geo-distribuerade arbetsbelastningar och geo-redundans

För geo-distribuerade arbetsbelastningar har användare som befinner sig långt från värddatacentret högre svarstidshastigheter. En begränsning är att tillhandahålla flera söktjänster i regioner med närmare närhet till dessa användare.

Azure Cognitive Search tillhandahåller för närvarande inte en automatiserad metod för georeplikering av Azure Cognitive Search-index i olika regioner, men det finns vissa tekniker som kan användas som kan göra den här processen enkel att implementera och hantera. Dessa beskrivs i de närmaste avsnitten.

Målet med en geodistribuerad uppsättning söktjänster är att ha två eller flera index tillgängliga i två eller flera regioner, där en användare dirigeras till Azure Cognitive Search-tjänsten som ger den lägsta svarstiden som visas i det här exemplet:

   ![Korsflik för tjänster efter region][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Synkronisera data mellan flera tjänster

Det finns två alternativ för att hålla dina distribuerade söktjänster synkroniserade, som består av att antingen använda [Azure Cognitive Search Indexer](search-indexer-overview.md) eller Push API (kallas även [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Använda indexerare för att uppdatera innehåll på flera tjänster

Om du redan använder indexeraren på en tjänst kan du konfigurera en andra indexerare på en andra tjänst så att samma datakällobjekt används och hämta data från samma plats. Varje tjänst i varje region har sin egen indexerare och ett målindex (ditt sökindex delas inte, vilket innebär att data dupliceras), men varje indexerare refererar till samma datakälla.

Här är en hög nivå visuella av hur den arkitekturen skulle se ut.

   ![Enkel datakälla med distribuerade indexerare och servicekombinationer][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Använd REST-API:er för att driva innehållsuppdateringar på flera tjänster

Om du använder AZURE Cognitive Search REST API för att [skicka innehåll i ditt Azure Cognitive Search-index](https://docs.microsoft.com/rest/api/searchservice/update-index)kan du synkronisera dina olika söktjänster genom att skicka ändringar till alla söktjänster när en uppdatering krävs. I koden ska du se till att hantera fall där en uppdatering till en söktjänst misslyckas men lyckas efter andra söktjänster.

## <a name="leverage-azure-traffic-manager"></a>Utnyttja Azure Traffic Manager

[Med Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) kan du dirigera begäranden till flera geolokala webbplatser som sedan backas upp av flera söktjänster. En fördel med Traffic Manager är att den kan avsöka Azure Cognitive Search för att säkerställa att den är tillgänglig och dirigera användare till alternativa söktjänster i händelse av driftstopp. Om du dirigerar sökbegäranden via Azure-webbplatser kan du dessutom läsa in aldofall där webbplatsen är uppe men inte Azure Cognitive Search. Här är ett exempel på vad arkitekturen som utnyttjar Traffic Manager.

   ![Korsflik för tjänster efter region, med central trafikchef][3]

## <a name="next-steps"></a>Nästa steg

Mer information om prisnivåer och servicegränser för var och en finns i [Servicegränser](search-limits-quotas-capacity.md). Se [Planera för kapacitet för](search-capacity-planning.md) att lära dig mer om partitions- och replikkombinationer.

En diskussion om prestanda och demonstrationer av de tekniker som beskrivs i den här artikeln finns i följande video:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
