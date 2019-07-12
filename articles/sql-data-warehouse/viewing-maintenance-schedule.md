---
title: Azure-underhållsscheman (förhandsversion) | Microsoft Docs
description: Schemaläggning av underhållsläge ger kunder möjlighet att planera runt de nödvändiga schemalagda underhållshändelser som Azure SQL Data warehouse-tjänsten använder för att lansera nya funktioner, uppgraderingar och uppdateringar.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: f0b9b59ec09445a170d1f93181c2b432eafb60bf
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839986"
---
# <a name="view-a-maintenance-schedule"></a>Visa ett underhållsschema 

## <a name="portal"></a>Portalen

Som standard har alla nyligen skapade Azure SQL Data Warehouse-instanser en snabbutvärdering på åtta timmar primära och sekundära underhållsperiod tillämpas under distributionen. Du kan ändra windows snart distributionen är klar. Inget Underhåll sker utanför de angivna underhållsfönster utan föregående meddelande.

Om du vill visa underhållsschemat som kopplats till ditt datalager, gör du följande:

1.  Logga in på [Azure Portal](https://portal.azure.com/).
2.  Välj det datalager som du vill visa. 
3.  Valda datalagret öppnas på översiktsbladet. Underhållsschemat som tillämpas på datalagret visas under **underhållsschema**.

![Översiktsbladet](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Nästa steg
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) om att skapa, visa och hantera aviseringar med hjälp av Azure Monitor.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) om webhook-åtgärder för loggaviseringsregler.
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) skapa och hantera åtgärdsgrupper.
- [Läs mer](https://docs.microsoft.com/azure/service-health/service-health-overview) om Azure Service Health.


