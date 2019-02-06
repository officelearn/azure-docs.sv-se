---
title: Hämta mått med REST API
titlesuffix: Azure Load Balancer
description: 'Använd Azure REST API: er för att samla in hälsa och användning mått för belastningsutjämnaren för ett visst område för tid och datum.'
services: sql-database
author: KumudD
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: KumudD
ms.openlocfilehash: 5f92ae386730032e7da278b9dd2b0effef313741
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752066"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Få mätvärden för resursutnyttjande belastningsutjämnare med hjälp av REST-API

Den här anvisningen visar hur du samlar in antalet byte som bearbetas av en [Standardbelastningsutjämnare](/azure/load-balancer/load-balancer-standard-overview) för ett intervall på tid med den [Azure REST API](/rest/api/azure/).

Fullständiga dokumentationen och ytterligare exempel för REST API finns i den [Azure Monitor REST-referens för](/rest/api/monitor). 

## <a name="build-the-request"></a>Skapa begäran

Använd följande GET-begäran för att samla in den [ByteCount mått](/azure/load-balancer/load-balancer-standard-diagnostics#a-name--multidimensionalmetricsamulti-dimensional-metrics) från en Standard Load Balancer. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Begärandehuvud

Följande huvuden krävs: 

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange `application/json`.|  
|*Auktorisering:*|Krävs. Ange att ett giltigt `Bearer` [åtkomsttoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-parametrar

| Namn | Beskrivning |
| :--- | :---------- |
| subscriptionId | Prenumerations-ID som identifierar en Azure-prenumeration. Om du har flera prenumerationer, se [arbeta med flera prenumerationer](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Namnet på resursgruppen som innehåller resursen. Du kan hämta det här värdet från Azure Resource Manager-API, CLI eller portalen. |
| loadBalancerName | Namnet på Azure Load Balancer. |
| metricnames | Kommaavgränsad lista över giltiga [belastningsutjämnaren mått](/azure/load-balancer/load-balancer-standard-diagnostics). |
| API-versionen | API-version för begäran.<br /><br /> Det här dokumentet beskriver api-versionen `2018-01-01`och ingår i URL: en ovan.  |
| Tidsintervall | Tidsintervall för frågan. Det är en sträng med formatet `startDateTime_ISO/endDateTime_ISO`. Valfria parametern är inställd att returnera en dags data i det här exemplet. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Begärandetext

Inga begärandetexten krävs för den här åtgärden.

## <a name="handle-the-response"></a>Hantera svaret

Statuskod 200 returneras när listan över måttvärden returnerades. En fullständig lista över felkoder finns i den [referensdokumentation](/rest/api/monitor/metrics/list#errorresponse).

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
