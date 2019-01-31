---
title: Azure SQL Data Warehouse viktig information | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/11/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 7a131429d8f70255e47efaac0c4b5fa99a6df7d6
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460819"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Viktig information om Azure SQL Data Warehouse

Azure SQL Data Warehouse (SQL DW) är en molnbaserad Företagsinformationslager som utnyttjar massivt parallell bearbetning (MPP) för att snabbt köra komplexa frågor över petabyte med data. Använd SQL Data Warehouse som en viktig komponent i en lösning för stordata. Importera stordata till SQL Data Warehouse med enkla PolyBase T-SQL-frågor och utnyttja sedan kraften i MPP för att köra högpresterande analyser. Använd informationslagret en tid för att integrera och analysera och upptäck vilket fantastiskt hjälpmedel det är för att fördjupa insikterna.

Klicka på länkarna nedan för att få mer information om nya funktioner och förbättringar som du kan förvänta dig i den senaste versionen av Azure SQL Data Warehouse. Du kan förvänta dig att få dessa tjänsteuppdateringar under ditt identifierade underhållsschema.

- [SQL DW Version 10.0.10106.0](./release-notes-10-0-10106-0.md)
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
