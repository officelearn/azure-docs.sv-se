---
title: Arbetsbelastningsisolering
description: Vägledning för att ange arbetsbelastningsisolering med arbetsbelastningsgrupper i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 5d81dc1f4da6e952061496fa348d0f8e87b00b81
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742970"
---
# <a name="azure-synapse-analytics-workload-group-isolation-preview"></a>Isolering av Azure Synapse Analytics-arbetsbelastningsgrupp (förhandsversion)

I den här artikeln beskrivs hur arbetsbelastningsgrupper kan användas för att konfigurera arbetsbelastningsisolering, innehålla resurser och tillämpa körningsregler för körning av frågor.

## <a name="workload-groups"></a>Arbetsbelastningsgrupper

Arbetsbelastningsgrupper är behållare för en uppsättning begäranden och är grunden för hur arbetsbelastningshantering, inklusive arbetsbelastningsisolering, konfigureras på ett system.  Arbetsbelastningsgrupper skapas med syntaxen [SKAPA ARBETSBELASTNINGSGRUPP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  En enkel konfiguration för arbetsbelastningshantering kan hantera databelastningar och användarfrågor.  En arbetsbelastningsgrupp `wgDataLoads` med namnet definierar till exempel arbetsbelastningsaspekter för data som läses in i systemet. Dessutom definierar en `wgUserQueries` arbetsbelastningsgrupp med namnet arbetsbelastningsaspekter för användare som kör frågor för att läsa data från systemet.

I följande avsnitt markeras hur arbetsbelastningsgrupper ger möjlighet att definiera isolering, inneslutning, begäran om resursdefinition och följa körningsregler.

## <a name="workload-isolation"></a>Arbetsbelastningsisolering

Arbetsbelastningsisolering innebär att resurser reserveras, exklusivt, för en arbetsbelastningsgrupp.  Arbetsbelastningsisolering uppnås genom att konfigurera parametern MIN_PERCENTAGE_RESOURCE till större än noll i syntaxen [SKAPA ARBETSBELASTNINGSGRUPP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  För kontinuerlig körning arbetsbelastningar som behöver följa snäva SLA, isolering säkerställer resurser är alltid tillgängliga för arbetsbelastningen gruppen.

Konfigurera arbetsbelastningsisolering definierar implicit en garanterad nivå av samtidighet. En arbetsbelastningsgrupp med `MIN_PERCENTAGE_RESOURCE` en uppsättning till `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 30 % och inställd på 2 % garanteras till exempel 15 samtidighet.  Nivån på samtidigheten garanteras eftersom 15-2% platser av resurser är reserverade inom arbetsbelastningsgruppen hela tiden (oavsett hur `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` är konfigurerad).  Om `REQUEST_MAX_RESOURCE_GRANT_PERCENT` är `REQUEST_MIN_RESOURCE_GRANT_PERCENT` större än och `CAP_PERCENTAGE_RESOURCE` är större än `MIN_PERCENTAGE_RESOURCE` ytterligare resurser läggs till per begäran.  Om `REQUEST_MAX_RESOURCE_GRANT_PERCENT` `REQUEST_MIN_RESOURCE_GRANT_PERCENT` och är `CAP_PERCENTAGE_RESOURCE` lika `MIN_PERCENTAGE_RESOURCE`och är större än är ytterligare samtidighet möjlig.  Tänk på nedanstående metod för att fastställa garanterad samtidighet:

[Garanterad samtidighet] =`MIN_PERCENTAGE_RESOURCE`[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE]
> Det finns specifika lägsta möjliga värden på servicenivå för min_percentage_resource.  Mer information finns i [Effektiva värden](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values) för mer information.

I avsaknad av arbetsbelastningsisolering fungerar begäranden i den [delade resurspoolen.](#shared-pool-resources)  Tillgång till resurser i den delade poolen garanteras inte och tilldelas på [en viktig](sql-data-warehouse-workload-importance.md) grund.

Konfigurera arbetsbelastningsisolering bör göras med försiktighet eftersom resurserna allokeras till arbetsbelastningsgruppen även om det inte finns några aktiva begäranden i arbetsbelastningsgruppen. Överkonfigurering isolering kan leda till minskad total systemanvändning.

Användare bör undvika en lösning för arbetsbelastningshantering som konfigurerar 100 % arbetsbelastningsisolering: 100 % isolering uppnås när summan av min_percentage_resource som konfigurerats för alla arbetsbelastningsgrupper är lika med 100 %.  Den här typen av konfiguration är alltför restriktiv och stel, vilket ger lite utrymme för resursbegäranden som av misstag är felklassificerade. Det finns en bestämmelse som tillåter en begäran att köra från arbetsbelastningsgrupper som inte har konfigurerats för isolering. De resurser som allokeras till den här begäran visas som en nolla i systemens DMV:er och lånar en liten resursbidragsnivå från systemreserverade resurser.

> [!NOTE]
> För att säkerställa optimal resursutnyttjande bör du överväga en lösning för arbetsbelastningshantering som utnyttjar viss isolering för att säkerställa att SLA:er uppfylls och blandas med delade resurser som används baserat på [arbetsbelastningsbetydning.](sql-data-warehouse-workload-importance.md)

## <a name="workload-containment"></a>Arbetsbelastningsinneslutning

Arbetsbelastningsinneslutning avser att begränsa mängden resurser som en arbetsbelastningsgrupp kan använda.  Arbetsbelastningsinneslutning uppnås genom att konfigurera parametern CAP_PERCENTAGE_RESOURCE till mindre än 100 i syntaxen [SKAPA ARBETSBELASTNINGSGRUPP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Tänk på det scenario där användarna behöver läsåtkomst till systemet så att de kan köra en vad händer om-analys via ad hoc-frågor.  Dessa typer av begäranden kan ha en negativ inverkan på andra arbetsbelastningar som körs på systemet.  Konfigurera inneslutning säkerställer att mängden resurser är begränsad.

Konfigurera arbetsbelastning inneslutning implicit definierar en maximal nivå av samtidighet.  Med en CAP_PERCENTAGE_RESOURCE inställd på 60 % och en REQUEST_MIN_RESOURCE_GRANT_PERCENT inställd på 1 % tillåts upp till en 60-samtidighetsnivå för arbetsbelastningsgruppen.  Tänk på den metod som anges nedan för att fastställa den maximala samtidigheten:

[Max Concurrency]`CAP_PERCENTAGE_RESOURCE`= [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`] / [ ]

> [!NOTE]
> Den effektiva CAP_PERCENTAGE_RESOURCE för en arbetsbelastningsgrupp når inte 100 % när arbetsbelastningsgrupper med MIN_PERCENTAGE_RESOURCE på en nivå som är större än noll skapas.  Se [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) för effektiva körningsvärden.

## <a name="resources-per-request-definition"></a>Resurser per definition av begäran

Arbetsbelastningsgrupper tillhandahåller en mekanism för att definiera den minsta och högsta mängden resurser som allokeras per begäran med REQUEST_MIN_RESOURCE_GRANT_PERCENT och REQUEST_MAX_RESOURCE_GRANT_PERCENT parametrar i syntaxen [skapa arbetsbelastningsgrupp.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Resurser i det här fallet är CPU och minne.  Konfigurera dessa värden avgör hur mycket resurser och vilken nivå av samtidighet kan uppnås på systemet.

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT är en valfri parameter som standard till samma värde som anges för REQUEST_MIN_RESOURCE_GRANT_PERCENT.

Som att välja en resursklass anger konfigurera REQUEST_MIN_RESOURCE_GRANT_PERCENT värdet för de resurser som används av en begäran.  Mängden resurser som anges av det angivna värdet garanteras för allokering till begäran innan den påbörjar körningen.  För kunder som migrerar från resursklasser till arbetsbelastningsgrupper bör du överväga att följa hur [du](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) mappar från resursklasser till arbetsbelastningsgrupper som utgångspunkt.

Genom att konfigurera REQUEST_MAX_RESOURCE_GRANT_PERCENT till ett värde som är större än REQUEST_MIN_RESOURCE_GRANT_PERCENT kan systemet allokera mer resurser per begäran.  När du schemalägger en begäran bestämmer systemet den faktiska resursallokeringen till begäran, som är mellan REQUEST_MIN_RESOURCE_GRANT_PERCENT och REQUEST_MAX_RESOURCE_GRANT_PERCENT, baserat på resurstillgänglighet i delad pool och aktuell belastning på systemet.  Resurserna måste finnas i den [delade resurspoolen](#shared-pool-resources) när frågan schemaläggs.  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT och REQUEST_MAX_RESOURCE_GRANT_PERCENT har effektiva värden som är beroende av de effektiva MIN_PERCENTAGE_RESOURCE och CAP_PERCENTAGE_RESOURCE värdena.  Se [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) för effektiva körningsvärden.

## <a name="execution-rules"></a>Regler för utförande

På ad hoc-rapporteringssystem kan kunder av misstag köra skenande frågor som allvarligt påverkar andras produktivitet.  Systemadministratörer tvingas spendera tid på att döda skenande frågor för att frigöra systemresurser.  Arbetsbelastningsgrupper erbjuder möjligheten att konfigurera en tidsgränsen för frågekörning för att avbryta frågor som har överskridit det angivna värdet.  Regeln konfigureras genom att `QUERY_EXECUTION_TIMEOUT_SEC` ange parametern i syntaxen [SKAPA ARBETSBELASTNINGSGRUPP.](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="shared-pool-resources"></a>Delade poolresurser

Delade poolresurser är de resurser som inte har konfigurerats för isolering.  Arbetsbelastningsgrupper med en MIN_PERCENTAGE_RESOURCE inställd på noll hävstångsresurser i den delade poolen för att köra begäranden.  Arbetsbelastningsgrupper med en CAP_PERCENTAGE_RESOURCE större än MIN_PERCENTAGE_RESOURCE också använt delade resurser.  Mängden resurser som är tillgängliga i den delade poolen beräknas på följande sätt.

[Delad pool] = 100 `MIN_PERCENTAGE_RESOURCE` - [summan av alla arbetsbelastningsgrupper]

Tillgång till resurser i den delade poolen fördelas på [en viktig](sql-data-warehouse-workload-importance.md) grund.  Begäranden med samma prioritetsnivå kommer att få tillgång till delade poolresurser på grundval av första in/först ut.

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: konfigurera arbetsbelastningsisolering](quickstart-configure-workload-isolation-tsql.md)
- [SKAPA ARBETSBELASTNINGSGRUPP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Konvertera resursklasser till arbetsbelastningsgrupper](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
- [Övervakning av arbetsbelastningshanteringsportalen](sql-data-warehouse-workload-management-portal-monitor.md).  
