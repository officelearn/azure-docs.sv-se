---
title: Hanterbarhet och övervakning - frågeaktivitet, resursutnyttjande
description: Lär dig vilka funktioner som är tillgängliga för att hantera och övervaka Azure Synapse Analytics. Använd Azure-portalen och DTV-na (Dynamic Management Views) för att förstå frågeaktivitet och resursutnyttjande av ditt informationslager.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: ce2fe66888893d82debcc412bb16752914d8a190
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011114"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Övervaka resursanvändning och frågeaktivitet i Azure Synapse Analytics

Azure Synapse Analytics ger en omfattande övervakningsupplevelse inom Azure-portalen för att visa insikter om din datalagerarbetsbelastning. Azure-portalen är det rekommenderade verktyget när du övervakar ditt informationslager eftersom det ger konfigurerbara kvarhållningsperioder, aviseringar, rekommendationer och anpassningsbara diagram och instrumentpaneler för mått och loggar. Portalen gör det också möjligt att integrera med andra Azure-övervakningstjänster, till exempel Azure Monitor (loggar) med logganalys för att ge en holistisk övervakningsupplevelse för inte bara ditt informationslager utan även hela din Azure-analysplattform för en integrerad övervakningsupplevelse. Den här dokumentationen beskriver vilka övervakningsfunktioner som är tillgängliga för att optimera och hantera din analysplattform med SQL Analytics.

## <a name="resource-utilization"></a>Resursutnyttjande

Följande mått är tillgängliga i Azure-portalen för SQL Analytics. Dessa mått visas via [Azure Monitor](../../azure-monitor/platform/data-collection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#metrics).

| Måttnamn             | Beskrivning                                                  | Sammansättningstyp |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU-procent          | CPU-användning över alla noder för informationslagret      | Medel, Min, Max    |
| Data IO-procent      | IO-användning för alla noder för informationslagret       | Medel, Min, Max    |
| Minnesprocent       | Minnesanvändning (SQL Server) för alla noder för informationslagret | Medel, Min, Max   |
| Aktiva frågor          | Antal aktiva frågor som körs på systemet             | Summa              |
| Köade frågor          | Antal köade frågor som väntar på att börja köras          | Summa              |
| Lyckade anslutningar  | Antal lyckade anslutningar (inloggningar) mot databasen | Summa, antal       |
| Misslyckade anslutningar      | Antal misslyckade anslutningar (inloggningar) mot databasen | Summa, antal       |
| Blockerad av brandvägg     | Antal inloggningar till informationslagret som blockerades     | Summa, antal       |
| DWU-gräns               | Datalagrets mål på servicenivå                | Medel, Min, Max    |
| DWU-procent          | Maximalt mellan CPU-procent och data-IO-procent        | Medel, Min, Max    |
| DWU används                | DWU-gräns * DWU-procent                                   | Medel, Min, Max    |
| Antal träffar i cacheminne    | (cache träffar / cache miss) * 100 där cache träffar är summan av alla columnstore segment träffar i den lokala SSD cache och cache miss är columnstore segment missar i den lokala SSD cache summeras över alla noder | Medel, Min, Max    |
| Använd procent av cache   | (cache används / cache kapacitet) * 100 där cache används är summan av alla byte i den lokala SSD-cachen över alla noder och cache kapacitet är summan av lagringskapaciteten för den lokala SSD-cachen över alla noder | Medel, Min, Max    |
| Lokal tempdb-procent | Lokal tempdb-användning över alla beräkningsnoder - värden avges var femte minut | Medel, Min, Max    |
| Datalagringsstorlek (GB) | Databasens totala storlek. Detta inkluderar använt, reserverat och oallokerat utrymme. Oallokerat utrymme behålls för databasen för att optimera fråge- och inläsningsprestanda. | Summa |
| Storlek för haveriberedskap (GB) | Den totala storleken på den geo-säkerhetskopiering som tas var 24:e timme | Summa |
| Lagringsstorlek för ögonblicksbilder (GB) | Total storlek på ögonblicksbilder som tagits för att tillhandahålla återställningspunkter för databasen. Detta inkluderar automatiserade och användardefinierade ögonblicksbilder. | Summa |

Saker att tänka på när du visar mått och ställer in aviseringar:

- DWU används representerar endast en hög nivå representation av användning över **SQL-poolen** och är inte tänkt att vara en omfattande indikator på utnyttjande. För att avgöra om du vill skala upp eller ned bör du tänka på alla faktorer som kan påverkas av DWU, till exempel samtidighet, minne, tempdb och adaptiv cachekapacitet. Vi rekommenderar att [du kör din arbetsbelastning med olika DWU-inställningar](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) för att avgöra vad som fungerar bäst för att uppfylla dina affärsmål.
- Misslyckade och lyckade anslutningar rapporteras för ett visst informationslager - inte för den logiska servern
- Minnesprocenten återspeglar användningen även om informationslagret är i inaktivt tillstånd - det återspeglar inte aktiv arbetsbelastningsminnesförbrukning. Använd och spåra det här måttet tillsammans med andra (tempdb, gen2-cache) för att fatta ett holistiskt beslut om om skalning för ytterligare cachekapacitet ökar arbetsbelastningsprestanda för att uppfylla dina krav.

## <a name="query-activity"></a>Frågeaktivitet

För en programmatisk upplevelse när du övervakar SQL Analytics via T-SQL tillhandahåller tjänsten en uppsättning d-spel (Dynamic Management Views). Dessa vyer är användbara när du aktivt felsöker och identifierar flaskhalsar med din arbetsbelastning.

Information om hur du visar listan över DMV:er som SQL Analytics tillhandahåller finns i den här [dokumentationen](sql-data-warehouse-reference-tsql-system-views.md#sql-data-warehouse-dynamic-management-views-dmvs).

## <a name="metrics-and-diagnostics-logging"></a>Mått- och diagnostikloggning

Både mått och loggar kan exporteras till Azure Monitor, särskilt [Azure Monitor-loggkomponenten](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och kan nås programmässigt via [loggfrågor](../../azure-monitor/log-query/get-started-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Loggsvarstiden för SQL Analytics är ca 10-15 minuter. Mer information om vilka faktorer som påverkar svarstiden finns i följande dokumentation.

## <a name="next-steps"></a>Nästa steg

Följande how-to-guider beskriver vanliga scenarier och användningsfall när du övervakar och hanterar ditt informationslager:

- [Övervaka din datalagerarbetsbelastning med DMV:er](sql-data-warehouse-manage-monitor.md)
