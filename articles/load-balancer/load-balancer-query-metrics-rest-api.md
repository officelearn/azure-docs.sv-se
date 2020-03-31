---
title: Hämta mått med REST API
titleSuffix: Azure Load Balancer
description: I den här artikeln kommer du igång med Azure REST API:er för att samla in hälso- och användningsmått för Azure Load Balancer.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 760ec8a945ab88b63dde2de75f5354818facf4f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225247"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Hämta användningsmått för belastningsutjämnare med REST API

Samla in antalet byte som bearbetas av en [standardbelastningsutjämning](/azure/load-balancer/load-balancer-standard-overview) för ett tidsintervall med hjälp av [Azure REST API](/rest/api/azure/).

Fullständig referensdokumentation och ytterligare exempel för REST API finns i [Azure Monitor REST-referensen](/rest/api/monitor). 

## <a name="build-the-request"></a>Skapa begäran

Använd följande GET-begäran för att samla in [bytecount-måttet](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) från en standardbelastningsutjämnare. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Begärandehuvuden

Följande huvuden krävs: 

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange till `application/json`.|  
|*Auktorisering:*|Krävs. Ange till en giltig -`Bearer` [åtkomsttoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-parametrar

| Namn | Beskrivning |
| :--- | :---------- |
| subscriptionId | Prenumerations-ID:t som identifierar en Azure-prenumeration. Om du har flera prenumerationer läser [du Arbeta med flera prenumerationer](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Namnet på resursgruppen som innehåller resursen. Du kan hämta det här värdet från Azure Resource Manager API, CLI eller portalen. |
| loadBalancerName | Namnet på Azure Load Balancer. |
| måttnamn | Kommaavgränsad lista över giltiga [belastningsutjämnad mått](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | DEN API-version som ska användas för begäran.<br /><br /> Det här dokumentet `2018-01-01`täcker api-version , som ingår i ovanstående URL.  |
| Gått | Tidsspannet för frågan. Det är en sträng med `startDateTime_ISO/endDateTime_ISO`följande format . Den här valfria parametern är inställd på att returnera en dags data i exemplet. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Begärandetext

Det behövs ingen begärandeorgan för den här åtgärden.

## <a name="handle-the-response"></a>Hantera svaret

Statuskod 200 returneras när listan över måttvärden returneras. En fullständig lista över felkoder finns i [referensdokumentationen](/rest/api/monitor/metrics/list#errorresponse).

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
