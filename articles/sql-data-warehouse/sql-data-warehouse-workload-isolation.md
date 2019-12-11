---
title: Arbetsbelastningsisolering
description: Vägledning för att ställa in arbets belastnings isolering med arbets belastnings grupper i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 11/27/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 82270c126d8a0894cd3a388dcab62017ed63c2cd
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974656"
---
# <a name="sql-data-warehouse-workload-group-isolation-preview"></a>SQL Data Warehouse isolering av arbets belastnings grupper (för hands version)

Den här artikeln förklarar hur arbets belastnings grupper kan användas för att konfigurera arbets belastnings isolering, innehåller resurser och tillämpa körnings regler för frågekörningen.

## <a name="workload-groups"></a>Arbets belastnings grupper

Arbets belastnings grupper är behållare för en uppsättning begär Anden och är grunden för hur arbets belastnings hantering, inklusive arbets belastnings isolering, är konfigurerat på ett system.  Arbets belastnings grupper skapas med hjälp av syntaxen [skapa arbets belastnings grupp](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  En enkel konfiguration av arbets belastnings hantering kan hantera data inläsningar och användar frågor.  Till exempel kan en arbets belastnings grupp med namnet `wgDataLoads` definiera arbets belastnings aspekter för data som läses in i systemet. Dessutom definierar en arbets belastnings grupp med namnet `wgUserQueries` arbets belastnings aspekter för användare som kör frågor för att läsa data från systemet.

I följande avsnitt ser du hur arbets belastnings grupper ger möjlighet att definiera isolering, inne slutning, begär resurs definition och följa körnings regler.

## <a name="workload-isolation"></a>Arbetsbelastningsisolering

Arbets belastnings isolering innebär att resurser är reserverade, exklusivt, för en arbets belastnings grupp.  Arbets belastnings isolering uppnås genom att konfigurera parametern MIN_PERCENTAGE_RESOURCE till större än noll i syntaxen [skapa arbets belastnings grupp](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  För kontinuerliga körnings arbets belastningar som behöver följa tätt service avtal säkerställer isolering att resurser alltid är tillgängliga för arbets belastnings gruppen. 

Konfiguration av arbets belastnings isolering definierar implicit en garanterad nivå av samtidighet.  Med en MIN_PERCENTAGE_RESOURCE inställd på 30% och REQUEST_MIN_RESOURCE_GRANT_PERCENT inställd på 2% garanteras en 15-samtidighets nivå för arbets belastnings gruppen.  Överväg nedanstående metod för att fastställa garanterad samtidighet:

[Garanterad samtidighet] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Det finns vissa lägsta möjliga värde för min_percentage_resource på service nivå.  Mer information finns i de [effektiva värdena](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values) för mer information.

Vid frånvaro av arbets belastnings isolering används förfrågningar i den [delade poolen](#shared-pool-resources) med resurser.  Åtkomst till resurser i den delade poolen är inte garanterad och tilldelas [prioritet](sql-data-warehouse-workload-importance.md) .

Konfigurering av arbets belastnings isolering bör göras med försiktighet när resurserna allokeras till arbets belastnings gruppen även om det inte finns några aktiva begär anden i arbets belastnings gruppen.  Överkonfigurerad isolering kan leda till försämrad övergripande system användning.

Användare bör undvika en lösning för hantering av arbets belastning som konfigurerar 100% arbets belastnings isolering: 100% isolering uppnås när summan av min_percentage_resource som kon figurer ATS för alla arbets belastnings grupper är lika med 100%.  Den här typen av konfiguration är mer restriktiv och stel och lämnar lite utrymme för resurs begär Anden som oavsiktligt felklassificeras.  Det finns en regel för att tillåta att en begäran körs från arbets belastnings grupper som inte har kon figurer ATS för isolering.  Resurserna som allokeras till den här begäran visas som noll i system-DMV: er och låna en smallrc resurs tilldelning från system reserverade resurser.

> [!NOTE] 
> För att säkerställa optimal resursutnyttjande bör du överväga en lösning för hantering av arbets belastning som utnyttjar viss isolering för att säkerställa att service avtal är uppfyllda och blandat med delade resurser som nås baserat på [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md).

## <a name="workload-containment"></a>Arbets belastnings inne slutning

Arbets belastnings inne slutning syftar till att begränsa den mängd resurser som en arbets belastnings grupp kan använda.  Arbets belastnings inne slutning uppnås genom att konfigurera parametern CAP_PERCENTAGE_RESOURCE till mindre än 100 i syntaxen [skapa arbets belastnings grupp](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Tänk dig ett scenario där användarna behöver Läs behörighet till systemet så att de kan köra en konsekvens analys via Ad hoc-frågor.  Dessa typer av begär Anden kan ha en negativ inverkan på andra arbets belastningar som körs på systemet.  Genom att konfigurera inne slutning ser du till att mängden resurser är begränsad.

Konfigurera arbets belastnings inne slutningen definierar en maximal nivå för samtidighet.  Med en CAP_PERCENTAGE_RESOURCE inställd på 60% och en REQUEST_MIN_RESOURCE_GRANT_PERCENT inställd på 1%, tillåts upp till en 60-samtidighets nivå för arbets belastnings gruppen.  Överväg den metod som beskrivs nedan för att fastställa högsta samtidighet:

[Max samtidighet] = [`CAP_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Den effektiva CAP_PERCENTAGE_RESOURCE av en arbets belastnings grupp når inte 100% när arbets belastnings grupper med MIN_PERCENTAGE_RESOURCE på en högre nivå än noll skapas.  Se [sys. dm_workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) för effektiva körnings värden.

## <a name="resources-per-request-definition"></a>Resurs per begär ande definition

Arbets belastnings grupper tillhandahåller en mekanism för att definiera den minsta och högsta mängden resurser som tilldelas per begäran med REQUEST_MIN_RESOURCE_GRANT_PERCENT och REQUEST_MAX_RESOURCE_GRANT_PERCENT parametrar i [create arbets belastnings gruppens](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) syntax.  Resurser i det här fallet är CPU och minne.  Genom att konfigurera dessa värden bestäms hur mycket resurser och vilken nivå av samtidighet som kan uppnås i systemet.

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT är en valfri parameter som är standardvärdet för samma värde som anges för REQUEST_MIN_RESOURCE_GRANT_PERCENT.

På samma sätt som du väljer en resurs klass, konfigurera REQUEST_MIN_RESOURCE_GRANT_PERCENT anger värdet för de resurser som används av en begäran.  Mängden resurser som anges av Set-värdet garanteras för allokering till begäran innan den börjar köras.  För kunder som migrerar från resurs klasser till arbets belastnings grupper kan du [överväga att följa artikeln artikeln](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md) att mappa från resurs klasser till arbets belastnings grupper som en start punkt.

Om du konfigurerar REQUEST_MAX_RESOURCE_GRANT_PERCENT till ett värde som är större än REQUEST_MIN_RESOURCE_GRANT_PERCENT kan systemet allokera fler resurser per begäran.  Vid schemaläggning av en begäran fastställer systemet faktisk resursallokering till begäran, vilket är mellan REQUEST_MIN_RESOURCE_GRANT_PERCENT och REQUEST_MAX_RESOURCE_GRANT_PERCENT, baserat på resurs tillgänglighet i delad pool och aktuell belastning på säker.  Resurserna måste finnas i den [delade poolen](#shared-pool-resources) med resurser när frågan har schemalagts.  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT och REQUEST_MAX_RESOURCE_GRANT_PERCENT har effektiva värden som är beroende av de effektiva MIN_PERCENTAGE_RESOURCE och CAP_PERCENTAGE_RESOURCE värden.  Se [sys. dm_workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) för effektiva körnings värden.

## <a name="execution-rules"></a>Körnings regler

På ad hoc-rapporterings system kan kunder oavsiktligt köra överliggande frågor som kraftigt påverkar produktiviteten hos andra.  System administratörer tvingas ägna tid åt att lägga ner frågor om att frigöra system resurser.  Arbets belastnings grupper ger möjlighet att konfigurera en tids gräns regel för frågekörningen för att avbryta frågor som har överskridit det angivna värdet.  Regeln konfigureras genom att ange parametern `QUERY_EXECUTION_TIMEOUT_SEC` i syntaxen [create arbets belastnings grupp](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .

## <a name="shared-pool-resources"></a>Delade pool resurser

Resurser för delad pool är de resurser som inte är konfigurerade för isolering.  Arbets belastnings grupper med MIN_PERCENTAGE_RESOURCE inställd på noll utnyttjar resurser i den delade poolen för att köra begär Anden.  Arbets belastnings grupper med en CAP_PERCENTAGE_RESOURCE större än MIN_PERCENTAGE_RESOURCE också använt delade resurser.  Mängden resurser som är tillgängliga i den delade poolen beräknas på följande sätt.

[Delad pool] = 100-[summan av `MIN_PERCENTAGE_RESOURCE` över alla arbets belastnings grupper]

Åtkomst till resurser i den delade poolen allokeras på grund av [prioritet](sql-data-warehouse-workload-importance.md) .  Begär Anden med samma prioritets nivå kommer att få åtkomst till Shared pool-resurser först in/först ut.

## <a name="next-steps"></a>Nästa steg

- [Snabb start: Konfigurera arbets belastnings isolering](quickstart-configure-workload-isolation-tsql.md)
- [SKAPA ARBETS BELASTNINGS GRUPP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [Konvertera resurs klasser till arbets belastnings grupper](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md).
