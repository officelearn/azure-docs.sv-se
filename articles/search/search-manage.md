---
title: Administration av tjänsten för Azure Search i Azure-portalen
description: Hantera Azure Search värdbaserade moln search-tjänsten på Microsoft Azure med Azure-portalen.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 896a12db1ac196b6de1e57dde9b5910e11dcc8c7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administration av tjänsten för Azure Search i Azure-portalen
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Search är en helt hanterad, molnbaserad search-tjänst som används för att skapa en miljö med omfattande Sök i anpassade appar. Den här artikeln beskriver den *tjänsten administration* uppgifter som du kan utföra i den [Azure-portalen](https://portal.azure.com) för en söktjänst som du redan har etablerats. *Tjänsten administration* är förenklad avsiktligt begränsat till följande uppgifter:

* Hantera och säker åtkomst till den *api-nycklar* används för Läs- eller skrivbehörighet till din tjänst.
* Justera kapacitet tjänster genom att ändra fördelningen av partitioner och repliker.
* Övervaka Resursanvändning, i förhållande till din tjänstnivån begränsningarna.

Observera att *uppgradera* inte anges som administrativa åtgärder. Eftersom resurser tilldelas när tjänsten etableras, kräver flytt till ett annat skikt en ny tjänst. Mer information finns i [skapa en Azure Search-tjänst](search-create-service-portal.md).

> [!Tip]
> Letar du efter information om hur du analyserar Sök trafik eller fråga prestanda? Få insikter om frågan volym, vilket villkor personer söka efter, och hur lyckade sökresultat finns i steg kunder på specifika dokument i ditt index. Anvisningar finns [Sök trafik Analytics för Azure Search](search-traffic-analytics.md), [övervaka användnings- och](search-monitor-usage.md), och [prestanda och optimering](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Administratörsbehörighet
Etablera eller inaktivering av själva tjänsten kan göras av en administratör för Azure-prenumeration eller en medadministratör.

Inom en tjänst har alla med åtkomst till tjänsten URL: en och en admin api-nyckel skrivskyddad åtkomst till tjänsten. Läs-/ skrivåtkomst ger möjlighet att lägga till, ta bort eller ändra serverobjekt, inklusive api-nycklar, index, indexerare, datakällor, scheman och rolltilldelningar som implementeras via [RBAC användardefinierade roller](search-security-rbac.md).

Alla användarinteraktion med Azure Search hamnar i ett läge: skrivskyddad åtkomst till tjänsten (administratörsbehörighet) eller skrivskyddad åtkomst till tjänsten (frågan rättigheter). Mer information finns i [hantera api-nycklar](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Loggning och Systeminformation
Azure Search visar inte gränssnittshändelsen loggfiler för en enskild tjänst antingen via portalen eller programgränssnitt. På den grundläggande nivån och över Microsoft övervakar Azure Search-tjänster för 99,9% tillgänglighet per servicenivåavtal (SLA). Om tjänsten är långsam eller dataflödet för begäran understiger SLA tröskelvärden supportteam Granska loggfilerna som är tillgängliga för dem och åtgärda problemet.

Vad gäller allmän information om tjänsten, kan du hämta information på följande sätt:

* I portalen på instrumentpanelen i tjänsten via meddelanden, egenskaper och statusmeddelanden.
* Med hjälp av [PowerShell](search-manage-powershell.md) eller [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/) till [hämta tjänstegenskaper](https://docs.microsoft.com/rest/api/searchmanagement/services), eller status på index Resursanvändning.
* Via [söka trafik analytics](search-traffic-analytics.md), som nämndes tidigare.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Övervaka Resursanvändning
I instrumentpanelen för är Resursövervakning begränsad till den information som visas i instrumentpanelen för tjänsten och några mått som du kan hämta genom att fråga tjänsten. På instrumentpanelen i tjänsten i avsnittet användning Se du snabbt om partitionen resurs nivåer är lämpliga för ditt program.

Med den Söktjänsts-REST API kan du räkna med dokument och index programmässigt: 

* [Få indexstatistik](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Antal dokument](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Disaster recovery och tjänsten avbrott

Även om vi kan rädda dina data, tillhandahåller Azure Search inte instant växling vid fel i tjänsten om det finns ett avbrott på klustret eller data center. Om ett kluster misslyckas i datacentret, driftteamet identifierar och fungerar om du vill återställa tjänsten. Du får driftstopp under återställning av tjänsten. Du kan begära servicekrediter att kompensera för tjänsten per den [serviceavtalet (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Om servicen krävs vid katastrofalt fel utanför Microsofts kontroll kunde [etablera en ytterligare tjänst](search-create-service-portal.md) i en annan region och implementera en strategi för geo-replikering så index är fullständigt redundanta för alla tjänster.

Kunder som använder [indexerare](search-indexer-overview.md) att fylla i och uppdatera index kan hantera katastrofåterställning via geo-specifika indexerare utnyttja samma datakälla. Två tjänster i olika regioner, kör en indexerare kan indexera från samma datakälla att uppnå geo-redundans. Om du indexerar från datakällor som också geo-redundant, Tänk på att Azure Search indexerare kan bara utföra inkrementell indexering från primära repliker. I en redundansväxling måste du peka indexeraren igen på den primära repliken. 

Om du inte använder indexerare, använder du din programkod för att push-objekt och data till olika söktjänster parallellt. Mer information finns i [prestanda och optimering i Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Eftersom Azure Search inte är en lösning för lagring av primära data, tillhandahåller vi en formell mekanism för självbetjäning säkerhetskopiering och återställning. Din programkod som används för att skapa och fylla i ett index är det faktiska återställningsalternativet om du av misstag tar bort ett index. 

Om du vill bygga om index, ta bort den (förutsatt att den finns), återskapa indexet i tjänsten och Läs in igen genom att hämta data från ditt primära dataarkiv. Alternativt kan du nå till [kundsupport]() till restvärde index om det finns ett regionalt strömavbrott.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Skala upp eller ned
Varje search-tjänsten startar med minst en replik och en partition. Om du har registrerat dig för en [nivå som ger dedicerade resurser](search-limits-quotas-capacity.md), klicka på den **skala** panelen i tjänsten instrumentpanelen för att justera Resursanvändning.

När du lägger till kapacitet via antingen resurs, använder tjänsten dem automatiskt. Ingen ytterligare åtgärd krävs från din sida, men det finns en viss fördröjning innan effekten av den nya resursen är en bra. Det kan ta 15 minuter eller mer att etablera ytterligare resurser.

 ![][10]

### <a name="add-replicas"></a>Lägg till repliker
Öka frågor per sekund (QPS) eller att uppnå hög tillgänglighet görs genom att lägga till repliker. Varje replik har en kopia av ett index, så att lägga till en mer replik översätter till en mer index som är tillgängliga för hantering av tjänstbegäranden för frågan. Det krävs minst 3 repliker för hög tillgänglighet (se [kapacitetsplanering](search-capacity-planning.md) information).

En söktjänst med flera repliker kan läsa in frågebegäranden via ett större antal index. Ges en nivå av frågan volym ska frågan genomströmning vara snabbare när det finns fler kopior av indexet tillgängliga begäran. Om du upplever svarstid du förväntar dig en positiv inverkan på prestanda när ytterligare repliker är online.

Även om frågan genomströmning går upp när du lägger till repliker, den inte exakt dubbla eller trippel när du lägger till repliker din tjänst. Alla sökprogram regleras av externa faktorer som kan ha inverkan på prestanda för frågor. Avancerade frågor och Nätverksfördröjningen är två faktorer som bidrar till variationer i frågan svarstider.

### <a name="add-partitions"></a>Lägg till partitioner
De flesta tjänstprogram behöver inbyggda ha flera repliker i stället för partitioner. Du kan lägga till partitioner om du har registrerat dig för tjänsten som Standard i de fall där en ökad dokumentantal krävs. Grundnivån tillhandahåller inte för fler partitioner.

Standardnivån, läggs partitioner i multiplar av 12 (särskilt 1, 2, 3, 4, 6 och 12). Det här är en artefakt för horisontell partitionering. Ett index skapas i 12 delar som kan alla lagras på 1 partition eller lika uppdelat 2, 3, 4, 6 och 12 partitioner (en Fragmentera per partition).

### <a name="remove-replicas"></a>Ta bort repliker
Du kan minska repliker när sökningen frågan belastningar har normaliserade (till exempel efter helgdag försäljning är över) efter perioder med hög frågan volymer.

Gör du genom att flytta skjutreglaget replikering tillbaka till ett lägre värde. Det finns inga ytterligare steg som krävs från din sida. Minska replikantalet avsäger virtuella datorer i datacenter. Dina frågor och införandet åtgärder körs på virtuella färre datorer än före. Det lägsta tillåtna värdet är en replik.

### <a name="remove-partitions"></a>Ta bort partitioner
Till skillnad från att ta bort repliker, vilket kräver inget extra arbete från din sida, kanske vissa arbetet för att göra om du använder mer lagringsutrymme än kan minskas. Till exempel om lösningen använder tre partitioner, genererar downsizing till en eller två partitioner ett fel om det nya lagringsutrymmet är mindre än vad som krävs. Som förväntat, är dina val för att ta bort index eller dokument inom ett associerat index Frigör utrymme eller behålla den aktuella konfigurationen.

Det finns inga identifieringsmetod som talar om vilka index shards lagras på specifika partitioner. Varje partition innehåller cirka 25 GB lagring, så du måste minska lagring till en storlek som kan utföras med antalet partitioner som du har. Om du vill återgå till en partition måste alla 12 delar att få plats.

Om du vill hjälpa till med framtida planering, kanske du vill kontrollera utrymme (med hjälp av [hämta indexstatistik](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) att se hur mycket du faktiskt används. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Bästa metoder för att skala och distribution
Den här 30 minuters videon granskar Metodtips för av avancerade distributionsscenarier, inklusive fördelade arbetsbelastningar. Du kan också se [prestanda och optimering i Azure Search](search-performance-optimization.md) för artiklar som omfattar samma punkter.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Nästa steg
När du förstår begreppen bakom tjänsten administration kan du använda [PowerShell](search-manage-powershell.md) att automatisera uppgifter.

Vi rekommenderar också att granska den [prestanda och optimering artikel](search-performance-optimization.md).

En annan rekommenderar vi att titta på videon som anges i föregående avsnitt. Det ger djupare täckning av tekniker som nämns i det här avsnittet.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



