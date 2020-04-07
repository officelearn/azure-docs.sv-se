---
title: Arbetsbelastningsklassificering
description: Vägledning för hur du använder klassificering för att hantera samtidighet, betydelse och beräkningsresurser för frågor i Azure Synapse Analytics.
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
ms.openlocfilehash: e7aa0c402878c994aabe4e12d811a99e300d7e67
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743650"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Klassificering av Azure Synapse Analytics-arbetsbelastning

I den här artikeln beskrivs arbetsbelastningsklassificeringsprocessen för att tilldela en arbetsbelastningsgrupp och vikten till inkommande begäranden med Synapse SQL-pooler i Azure Synapse.

## <a name="classification"></a>Klassificering

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Klassificering av arbetsbelastningshantering gör att arbetsbelastningsprinciper kan tillämpas på begäranden genom att tilldela [resursklasser](resource-classes-for-workload-management.md#what-are-resource-classes) och [betydelse](sql-data-warehouse-workload-importance.md).

Det finns många sätt att klassificera datalagringsarbetsbelastningar, men den enklaste och vanligaste klassificeringen är inläsning och fråga. Du läser in data med infoga, uppdatera och ta bort satser.  Du frågar data med hjälp av val. En datalagringslösning har ofta en arbetsbelastningsprincip för inläsningsaktivitet, till exempel tilldela en högre resursklass med fler resurser. En annan arbetsbelastningsprincip kan gälla för frågor, till exempel lägre prioritet jämfört med belastningsaktiviteter.

Du kan också underklassificera dina inläsnings- och frågearbetsbelastningar. Underklassificering ger dig mer kontroll över dina arbetsbelastningar. Frågearbetsbelastningar kan till exempel bestå av kubuppdateringar, instrumentpanelsfrågor eller ad hoc-frågor. Du kan klassificera var och en av dessa frågearbetsbelastningar med olika resursklasser eller prioritetsinställningar. Belastning kan också dra nytta av underklassificering. Stora omvandlingar kan tilldelas större resursklasser. Större vikt kan användas för att säkerställa att viktiga försäljningsdata laddas före väderdata eller ett socialt dataflöde.

Alla uttalanden klassificeras inte eftersom de inte kräver resurser eller behöver betydelse för att påverka körningen.  DBCC-kommandon, BEGIN-, COMMIT- och ROLLBACK-TRANSAKTIONssatser klassificeras inte.

## <a name="classification-process"></a>Klassificeringsprocess

Klassificering för Synapse SQL-pool i Azure Synapse uppnås idag genom att tilldela användare till en roll som har en motsvarande resursklass tilldelad med hjälp av [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Möjligheten att karakterisera begäranden utöver en inloggning till en resursklass är begränsad med den här funktionen. En rikare metod för klassificering är nu tillgänglig med syntaxen [SKAPA ARBETSBELASTNINGSKLASSIFICER.](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Med den här syntaxen kan Synapse SQL-poolanvändare tilldela prioritet och `workload_group` hur mycket systemresurser som tilldelas en begäran via parametern.

> [!NOTE]
> Klassificeringen utvärderas per begäran. Flera begäranden i en enda session kan klassificeras på olika sätt.

## <a name="classification-weighting"></a>Klassificering viktning

Som en del av klassificeringsprocessen finns viktning på plats för att avgöra vilken arbetsbelastningsgrupp som har tilldelats.  Viktningen går som följer:

|Parameter för klassificerare |Vikt   |
|---------------------|---------|
|MEDLEMSNAMN:ANVÄNDARE      |64       |
|MEDLEMSNAMN:ROLL      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

Parametern `membername` är obligatorisk.  Men om det angivna medlemsnamnet är en databasanvändare i stället för en databasroll är viktningen för användaren högre och klassificeraren väljs därför.

Om en användare är medlem i flera roller med olika resursklasser som tilldelats eller matchas i flera klassificerare, får användaren den högsta resursklasstilldelningen.  Det här beteendet är förenligt med befintliga resursklasstilldelningsbeteende.

## <a name="system-classifiers"></a>Systemklassificerare

Arbetsbelastningsklassificering har systemarbetsbelastningsklassificerare. Systemklassificerarna mappar befintliga resursklassrollmedlemskap till resursklassresursallollollollollollollollos med normal betydelse. Systemklassificerare kan inte tas bort. Om du vill visa systemklassificerare kan du köra nedanstående fråga:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Blanda resursklasstilldelningar med klassificerare

Systemklassificerare som skapats för din räkning ger en enkel sökväg att migrera till arbetsbelastningsklassificering. Med hjälp av resursklassrollmappningar med klassificeringsprioritet kan det leda till felklassificering när du börjar skapa nya klassificerare med betydelse.

Föreställ dig följande scenario:

- Ett befintligt informationslager har en databasanvändare DBAUser som tilldelats rollen större resursklass. Resursklasstilldelningen gjordes med sp_addrolemember.
- Informationslagret uppdateras nu med arbetsbelastningshantering.
- För att testa den nya klassificeringsyntaxen har databasrollen DBARole (som DBAUser är medlem i), en klassificerare som skapats för dem som mappar dem till mediumrc och hög prioritet.
- När DBAUser loggar in och kör en fråga tilldelas frågan till largerc. Eftersom en användare har företräde framför ett rollmedlemskap.

För att förenkla felsökningen av felklassificering rekommenderar vi att du tar bort rollmappningar för resursklass när du skapar arbetsbelastningsklassificerare.  Koden nedan returnerar befintliga medlemskap i resursklassrollen.  Kör [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) för varje medlemsnamn som returneras från motsvarande resursklass.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar en klassificerare finns i [CREATE WORKLOAD CLASSIFIER (Transact-SQL).](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  
- Se snabbstarten om hur du skapar en arbetsbelastningsklassificerare [Skapa en arbetsbelastningsklassificerare](quickstart-create-a-workload-classifier-tsql.md).
- Se hur du gör-artiklar för att [konfigurera arbetsbelastningsbetydning](sql-data-warehouse-how-to-configure-workload-importance.md) och hur du [hanterar och övervakar arbetsbelastningshantering](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Se [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om du vill visa frågor och den tilldelade betydelsen.
