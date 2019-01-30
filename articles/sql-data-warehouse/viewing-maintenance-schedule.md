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
ms.openlocfilehash: 61537a4cb8ca1e6e98f2fa04959861eb03480fe9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247263"
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
- [Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) skapa och hantera åtgärdsgrupper.
- [Läs mer](https://docs.microsoft.com/azure/service-health/service-health-overview) om Azure Service Health.


