---
title: Hämta mått med REST API
titlesuffix: Azure Load Balancer
description: 'Använd Azure REST-API: erna för att samla in hälso-och användnings statistik för Load Balancer under ett angivet tidsintervall och datum.'
services: sql-database
author: asudbring
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: allensu
ms.openlocfilehash: 0d12dc04aff58dd6273d8d29d422bdbd9e7c886b
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274531"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Få Load Balancer användnings mått med hjälp av REST API

Den här instruktionen visar hur du samlar in antalet byte som bearbetas av en [standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) under ett tidsintervall med hjälp av [Azure REST API](/rest/api/azure/).

Fullständig referens dokumentation och ytterligare exempel för REST API finns i [Azure Monitor rest](/rest/api/monitor)-referensen. 

## <a name="build-the-request"></a>Skapa begäran

Använd följande GET-begäran för att samla in [ByteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) -måttet från en standard Load Balancer. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Begärandehuvud

Följande rubriker krävs: 

|Begär ande huvud|Beskrivning|  
|--------------------|-----------------|  
|*Innehålls typ:*|Obligatoriskt. Ange till `application/json`.|  
|*Authorization:*|Obligatoriskt. Ange en giltig `Bearer` [åtkomsttoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-parametrar

| Namn | Beskrivning |
| :--- | :---------- |
| subscriptionId | Det prenumerations-ID som identifierar en Azure-prenumeration. Om du har flera prenumerationer kan du läsa mer i [arbeta med flera prenumerationer](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Namnet på den resurs grupp som innehåller resursen. Du kan hämta det här värdet från Azure Resource Manager API, CLI eller portalen. |
| loadBalancerName | Namnet på Azure Load Balancer. |
| metricnames | Kommaavgränsad lista över giltiga Load Balancers [mått](/azure/load-balancer/load-balancer-standard-diagnostics). |
| API-versionen | Den API-version som ska användas för begäran.<br /><br /> Det här dokumentet beskriver API- `2018-01-01`versionen, som ingår i ovanstående URL.  |
| TimeSpan | Frågans TimeSpan. Det är en sträng med följande format `startDateTime_ISO/endDateTime_ISO`. Den här valfria parametern är inställd på att returnera en dags värde i exemplet. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Begärandetext

Ingen begär ande text krävs för den här åtgärden.

## <a name="handle-the-response"></a>Hantera svaret

Status koden 200 returneras när listan med mått värden returneras. En fullständig lista över felkoder finns i [referens dokumentationen](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Exempelsvar 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
