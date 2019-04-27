---
title: Administration av tjänster för Azure Search i portalen – Azure Search
description: Hantera en Azure Search-tjänst, en värdbaserad molnsöktjänst på Microsoft Azure, med hjälp av Azure portal.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d5820c927b88eba37eaf092dfd4b209180bfc8eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565447"
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administration av tjänster för Azure Search i Azure portal
> [!div class="op_single_selector"]
> * [PowerShell](search-manage-powershell.md)
> * [REST-API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Search är en fullständigt hanterad, molnbaserad search-tjänst som används för att skapa en fullständig sökfunktion i anpassade appar. Den här artikeln beskriver administrationsuppgifter för tjänsten som du kan utföra i den [Azure-portalen](https://portal.azure.com) för en search-tjänst som du redan har etablerat. Administration av tjänster är lätta i design, begränsat till följande uppgifter:

> [!div class="checklist"]
> * Hantera åtkomst till den *api-nycklar* används för Läs- eller skrivbehörighet till din tjänst.
> * Justera tjänstkapacitet genom att ändra allokeringen av partitioner och -repliker.
> * Övervaka Resursanvändning, i förhållande till gränsvärdena för din tjänstnivå.

Observera att *uppgradera* har inte listats som en administrativ åtgärd. Eftersom resurser tilldelas när tjänsten har etablerats, måste flytta till en annan nivå en ny tjänst. Mer information finns i [skapa en Azure Search-tjänst](search-create-service-portal.md).

> [!Tip]
> Behöver du hjälp med att analysera search trafik eller fråga prestanda? Du kan övervaka fråga volym, vilket villkor personer söka efter och hur lyckad sökning resultatet är i guida kunder till specifika dokument i indexet. Mer information finns i [Search Traffic Analytics för Azure Search](search-traffic-analytics.md), [övervaka användning och fråga mått](search-monitor-usage.md), och [prestanda och optimering](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Administratörsbehörighet
Etablering eller inaktivering av själva tjänsten kan göras av en administratör för Azure-prenumeration eller delad administratör.

Inom en tjänst har alla med tillgång till tjänstens URL och en api-administratörsnyckel skrivskyddad åtkomst till tjänsten. Läs-/ skrivåtkomst ger dig möjlighet att lägga till, ta bort eller ändra serverobjekt, inklusive api-nycklar, index, indexerare, datakällor, scheman och rolltilldelningar som implementerats via [RBAC-definierade roller](search-security-rbac.md).

Alla användarinteraktion med Azure Search som ligger inom något av följande lägen: skrivskyddad åtkomst till tjänsten (administratörsbehörighet) eller skrivskyddad åtkomst till tjänsten (fråga rights). Mer information finns i [hantera api-nycklar](search-security-api-keys.md).

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>Loggning och Systeminformation
Azure Search exponerar inte loggfilerna för en enskild tjänst antingen via portalen eller programgränssnitt. På Basic-nivån och över Microsoft övervakar alla Azure Search-tjänsterna för 99,9% tillgänglighet per servicenivåavtal (SLA). Om tjänsten är långsam eller dataflödet för begäran ligger under SLA tröskelvärden supportteam Granska loggfilerna som är tillgängliga för dem och åtgärda problemet.

När det gäller allmän information om din tjänst, kan du få information på följande sätt:

* I portalen på instrumentpanelen för tjänsten via aviseringar, egenskaper och statusmeddelanden.
* Med hjälp av [PowerShell](search-manage-powershell.md) eller [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/) till [hämta tjänstegenskaper](https://docs.microsoft.com/rest/api/searchmanagement/services), eller status på index Resursanvändning.
* Via [söktrafikanalys](search-traffic-analytics.md), enligt tidigare notering.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Övervaka Resursanvändning
Instrumentpanelen för är Resursövervakning begränsad till informationen som visas i instrumentpanelen för tjänsten och några mått som du kan få genom att fråga tjänsten. På instrumentpanelen för tjänsten i avsnittet användning kan du snabbt se om partitionen resursnivåer är lämpliga för ditt program. Om du vill samla in och spara loggade händelser kan du etablera externa resurser, till exempel Azure-övervakning. Mer information finns i [övervakning av Azure Search](search-monitor-usage.md).

Med Search Service REST API kan få du ett antal på dokument och index programmässigt: 

* [Hämta Indexstatistiken](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Antal dokument](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Disaster recovery och service avbrott

Vi kan rädda dina data, tillhandahåller Azure Search ingen omedelbar redundans av tjänsten om det uppstår ett avbrott på klustret eller data center. Om det inte går att ett kluster i datacentret, driftsteamet identifierar och arbetar för att återställa tjänsten. Du får driftstopp under återställning av tjänsten, men du kan begära servicekrediter att kompensera för tjänsten otillgänglighet per den [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Om tjänsternas kontinuitet krävs i händelse av kritiska fel utanför Microsofts kontroll, kan du [etablera en ytterligare tjänst](search-create-service-portal.md) i en annan region och implementera en geo-replikering-strategi för att se till att index är fullständigt redundanta för alla tjänster.

Kunder som använder [indexerare](search-indexer-overview.md) att fylla i och uppdatera index kan hantera katastrofåterställning via geo-specifika indexerare att använda samma datakälla. Två tjänster i olika regioner, som kör en indexerare kan indexera samma datakälla för att uppnå geo-redundans. Om du indexerar från datakällor som också är geo-redundant, Tänk på att Azure Search-indexerare kan bara utföra inkrementell indexering från primära repliker. I en redundansväxling måste du peka indexeraren igen på den primära repliken. 

Om du inte använder indexerare, använder du din programkod för att push-objekt och data till olika söktjänster parallellt. Mer information finns i [prestanda och optimering i Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Eftersom Azure Search inte är en lösning för lagring av primärdata, erbjuder vi inte en formell mekanism för självbetjäning säkerhetskopiering och återställning. Din programkod som används för att skapa och fylla ett index är det faktiska restore-alternativet om du av misstag tar bort ett index. 

Om du vill bygga om index, ta bort den (förutsatt att den finns), återskapa indexet i tjänsten och Läs in på nytt genom att hämta data från det primära datalagret.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Skala upp eller ned
Varje söktjänst börjar med minst en replik och en partition. Om du har registrerat sig för en [nivå som tillhandahåller dedikerade resurser](search-limits-quotas-capacity.md), klickar du på den **skala** panelen på instrumentpanelen för att justera Resursanvändning.

När du lägger till kapacitet via antingen resursen använder tjänsten dem automatiskt. Ingen ytterligare åtgärd krävs från din sida, men det finns en fördröjning innan effekten av den nya resursen realiseras. Det kan ta 15 minuter eller mer att etablera ytterligare resurser.

 ![][10]

### <a name="add-replicas"></a>Lägg till repliker
Öka frågor per sekund (QPS) eller uppnå hög tillgänglighet gör du genom att lägga till repliker. Varje replik har en kopia av ett index så att lägga till en mer replik översätts till en mer index för hantering av tjänsten frågebegäranden. Det krävs minst 3 repliker för hög tillgänglighet (finns i [kapacitetsplanering](search-capacity-planning.md) information).

En söktjänst har flera repliker kan läsa in frågebegäranden för ett större antal index. Med en nivå av frågan volymen kan kommer frågedataflöde att vara snabbare när det finns fler kopior av index som är tillgängliga som kan hantera begäran. Om du upplever svarstid, du kan förvänta dig en positiv inverkan på prestanda när ytterligare repliker är online.

Även om frågedataflöde går upp när du lägger till repliker, den inte exakt dubbelklicka eller tredubbla när du lägger till repliker till din tjänst. Alla sökprogram omfattas externa faktorer som kan skada frågeprestanda. Komplexa frågor och svarstid för nätverk är två faktorer som bidrar till variationer i frågesvarstiderna.

### <a name="add-partitions"></a>Lägg till partitioner
De flesta tjänstprogram har inbyggda behov av mer repliker snarare än partitioner. För de fall där en ökad dokumentantal krävs, kan du lägga till partitioner om du har registrerat dig för Standard-tjänsten. Basic-nivån ger inte för fler partitioner.

På Standard-nivån partitioner har lagts till i multipler av 12 (mer specifikt 1, 2, 3, 4, 6 eller 12). Det här är en metod med horisontell partitionering. Ett index skapas i 12 shards, vilket kan alla lagras på 1 partition eller jämt i 2, 3, 4, 6 eller 12 partitioner (en shard per partition).

### <a name="remove-replicas"></a>Ta bort repliker
Du kan använda skjutreglaget för att minska repliker när search frågebelastningar har normaliserats (till exempel efter helgdag försäljning har överskridit) efter perioder med hög ställa frågor till volymer. Det finns inga ytterligare åtgärder krävs från din sida. Sänka replikantalet avsäger virtuella datorer i datacentret. Åtgärder för din fråga och inmatning nu körs på virtuella färre datorer än innan. Minimikravet är en replik.

### <a name="remove-partitions"></a>Ta bort partitioner
Till skillnad från tar bort repliker, vilket kräver ingen extra ansträngning från din sida måste kanske du vissa arbete att göra om du använder mer lagring än vad som kan minskas. Till exempel om lösningen använder tre partitioner, genereras downsizing till en eller två partitioner ett fel om det nya lagringsutrymmet är mindre än vad som krävs för som är värd för ditt index. Dina val är att ta bort index eller dokument inom ett associerat index Frigör utrymme eller behålla den aktuella konfigurationen som förväntat.

Det finns inga identifieringsmetod som talar om vilka index shards lagras på specifika partitioner. Varje partition innehåller cirka 25 GB i lagring, så du måste minska lagring till en storlek som kan användas av antalet partitioner som du har. Om du vill återställa till en partition måste alla 12 shards att få plats.

För att hjälpa till med framtida planering, kanske du vill kontrollera storage (med hjälp av [hämta Indexstatistiken](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) att se hur mycket du faktiskt används. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Bästa metoder för skalning och distribution
Den här 30-minuters videon går igenom metodtips för av avancerade distributionsscenarier, bland annat geo-distribuerad arbetsbelastningar. Du kan också se [prestanda och optimering i Azure Search](search-performance-optimization.md) för hjälpsidor som omfattar samma punkter.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Nästa steg
När du förstår begreppen bakom tjänstadministration kan du överväga att använda [PowerShell](search-manage-powershell.md) att automatisera uppgifter.

Vi rekommenderar också granska den [prestanda och optimering artikeln](search-performance-optimization.md).

En annan rekommendation är att titta på videon som anges i föregående avsnitt. Det ger bättre täckning av de metoder som nämns i det här avsnittet.

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



