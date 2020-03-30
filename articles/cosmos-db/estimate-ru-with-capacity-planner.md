---
title: Uppskatta kostnader med hjälp av Azure Cosmos DB-kapacitetsplaneraren
description: Azure Cosmos DB kapacitetsplanerare kan du uppskatta det dataflöde (RU /s) som krävs och kostnaden för din arbetsbelastning. I den här artikeln beskrivs hur du använder den nya versionen av kapacitetsplaneraren för att uppskatta det dataflöde och den kostnad som krävs.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68707635"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Uppskatta RU/s med hjälp av Azure Cosmos DB-kapacitetsplaneraren

Konfigurera dina Azure Cosmos-databaser och behållare med rätt mängd etablerat dataflöde, eller [Begär enheter (RU/s)](request-units.md), för din arbetsbelastning är viktigt för att optimera kostnader och prestanda. I den här artikeln beskrivs hur du använder Azure Cosmos [DB-kapacitetsplaneraren](https://cosmos.azure.com/capacitycalculator/) för att få en uppskattning av de nödvändiga RU/s och kostnaden för din arbetsbelastning. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Så här beräknar du dataflöde och kostnad med Azure Cosmos DB-kapacitetsplanerare

Kapacitetsplaneraren kan användas i två lägen.

|**Läge**  |**Beskrivning**  |
|---------|---------|
|Basic|Ger en snabb, hög nivå RU / s och kostnadsuppskattning. Det här läget förutsätter standardinställningarna för Azure Cosmos DB för indexeringsprincip, konsekvens och andra parametrar. <br/><br/>Använd grundläge för en snabb uppskattning på hög nivå när du utvärderar en potentiell arbetsbelastning som ska köras på Azure Cosmos DB.|
|Avancerat|Ger en mer detaljerad RU/s och kostnadsuppskattning, med möjlighet att justera ytterligare inställningar – indexeringsprincip, konsekvensnivå och andra parametrar som påverkar kostnaden och dataflödet. <br/><br/>Använd avancerat läge när du uppskattar RU/s för ett nytt projekt eller vill ha en mer detaljerad uppskattning. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Uppskatta etablerat dataflöde och kostnad med hjälp av grundläge
Om du vill få en snabb uppskattning för din arbetsbelastning med hjälp av det grundläggande läget navigerar du till [kapacitetsplaneraren](https://cosmos.azure.com/capacitycalculator/). Ange följande parametrar baserat på din arbetsbelastning: 

|**Input**  |**Beskrivning**  |
|---------|---------|
|Antal regioner|Azure Cosmos DB är tillgängligt i alla Azure-regioner. Välj hur många regioner som krävs för din arbetsbelastning. Du kan associera valfritt antal regioner med ditt Cosmos-konto. Mer information finns i [den globala distributionen](distribute-data-globally.md) i Azure Cosmos DB.|
|Skrivningar för flera regioner|Om du aktiverar [skrivningar med flera regioner](distribute-data-globally.md#key-benefits-of-global-distribution)kan ditt program läsa och skriva till valfri Azure-region. Om du inaktiverar skrivningar med flera regioner kan programmet skriva data till en enda region. <br/><br/> Aktivera skrivningar med flera regioner om du förväntar dig att ha en aktiv aktiv arbetsbelastning som kräver skrivningar med låg latens i olika regioner. Till exempel en IOT-arbetsbelastning som skriver data till databasen vid stora volymer i olika regioner. <br/><br/> Flera regioner skriver garanterar 99,999% läs- och skrivtillgänglighet. Skrivningar i flera regioner kräver mer dataflöde jämfört med de enskilda skrivregionerna. Mer information finns i [hur ru:er skiljer sig åt för en- och flerskrivningsregioner.](optimize-cost-regions.md)|
|Totalt antal lagrade data (per region)|Totalt antal uppskattade data som lagras i GB i en enda region.|
|Objektstorlek|Den uppskattade storleken på dataobjektet (t.ex. dokument), från 1 KB till 2 MB. |
|Läsningar per region|Antal förväntade läsningar per sekund. |
|Skriver per region|Antal förväntade skrivningar per sekund. |

När du har fyllt i de nödvändiga detaljerna väljer du **Beräkna**. På fliken **Kostnadsuppskattning** visas den totala kostnaden för lagring och etablerat dataflöde. Du kan expandera länken **Visa information på** den här fliken för att få en uppdelning av det dataflöde som krävs för läs- och skrivbegäranden. Varje gång du ändrar värdet för ett fält väljer du **Beräkna** för att beräkna om den uppskattade kostnaden. 

![Grundläggande läge för kapacitetsplanerare](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Uppskatta etablerat dataflöde och kostnad med avancerat läge

Med avancerat läge kan du ange fler inställningar som påverkar RU/s uppskattning. Om du vill använda det här alternativet navigerar du till [kapacitetsplaneraren](https://cosmos.azure.com/capacitycalculator/) och loggar in på verktyget med ett konto som du använder för Azure. Inloggningsalternativet finns i det högra hörnet. 

När du har loggat in kan du se ytterligare fält jämfört med fälten i grundläge. Ange ytterligare parametrar baserat på din arbetsbelastning. 

|**Input**  |**Beskrivning**  |
|---------|---------|
|API|Azure Cosmos DB är en multi-modell och multi-API-tjänst. För nya arbetsbelastningar väljer du SQL (Core) API. |
|Antal regioner|Azure Cosmos DB är tillgängligt i alla Azure-regioner. Välj hur många regioner som krävs för din arbetsbelastning. Du kan associera valfritt antal regioner med ditt Cosmos-konto. Mer information finns i [den globala distributionen](distribute-data-globally.md) i Azure Cosmos DB.|
|Skrivningar för flera regioner|Om du aktiverar [skrivningar med flera regioner](distribute-data-globally.md#key-benefits-of-global-distribution)kan ditt program läsa och skriva till valfri Azure-region. Om du inaktiverar skrivningar med flera regioner kan programmet skriva data till en enda region. <br/><br/> Aktivera skrivningar med flera regioner om du förväntar dig att ha en aktiv aktiv arbetsbelastning som kräver skrivningar med låg latens i olika regioner. Till exempel en IOT-arbetsbelastning som skriver data till databasen vid stora volymer i olika regioner. <br/><br/> Flera regioner skriver garanterar 99,999% läs- och skrivtillgänglighet. Skrivningar i flera regioner kräver mer dataflöde jämfört med de enskilda skrivregionerna. Mer information finns i [hur ru:er skiljer sig åt för en- och flerskrivningsregioner.](optimize-cost-regions.md)|
|Standardkonsekvens|Azure Cosmos DB stöder 5 konsekvensnivåer, så att utvecklare kan balansera avvägningen mellan kompromisser om konsekvens, tillgänglighet och svarstid. Mer information finns i artikeln [om konsekvensnivåer.](consistency-levels.md) <br/><br/> Som standard använder Azure Cosmos DB sessionskonsekvens, vilket garanterar möjligheten att läsa dina egna skrivningar i en session. <br/><br/> Att välja stark eller begränsad föråldring kräver dubbelt så hög som krävs RU/s för läsningar, jämfört med session, konsekvent prefix och eventuell konsekvens. Stark konsekvens med flera regioner skriver stöds inte och kommer automatiskt standard till en region skriver med stark konsekvens. |
|Indexeringspolicy|Som standard [indexerar](index-policy.md) Azure Cosmos DB alla egenskaper i alla objekt för flexibla och effektiva frågor (kartor till principen **Automatisk** indexering). <br/><br/> Om du väljer **av**indexeras ingen av egenskaperna. Detta resulterar i den lägsta RU-avgiften för skrivningar. Välj **av** principen om du förväntar dig att bara göra [punktläsningar](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) (nyckelvärdesökningar) och/eller skrivningar och inga frågor. <br/><br/> Med anpassad indexeringsprincip kan du inkludera eller utesluta specifika egenskaper från indexet för lägre skrivdataflöde och lagring. Mer information finns i [indexeringsprinciper](index-overview.md) och [exempel på indexeringsprinciper.](how-to-manage-indexing-policy.md#indexing-policy-examples)|
|Totalt antal lagrade data (per region)|Totalt antal uppskattade data som lagras i GB i en enda region.|
|Arbetsbelastningsläge|Välj **Stadig** om arbetsbelastningsvolymen är konstant. <br/><br/> Välj **Variabel** om arbetsbelastningsvolymen ändras med tiden.  Till exempel under en viss dag eller en månad. <br/><br/> Följande inställningar är tillgängliga om du väljer alternativet variabel arbetsbelastning:<ul><li>Procentandel av tiden vid topp: Procentandel av tiden i en månad där din arbetsbelastning kräver högsta (högsta) dataflöde. <br/><br/> Till exempel, om du har en arbetsbelastning som har hög aktivitet under 09:00 till 18:00 veckodag kontorstid, då andelen tid vid topp är: 45 timmar vid topp / 730 timmar / månad = ~ 6%.<br/><br/></li><li>Läsningar/sek per region på topp - Antal läsningar som förväntas per sekund på topp.</li><li>Skriver per region på topp – Antal förväntade skrivningar per sekund på topp.</li><li>Läser/sek per region låg topp – Antal läsningar som förväntas per sekund under lågtrafik.</li><li>Skriver per region låg topp – Antal skrivningar som förväntas per sekund under lågtrafik.</li></ul>Med högsta och lågbelastningsintervall kan du optimera din kostnad genom [att programmässigt skala upp](set-throughput.md#update-throughput-on-a-database-or-a-container) och ned det etablerade dataflödet uppåt och nedåt.|
|Objektstorlek|Storleken på dataobjektet (t.ex. dokument), från 1 KB till 2 MB. <br/><br/>Du kan också **ladda upp exempel (JSON)** dokument för en mer exakt uppskattning.<br/><br/>Om din arbetsbelastning har flera typer av objekt (med olika JSON-innehåll) i samma behållare kan du ladda upp flera JSON-dokument och få uppskattningen. Använd knappen **Lägg till nya objekt** om du vill lägga till flera JSON-exempeldokument.|

Du kan också använda knappen **Spara uppskattning** för att hämta en CSV-fil som innehåller den aktuella uppskattningen. 

![Avancerat läge för kapacitetsplanerare](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Priserna som visas i Azure Cosmos DB-kapacitetsplaneraren är uppskattningar baserade på de offentliga prisnivåerna för dataflöde och lagring. Alla priser visas i us-dollar. Se [azure Cosmos DB-prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/) för att se alla priser per region.  

## <a name="estimating-throughput-for-queries"></a>Uppskatta dataflöde för frågor

Azure Cosmos kapacitetskalkylator förutsätter punktläsningar (en läsning av ett enda objekt, t.ex. Om du vill uppskatta det dataflöde som behövs för frågor kör du frågan på en representativ datauppsättning i en Cosmos-behållare och [hämtar RU-avgiften](find-request-unit-charge.md). Multiplicera RU-avgiften med antalet frågor som du räknar med att köra per sekund för att få den totala RU/s som krävs. 

Till exempel, om din arbetsbelastning kräver en fråga, ``SELECT * FROM c WHERE c.id = 'Alice'`` som körs 100 gånger per sekund, och RU avgiften för frågan är 10 RU, behöver du 100 fråga / sek * 10 RU / fråga = 1000 RU / s totalt för att tjäna dessa begäranden. Lägg till dessa RU/s i RU/s som krävs för alla läsningar eller skrivningar som händer i din arbetsbelastning.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Cosmos DB:s prismodell](how-pricing-works.md).
* Skapa ett nytt [Cosmos-konto, databas och behållare](create-cosmosdb-resources-portal.md).
* Läs om hur du [optimerar etablerad dataflödeskostnad](optimize-cost-throughput.md).
* Lär dig hur du [optimerar kostnaden med reserverad kapacitet](cosmos-db-reserved-capacity.md).

