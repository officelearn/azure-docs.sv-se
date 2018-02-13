---
title: "Nära realtid mått aviseringar i Azure-Monitor | Microsoft Docs"
description: "Lär dig hur du använder nära mått aviseringar i realtid för att övervaka Azure-resurs med en frekvens som är så liten som 1 minut."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: 6370f4596e6b20962c6de0dbcbd5f86c3b7777cc
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>Nära realtid mått aviseringar (förhandsgranskning)
Azure-Monitor stöder en ny aviseringstyp kallas nära realtid mått aviseringar (förhandsversion). Den här funktionen är för närvarande i förhandsversion.

Nära realtid mått aviseringar skiljer sig från vanlig mått aviseringar på flera sätt:

- **Förbättrad latens**: nära realtid mått aviseringar kan övervaka ändringar i måttvärden med en frekvens som är så liten som 1 minut.
- **Mer kontroll över mått villkor**: du kan definiera bättre Varningsregler i nära realtid mått aviseringar. Aviseringarna stöd för övervakning maximala, minsta, genomsnittlig och totala värden för mått.
- **Kombinerade övervakning av flera mått**: nära realtid mått aviseringar kan du övervaka flera (för närvarande högst två mått) med en enskild regel. En varning aktiveras om båda mått bryta mot sina respektive tröskelvärden för den angivna tidsperioden.
- **Modulära meddelandesystem**: nära realtid mått aviseringar använder [åtgärdsgrupper](monitoring-action-groups.md). Du kan använda åtgärdsgrupper skapa modulära åtgärder. Du kan återanvända åtgärdsgrupper för flera Varningsregler.

> [!NOTE]
> I nära realtid mått postaviseringsfunktionen är för närvarande i förhandsversion. Funktioner och användarupplevelse kan ändras.
>

## <a name="resources-you-can-use-with-near-real-time-metric-alerts"></a>Resurser som du kan använda med nästan realtid mått aviseringar
Här är en fullständig lista över resurstyper som stöds för nära realtid mått aviseringar:

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-for-metrics-that-use-dimensions"></a>Nära realtid mått aviseringar för mått som använder dimensioner
Nära realtid mått stöder aviseringar aviseringar för mått som använder dimensioner. Du kan använda dimensioner för att filtrera dina mått för rätt nivå. Aviseringar för mått som använder dimensioner stöds nära realtid mått för följande typer av resurser:

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (stöds bara för storage-konton i USA regioner)
* Microsoft.Storage/storageAccounts/services (stöds bara för storage-konton i USA regioner)

## <a name="create-a-near-real-time-metric-alert"></a>Skapa en nära realtid mått
För närvarande kan du skapa nära realtid mått aviseringar endast i Azure-portalen. Stöd för att konfigurera nästan realtid mått aviseringar med hjälp av PowerShell, Azure-kommandoradsgränssnittet (Azure CLI) och Azure övervakaren REST API: er kommer snart.

Upplevelse för att skapa en nära realtid mått avisering har flyttats till den nya **aviseringar (förhandsgranskning)** sidan. Även om de aktuella aviseringarna sidan visar **lägga till nära realtid mått avisering**, du omdirigeras till den **aviseringar (förhandsgranskning)** sidan.

Information om hur du skapar en nära realtid mått avisering finns [skapa en aviseringsregel i Azure portal](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="manage-near-real-time-metric-alerts"></a>Hantera nära realtid mått aviseringar
När du har skapat en nära realtid mått avisering kan du hantera aviseringen med hjälp av stegen som beskrivs i [Hantera aviseringar i Azure portal](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="payload-schema"></a>Nyttolasten i schemat

POST-åtgärden innehåller följande JSON-nyttolast och schemat för alla nära realtid mått aviseringar:

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>Nästa steg

* Mer information om den nya [aviseringar (förhandsgranskning)-upplevelse](monitoring-overview-unified-alerts.md).
* Lär dig mer om [Logga varningar i Azure varningar (förhandsgranskning)](monitor-alerts-unified-log.md).
* Lär dig mer om [aviseringar i Azure](monitoring-overview-alerts.md).