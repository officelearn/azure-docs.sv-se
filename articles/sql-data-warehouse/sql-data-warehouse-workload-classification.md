---
title: SQL Data Warehouse klassificering | Microsoft Docs
description: Riktlinjer för att hantera samtidighet, viktiga, och beräkna resurser för frågor i Azure SQL Data Warehouse med klassificering.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 888a64de29178834fc47199a033eb6bc62858e57
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617758"
---
# <a name="sql-data-warehouse-workload-classification-preview"></a>SQL Data Warehouse arbetsbelastning klassificering (förhandsversion)

Den här artikeln beskrivs SQL Data Warehouse arbetsbelastning klassificeringsprocessen för att tilldela en resursklass och prioritet till inkommande begäranden.

> [!Note]
> Klassificering för arbetsbelastningen är tillgänglig för förhandsversionen av SQL Data Warehouse Gen2. Klassificering för hantering av arbetsbelastning och vikten förhandsversion är för versioner med ett frisläppningsdatum av 9 April 2019 eller senare.  Användare bör undvika att använda versioner tidigare än det här datumet för arbetsbelastningen management testning.  För att fastställa om din version är hantering av arbetsbelastning kan du köra väljer @@version när du är ansluten till din SQL Data Warehouse-instans.

## <a name="classification"></a>Klassificering

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Klassificering för hantering av arbetsbelastning kan arbetsbelastningen principer tillämpas på begäranden genom att tilldela [resursklasser](resource-classes-for-workload-management.md#what-are-resource-classes) och [vikten](sql-data-warehouse-workload-importance.md).

Det finns många sätt att klassificera arbetsbelastningar i informationslager, är den enklaste och vanligaste klassificeringen belastning och fråga. Du läser in data med insert-, update- och delete-instruktioner.  Du fråga data med väljer. En datalagerlösning har ofta en princip för arbetsbelastningen för load-aktivitet, till exempel tilldela en högre resursklass med mer resurser. En princip för olika arbetsbelastningar kan gälla frågor, till exempel lägre prioritet jämfört med för att läsa in aktiviteter.

Du kan också subclassify belastning och fråga arbetsbelastningar. Etablering ger mer kontroll över dina arbetsbelastningar. Frågearbetsbelastningar kan exempelvis bestå av kuben uppdateras, instrumentpanelen frågor eller ad hoc-frågor. Du kan klassificera var och en av dessa fråga arbetsbelastningar med olika resursklasser eller prioritetsinställningar. Belastningen kan också dra nytta av etablering. Stora omvandlingar kan tilldelas till större resursklasser. Högre prioritet kan användas för att säkerställa att viktiga försäljningsdata är inläsaren innan weather-data eller en sociala data feed.

Inte alla instruktioner som klassificerats som de inte kräver resurser eller behöver prioritet att påverka körning.  DBCC-kommandon, inte klassificeras BEGIN, COMMIT och ROLLBACK TRANSACTION-instruktioner.

## <a name="classification-process"></a>Klassificeringsprocessen för

Klassificeringen i SQL Data Warehouse uppnås idag genom att tilldela användare till en roll som har en motsvarande resursklass som tilldelats den med hjälp av [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Möjligheten att beskriva begäranden utöver en inloggning till en resursklass begränsas med den här funktionen. En mer omfattande metod för klassificering är nu tillgänglig med den [skapa ARBETSBELASTNING KLASSIFICERARE](/sql/t-sql/statements/create-workload-classifier-transact-sql) syntax.  Med den här syntaxen, kan SQL Data Warehouse användare tilldela prioritet och en resursklass till begäranden.  

> [!NOTE]
> Klassificering utvärderas på varje begäran. Flera begäranden i en enda session kan klassificeras på olika sätt.

## <a name="classification-precedence"></a>Klassificering prioritet

Som en del av klassificering är prioritet på plats för att avgöra vilka resursklass tilldelas. Klassificering baserat på en databasanvändare har företräde framför rollmedlemskap. Om du skapar en klassificerare som mappar användare a databasanvändaren till resursklassen mediumrc. Mappa rollen RoleA (som UserA är medlem) till largerc resursklass. Klassificerare som mappar databasanvändaren till resursklass mediumrc åsidosätter den klassificerare som mappar rollen RoleA till largerc resursklass.

Om en användare är medlem i flera roller med olika resursklasser tilldelas eller matchas i flera klassificerare, ges användaren högsta klass resurstilldelningen.  Det här beteendet är konsekvent med befintliga tilldelningsbeteendet för resurs-klassen.

## <a name="system-classifiers"></a>System-klassificerare

Arbetsbelastningen klassificering har system arbetsbelastning klassificerare. System-klassificerare mappa befintliga rollmedlemskap för resurs-klassen till resursallokeringar klass resurs med normal prioritet. System-klassificerare kan inte släppas. Om du vill visa system-klassificerare som du kan köra den nedanför fråga:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Blanda resource klass tilldelningar med klassificerare

System-klassificerare som skapats i ditt ställe tillhandahåller ett enkelt sätt för att migrera till arbetsbelastningen klassificering. Med hjälp av resource klassmappningar rollen med klassificering prioritet kan leda till felklassificering när du börjar skapa nya klassificerare med prioritet.

Föreställ dig följande:

•An befintliga data warehouse har en databasanvändare DBAUser tilldelats rollen largerc resource klass. Klass resurstilldelning gjordes med sp_addrolemember.
•DNS-datalagret har uppdaterats med hantering av arbetsbelastning.
•Om du vill testa den nya klassificering syntaxen i en databasroll DBARole (som DBAUser är medlem i) har en klassificerare som skapats för dessa mappar dem till mediumrc och hög prioritet.
•When DBAUser loggar in och kör en fråga, frågan kommer att tilldelas till largerc. Eftersom en användare har företräde framför en rollmedlemskap.

För att förenkla felsökning felklassificering, rekommenderar vi att du tar bort resursen klassmappningar rollen när du skapar arbetsbelastning klassificerare.  Koden nedan returnerar befintlig resurs rollmedlemskap för klassen.  Kör [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) för varje medlemsnamn returnerades från motsvarande resursklass.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>Nästa steg

Läs mer om SQL Data Warehouse arbetsbelastning klassificering och vikten [skapa en arbetsbelastning klassificerare](quickstart-create-a-workload-classifier-tsql.md) och [SQL Data Warehouse vikten](sql-data-warehouse-workload-importance.md). Se [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) visa frågor och den prioritet som tilldelas.
