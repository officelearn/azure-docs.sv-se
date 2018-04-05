---
title: Aktivera diagnostikloggning för Batch - händelser i Azure | Microsoft Docs
description: Registrera och analysera diagnostiska logghändelser för Azure Batch-kontot resurser som pooler och uppgifter.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: e14e611d-12cd-4671-91dc-bc506dc853e5
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c41c8c9f8fd9302c610ce356b0485e33ea3c967d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="log-events-for-diagnostic-evaluation-and-monitoring-of-batch-solutions"></a>Logghändelser diagnostiska utvärdering och övervakning av Batch-lösningar

Precis som med många Azure-tjänster, skickar Batch-tjänsten händelser för vissa resurser under livslängden för resursen. Du kan aktivera Azure Batch diagnostikloggar kan registrera händelser för resurser som pooler och uppgifter och sedan använda loggarna för diagnostiska utvärdering och övervakning. Händelser, t.ex. poolen skapa, ta bort poolen, uppgiften start, uppgift slutförd och andra ingår i Batch diagnostikloggar.

> [!NOTE]
> Den här artikeln beskriver loggning av händelser för Batch-kontot själva resurserna, inte jobbet och uppgift utdata. Mer information om lagra utdata för jobb och aktiviteter finns [kvarstår Azure Batch-jobb- och utdata](batch-task-output.md).
> 
> 

## <a name="prerequisites"></a>Krav
* [Azure Batch-kontot](batch-account-create-portal.md)
* [Azure-lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account)
  
  Du måste skapa ett Azure Storage-konto där Azure kommer att lagra loggfilerna för att bevara Batch diagnostikloggar. Du anger det här lagringskontot när du [aktivera diagnostikloggning](#enable-diagnostic-logging) för Batch-kontot. Storage-konto som du anger när du aktiverar Logginsamling inte är samma som ett konto för länkad lagring som anges i den [programpaket](batch-application-packages.md) och [aktivitet utdata beständiga](batch-task-output.md) artiklar.
  
  > [!WARNING]
  > Du är **debiteras** för data som lagras i Azure Storage-konto. Detta inkluderar diagnostikloggar som beskrivs i den här artikeln. Ha detta i åtanke när du utformar din [logga bevarandeprincip](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).
  > 
  > 

## <a name="enable-diagnostic-logging"></a>Aktivera diagnostisk loggning
Diagnostikloggning är inte aktiverad som standard för Batch-kontot. Du måste uttryckligen aktivera diagnostikloggning för varje Batch-kontot som du vill övervaka:

[Så här aktiverar du samling diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs)

Vi rekommenderar att du läser hela [översikt av Azure diagnostikloggar](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) artikel för att få en bättre förståelse av inte bara hur du aktiverar loggning, men log-kategorier som stöds av olika Azure-tjänster. Till exempel Azure Batch för närvarande stöder en logg kategori: **tjänstloggar**.

## <a name="service-logs"></a>Service Logs
Azure Batch-tjänstloggar innehålla händelser som sänds av Azure Batch-tjänsten under livslängden för en Batch resurs, till exempel en pool eller aktiviteten. Varje händelse som orsakat av Batch lagras i det angivna Storage-kontot i JSON-format. Detta är till exempel brödtexten i ett exempel på en **pool Skapa händelse**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Varje händelsemeddelandet finns i en JSON-fil i det angivna Azure Storage-kontot. Om du vill komma åt loggarna direkt kan du granska den [schemat för diagnostikloggar i lagringskonto](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Tjänsten logghändelser
Batch-tjänsten skickar för närvarande följande händelser i loggen för tjänsten. Den här listan får inte vara fullständig, eftersom ytterligare händelser kan ha lagts eftersom den här artikeln senast uppdaterades.

| **Tjänsten logghändelser** |
| --- |
| [Skapa poolen][pool_create] |
| [Poolen delete start][pool_delete_start] |
| [Poolen ta bort klar][pool_delete_complete] |
| [Poolen storleksändring start][pool_resize_start] |
| [Poolen storleksändring slutförd][pool_resize_complete] |
| [Uppgiften start][task_start] |
| [Uppgift slutförd][task_complete] |
| [Aktiviteten misslyckas][task_fail] |

## <a name="next-steps"></a>Nästa steg
Förutom att lagra diagnostiska logghändelser i ett Azure Storage-konto kan du också strömma Batch-tjänsten logghändelser till en [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md), och skickar dem till [Azure logganalys](../log-analytics/log-analytics-overview.md).

* [Strömma Azure diagnostikloggar i Händelsehubbar](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
  
  Strömma Batch diagnostiska händelser till den skalbara tjänsten för dataingång, Händelsehubbar. Händelsehubbar kan mata in miljontals händelser per sekund, vilket du kan sedan omvandla och lagra med hjälp av en leverantör av realtidsanalys.
* [Analysera Azure diagnostikloggar med logganalys](../log-analytics/log-analytics-azure-storage.md)
  
  Skicka dina diagnostikloggar till logganalys där du kan analysera dem i portalen Operations Management Suite (OMS) eller exportera dem för analys i Power BI eller Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
