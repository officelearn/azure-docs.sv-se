---
title: Azure underhållsscheman (förhandsversion) | Microsoft Docs
description: Schemalägga Underhåll kan kunderna planera runt händelserna behövs schemalagt underhåll Azure SQL Data warehouse-tjänsten använder för att lansera nya funktioner, uppgraderingar och uppdateringar.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 6fbf914c8035344d33e8d2739fb9d92d5757c3d1
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228429"
---
# <a name="viewing-a-maintenance-schedule"></a>Visa ett underhållsschema 

## <a name="portal"></a>Portalen

Som standard alla nyligen skapade Azure SQL Data Warehouse instanser har en 8 timmar systemdefinierade primära och sekundära underhållsperiod tillämpas under distributionen, kan detta redigeras så snart distributionen är klar. Inget Underhåll sker utanför de angivna underhållsperioderna utan föregående meddelande.
Utför följande steg om du vill visa underhållsschemat som har tillämpats på ditt informationslager i portalen.

Utför följande steg om du vill visa underhållsschemat som har tillämpats på ditt informationslager i portalen.
1.  Logga in på [Azure-portalen](https://portal.azure.com/).
2.  Välj det datalager som du vill visa. 
3.  Den valda Azure SQL Data Warehouse öppnas på översiktsbladet. Underhållsschema som tillämpas på valda datalagret visas nedan underhållsschema (förhandsversion).

![Översiktsbladet](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Nästa steg
[Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) om att skapa, visa och hantera aviseringar med Azure Monitor.
[Läs mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) om Webhook-åtgärder för loggaviseringsregler.
[Läs mer](https://docs.microsoft.com/azure/service-health/service-health-overview) om Azure Service Health


