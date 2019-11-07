---
title: Omvandla resurs klass till en arbets belastnings grupp
description: Lär dig hur du skapar en arbets belastnings grupp som liknar en resurs klass i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5708dccce5f7cdcc33fe6bfca980126cd8b5ee7f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685703"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Konvertera resurs klasser till arbets belastnings grupper
Arbets belastnings grupper tillhandahåller en mekanism för att isolera och innehålla system resurser.  Dessutom kan du med arbets belastnings grupper ange körnings regler för de begär Anden som körs i dem.  En regel för körning av tids gräns tillåter att omsluts frågor avbryts utan att användaren tillfrågas.  Den här artikeln förklarar hur du tar en befintlig resurs klass och skapar en arbets belastnings grupp med en liknande konfiguration.  Dessutom läggs en valfri tids gräns regel för frågor till.

## <a name="understanding-the-existing-resource-class-configuration"></a>Förstå den befintliga resurs klass konfigurationen
Arbets belastnings grupper kräver en parameter med namnet `REQUEST_MIN_RESOURCE_GRANT_PERCENT` som anger procent andelen totala system resurser som allokeras per begäran.  Resursallokering görs för [resurs klasser](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes) genom att allokera samtidiga platser.  Använd sys. DM _workload_management_workload_groups_stats <link tbd> DMV för att fastställa värdet som ska anges för `REQUEST_MIN_RESOURCE_GRANT_PERCENT`.  Frågan nedan returnerar exempelvis ett värde som kan användas för parametern `REQUEST_MIN_RESOURCE_GRANT_PERCENT` för att skapa en arbets belastnings grupp som liknar staticrc40.   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Arbets belastnings grupper arbetar baserat på procent av övergripande system resurser.  

Eftersom arbets belastnings grupper fungerar baserat på procent andelen av övergripande system resurser, när du skalar upp och ned, ändras procent andelen resurser som har allokerats till statiska resurs klasser i förhållande till de totala system resurserna.  Staticrc40 vid DW1000c allokerar till exempel 9,6% av de totala system resurserna.  Vid DW2000c tilldelas 19,2%.  Den här modellen är liknande om du vill skala upp för samtidighet jämfört med att allokera fler resurser per begäran.   

## <a name="create-workload-group"></a>Skapa arbets belastnings grupp
Med den kända `REQUEST_MIN_RESOURCE_GRANT_PERCENT`kan du använda syntaxen CREATE arbets BELASTNINGs grupp <link> för att skapa arbets belastnings gruppen.  Du kan också ange en `MIN_PERCENTAGE_RESOURCE` som är större än noll för att isolera resurser för arbets belastnings gruppen.  Du kan också ange `CAP_PERCENTAGE_RESOURCE` mindre än 100 för att begränsa den mängd resurser som arbets belastnings gruppen kan använda.  

I exemplet nedan anges `MIN_PERCENTAGE_RESOURCE` för att tilldela 9,6% av system resurserna till `wgDataLoads` och garanterar att en fråga kan köra alla tider.  Dessutom är `CAP_PERCENTAGE_RESOURCE` inställd på 38,4% och begränsar den här arbets belastnings gruppen till fyra samtidiga begär Anden.  Genom att ange parametern `QUERY_EXECUTION_TIMEOUT_SEC` till 3600 avbryts alla frågor som körs i mer än en timme automatiskt.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Skapa klassificeraren
Tidigare gjordes mappningen av frågor till resurs klasser med [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Om du vill uppnå samma funktioner och mappa begär anden till arbets belastnings grupper använder du KLASSIFICERINGs-syntaxen [skapa arbets belastning](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) .  Med sp_addrolemember får du bara mappa resurser till en begäran baserat på en inloggning.  En klassificerare innehåller ytterligare alternativ förutom inloggning, till exempel:
    - etikett
    - session
    - tid i exemplet nedan tilldelar frågor från den `AdfLogin` inloggning som också har [alternativ etiketten](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label) inställd på `factloads` till arbets belastnings gruppen `wgDataLoads` som du skapade ovan.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>Testa med en exempel fråga
Nedan visas en exempel fråga och en DMV-fråga för att se till att arbets belastnings gruppen och klassificeraren är korrekt konfigurerade.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 * 
  FROM nation 
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command 
  FROM sys.dm_pdw_exec_requests 
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Nästa steg

Arbets belastnings isolering – länka TBD instruktioner – Skapa en arbets belastnings grupp – länk TBD