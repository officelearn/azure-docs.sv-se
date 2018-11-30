---
title: Azure SQL Data Warehouse-hantering och övervakning – fråga aktivitet, resursutnyttjande | Microsoft Docs
description: Läs om vilka funktioner är tillgängliga för att hantera och övervaka Azure SQL Data Warehouse. Använd Azure-portalen och dynamiska hanteringsvyer (DMV) för att förstå frågeaktivitet och resursanvändningen för ditt informationslager.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b53a3f99f601f540723a9a383e3bf7bd2908de23
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52499574"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Övervaka användning och fråga aktivitet för resurs i Azure SQL Data Warehouse
Azure SQL Data Warehouse är en omfattande övervakning i Azure portal och ytinsikter till din arbetsbelastning i informationslager. Azure-portalen är det rekommendera verktyget när du övervakar ditt informationslager eftersom den innehåller konfigurerbara innehållna perioder, aviseringar, rekommendationer, och anpassningsbara diagram och instrumentpaneler för mått och loggar. På portalen kan du integrera med andra Azure-övervakningstjänster, till exempel Operations Management Suite (OMS) / Log Analytics och Azure Monitor för att tillhandahålla en holistisk övervakning av inte bara din data warehouse utan även din hela Azure analysplattform för en integrerad upplevelse för övervakning. Den här dokumentationen beskriver vilka övervakningsfunktioner som är tillgängliga för att optimera och hantera dina analysplattform med SQL Data Warehouse. 

## <a name="resource-utilization"></a>Resursutnyttjande 
Följande mått är tillgängliga i Azure-portalen för SQL Data Warehouse. De här måtten exponeras via [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics).

> [!NOTE]
> Från och med November 2018 arbetar det tekniska teamet med ett problem med processor- och Data IO-procent till underreport. Detta leder till DWU används och procent samt underreport. 

| Måttnamn                           | Beskrivning     | Sammansättningstyp: |
| --------------------------------------- | ---------------- | --------------------------------------- |
| CPU-procent                          | CPU-användningen över alla noder för datalagret | Maximal      |
| Data IO-procent                      | I/o-användningen över alla noder för datalagret | Maximal   |
| Anslutningarna lyckades                  | Antal lyckade anslutningar till data | Totalt            |
| Misslyckade anslutningar                      | Antal misslyckade anslutningar till datalagret | Totalt            |
| Blockeras av brandvägg                     | Antal inloggningar till datalagret som har blockerats | Totalt            |
| DWU-gräns                              | Servicenivåmål för datalagret | Maximal   |
| DWU-procent                          | Maximalt mellan processor- och Data IO-procent | Maximal   |
| Använda DWU                                | DWU-gräns * DWU-procent | Maximal   |
| Cacheträff i procent | (cachelagra träffar / cachelagra missa) * 100 där cacheträffar är summan av alla columnstore segment träffar i den lokala SSD-cachen och cachemiss är columnstore-segmenten missar i den lokala SSD-cachen som summeras i alla noder | Maximal |
| Använd cache-procent | (cache används / cachelagra kapacitet) * 100 där cache som används är summan av alla byte i den lokala SSD-cachen på alla noder och cache kapacitet är summan av lokal SSD lagringskapacitet cachelagra på alla noder | Maximal |
| Lokala tempdb-procent | Lokala tempdb-användningen över alla beräkningsnoder – värden genereras var femte minut | Maximal |

## <a name="query-activity"></a>Frågeaktivitet
Tjänsten ger en uppsättning med dynamiska hanteringsvyer (DMV) för en programmatisk upplevelse vid övervakning av SQL Data Warehouse via T-SQL. Dessa vyer är användbart när du aktivt felsökning och identifiera flaskhalsar i prestanda med din arbetsbelastning.

Om du vill visa listan med DMV: er som tillhandahåller SQL Data Warehouse, referera till denna [dokumentation](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Mått och diagnostikloggning
Både mått och loggar kan exporteras till Azure Monitor, särskilt de [Log analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) komponent och kan nås via programmering genom [Loggsökning](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata).


> [!NOTE]
> Från och med November 2018 loggar är håller på att distribueras för SQL Data Warehouse

## <a name="next-steps"></a>Nästa steg
Följande instruktionsguider beskrivs vanliga scenarier och användningsfall när övervaka och hantera ditt informationslager:

- [Övervaka din arbetsbelastning i informationslager med dynamiska hanteringsvyer](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

