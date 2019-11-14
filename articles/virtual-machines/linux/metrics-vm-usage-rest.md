---
title: Hämta användnings data för Azure virtuell dator med hjälp av REST API
description: 'Använd Azure REST-API: erna för att samla in användnings mått för en virtuell dator.'
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: gwallace
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 523b81e53f2b0622b237993dbd88fb9492079c86
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035811"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Hämta användnings statistik för virtuella datorer med hjälp av REST API

Det här exemplet visar hur du hämtar processor användningen för en [virtuell Linux-dator](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) med hjälp av [Azure-REST API](/rest/api/azure/).

Fullständig referens dokumentation och ytterligare exempel för REST API finns i [Azure Monitor rest-referensen](/rest/api/monitor). 

## <a name="build-the-request"></a>Skapa begäran

Använd följande GET-begäran för att samla in [procent andel CPU-mått](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) från en virtuell dator

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Begärandehuvud

Följande huvuden krävs: 

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange till `application/json`.|  
|*Auktorisering:*|Krävs. Ange till en giltig `Bearer`-[åtkomsttoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-parametrar

| Namn | Beskrivning |
| :--- | :---------- |
| subscriptionId | Det prenumerations-ID som identifierar en Azure-prenumeration. Om du har flera prenumerationer kan du läsa mer i [arbeta med flera prenumerationer](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | Namnet på den Azure-resurs grupp som är kopplad till resursen. Du kan hämta det här värdet från Azure Resource Manager API, CLI eller portalen. |
| VMName | Namnet på den virtuella Azure-datorn. |
| metricnames | Kommaavgränsad lista över giltiga [load BALANCERS mått](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | Den API-version som ska användas för begäran.<br /><br /> Det här dokumentet beskriver API-version `2018-01-01`, som ingår i ovanstående URL.  |
| TimeSpan | Sträng med följande format `startDateTime_ISO/endDateTime_ISO` som definierar tidsintervallet för de returnerade måtten. Den här valfria parametern är inställd på att returnera en dags värde i exemplet. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Begärandetext

Ingen begär ande text krävs för den här åtgärden.

## <a name="handle-the-response"></a>Hantera svaret

Status koden 200 returneras när listan med mått värden returneras. En fullständig lista över felkoder finns i [referens dokumentationen](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Exempelsvar 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
