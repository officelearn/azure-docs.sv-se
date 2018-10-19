---
title: Azure-underhållsscheman (förhandsversion) | Microsoft Docs
description: Schemaläggning av underhållsläge ger kunder möjlighet att planera runt de nödvändiga schemalagda underhållshändelser som Azure SQL Data warehouse-tjänsten använder för att lansera nya funktioner, uppgraderingar och uppdateringar.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: db5502b796fc345b2e2bf083f864d80bd59e7293
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427135"
---
# <a name="view-a-maintenance-schedule"></a>Visa ett underhållsschema 

## <a name="portal"></a>Portalen

Som standard har alla nyligen skapade Azure SQL Data Warehouse-instanser en snabbutvärdering på åtta timmar primära och sekundära underhållsperiod tillämpas under distributionen. Du kan ändra windows snart distributionen är klar. Inget Underhåll sker utanför de angivna underhållsfönster utan föregående meddelande.

Om du vill visa underhållsschemat som kopplats till ditt datalager, gör du följande:

1.  Logga in på [Azure Portal](https://portal.azure.com/).
2.  Välj det datalager som du vill visa. 
3.  Valda datalagret öppnas på översiktsbladet. Underhållsschemat som tillämpas på datalagret visas under **underhållsschema (förhandsversion)**.

![Översiktsbladet](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Nästa steg
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) om att skapa, visa och hantera aviseringar med hjälp av Azure Monitor.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) om webhook-åtgärder för loggaviseringsregler.
- [Läs mer](https://docs.microsoft.com/azure/service-health/service-health-overview) om Azure Service Health.


