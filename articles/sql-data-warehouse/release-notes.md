---
title: Azure SQL Data Warehouse viktig information | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
ms.openlocfilehash: 2ac60287c9d92ab6230e1dd6777504036e54492d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244682"
---
# <a name="azure-sql-data-warehouse-release-notes-and-documentation-updates"></a>Azure SQL Data Warehouse-viktig information och dokumentationsuppdateringar

Azure SQL Data Warehouse (SQL DW) är en molnbaserad Företagsinformationslager som utnyttjar massivt parallell bearbetning (MPP) för att snabbt köra komplexa frågor över petabyte med data. Använd SQL Data Warehouse som en viktig komponent i en lösning för stordata. Importera stordata till SQL Data Warehouse med enkla PolyBase T-SQL-frågor och utnyttja sedan kraften i MPP för att köra högpresterande analyser. Använd informationslagret en tid för att integrera och analysera och upptäck vilket fantastiskt hjälpmedel det är för att fördjupa insikterna.

Klicka på länkarna nedan för att få mer information om nya funktioner och förbättringar som du kan förvänta dig i den senaste versionen av Azure SQL Data Warehouse. Du kan förvänta dig att få dessa tjänsteuppdateringar under ditt identifierade underhållsschema.

- [SQL Data Warehouse Version 10.0.10106.0 (januari)](./release-notes-10-0-10106-0.md)
- [December 2018](./release-notes-december-2018.md)
- [Oktober 2018](./release-notes-october-2018.md)
- [September 2018](./release-notes-september-2018.md)
- [Augusti 2018](./release-notes-august-2018.md)
- [Juli 2018](./release-notes-july-2018.md)
- [Juni 2018](./release-notes-june-2018.md)
- [Maj 2018](./release-notes-may-2018.md)

## <a name="checking-the-code-version-that-has-been-applied-to-your-data-warehouse"></a>Kontrollerar vilken kodversion som har tillämpats på ditt informationslager

Att fastställa vilka versionen har varit som tillämpats på ditt informationslager. Anslut till ditt informationslager via SSMS och kör du följande syntax för att returnera den aktuella versionen av SQL Data Warehouse.

```sql
SELECT @@VERSION AS 'SQL Data Warehouse';
```

Exempel på utdata: ![SQL Data Warehouse-version](./media/release-notes/dw-version.png)

Använd den version som identifierats för att kontrollera vilken versionen har kopplats till Azure SQL Data Warehouse. 


## <a name="next-steps"></a>Nästa steg
- [Läs mer](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule) om hur du visar ett underhållsschema. 
- [Läs mer](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule) om hur du ändrar ett underhållsschema.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric) om att skapa, visa och hantera aviseringar med hjälp av Azure Monitor.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) om webhook-åtgärder för loggaviseringsregler.
- [Läs mer](https://docs.microsoft.com/azure/service-health/service-health-overview) om Azure Service Health