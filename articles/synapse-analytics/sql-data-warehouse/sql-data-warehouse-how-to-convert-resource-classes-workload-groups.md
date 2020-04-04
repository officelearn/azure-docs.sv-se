---
title: Konvertera resursklass till en arbetsbelastningsgrupp
description: Lär dig hur du skapar en arbetsbelastningsgrupp som liknar en resursklass i Azure SQL Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a249dfc4f04fbd7b6b73a0e9f37d53106bf82efd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633345"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Konvertera resursklasser till arbetsbelastningsgrupper

Arbetsbelastningsgrupper tillhandahåller en mekanism för att isolera och innehålla systemresurser.  Dessutom kan arbetsbelastningsgrupper du ange körningsregler för begäranden som körs i dem.  Med en regel för körning av frågetidsutgång kan skena frågor som ska avbrytas utan att användaren behöver göra något.  I den här artikeln beskrivs hur du tar en befintlig resursklass och skapar en arbetsbelastningsgrupp med en liknande konfiguration.  Dessutom läggs en valfri frågetidsutgångsregel till.

## <a name="understanding-the-existing-resource-class-configuration"></a>Förstå den befintliga resursklasskonfigurationen

Arbetsbelastningsgrupper kräver `REQUEST_MIN_RESOURCE_GRANT_PERCENT` en parameter som anropas som anger procentandelen av de totala systemresurserna som allokerats per begäran.  Resursallokering görs för [resursklasser](resource-classes-for-workload-management.md#what-are-resource-classes) genom att allokera samtidighetsplatser.  Om du vill bestämma `REQUEST_MIN_RESOURCE_GRANT_PERCENT`värdet för att använda <link tbd> använder du sys.dm_workload_management_workload_groups_stats DMV.  Frågan nedan returnerar till exempel ett värde som `REQUEST_MIN_RESOURCE_GRANT_PERCENT` kan användas för parametern för att skapa en arbetsbelastningsgrupp som liknar staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Arbetsbelastningsgrupper fungerar baserat på procentandel av de totala systemresurserna.  

Eftersom arbetsbelastningsgrupper fungerar baserat på procentandel av de totala systemresurserna, när du skalar upp och ned, ändras procentandelen resurser som allokerats till statiska resursklasser i förhållande till de totala systemresurserna.  Statiskrc40 vid DW1000c tilldelar till exempel 9,6 % av de totala systemresurserna.  Vid DW2000c fördelas 19,2 %.  Den här modellen är liknande om du vill skala upp för samtidighet kontra att fördela mer resurser per begäran.

## <a name="create-workload-group"></a>Skapa arbetsbelastningsgrupp

Med den `REQUEST_MIN_RESOURCE_GRANT_PERCENT`kända kan du använda <link> syntaxen skapa arbetsbelastningsgrupp för att skapa arbetsbelastningsgruppen.  Du kan också `MIN_PERCENTAGE_RESOURCE` ange en som är större än noll för att isolera resurser för arbetsbelastningsgruppen.  Du kan också ange `CAP_PERCENTAGE_RESOURCE` mindre än 100 för att begränsa mängden resurser som arbetsbelastningsgruppen kan använda.  

I exemplet nedan `MIN_PERCENTAGE_RESOURCE` anges att 9,6 % av `wgDataLoads` systemresurserna ska avsättas och garanterar att en fråga kan köras hela tiden.  Dessutom `CAP_PERCENTAGE_RESOURCE` är inställd på 38,4% och begränsar den här arbetsbelastningsgruppen till fyra samtidiga begäranden.  Genom att `QUERY_EXECUTION_TIMEOUT_SEC` ställa in parametern på 3600 avbryts alla frågor som körs i mer än 1 timme automatiskt.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Skapa klassificeraren

Tidigare gjordes mappningen av frågor till resursklasser med [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Om du vill uppnå samma funktioner och mappa begäranden till arbetsbelastningsgrupper använder du syntaxen [SKAPA ARBETSBELASTNINGSKLASSIFICER.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)  Genom att använda sp_addrolemember kunde du bara mappa resurser till en begäran baserat på en inloggning.  En klassificerare ger ytterligare alternativ förutom inloggning, till exempel:
    - etikett
    - session
    - tid Nedanstående exempel tilldelar `AdfLogin` frågor från inloggningen som `factloads` också har `wgDataLoads` [ALT-etiketten](sql-data-warehouse-develop-label.md) inställd på den arbetsbelastningsgrupp som skapats ovan.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Testa med en exempelfråga

Nedan finns en exempelfråga och en DMV-fråga för att säkerställa att arbetsbelastningsgruppen och klassificeraren är korrekt konfigurerade.

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

- [Isolering av arbetsbelastning](sql-data-warehouse-workload-isolation.md)
- [Skapa en arbetsbelastningsgrupp-länk](quickstart-configure-workload-isolation-tsql.md)
