---
title: Hämta Azure Virtual Machine-användningsdata med REST API
description: Använd Azure REST-API:er för att samla in användningsmått för en virtuell dator.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 07e91f3d9fd32f01db91415bfd90746cd1aef403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944743"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Hämta användningsmått för virtuella datorer med REST API

Det här exemplet visar hur du hämtar CPU-användningen för en [Virtuell Linux-dator](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) med hjälp av [Azure REST API](/rest/api/azure/).

Fullständig referensdokumentation och ytterligare exempel för REST API finns i [Azure Monitor REST-referensen](/rest/api/monitor). 

## <a name="build-the-request"></a>Skapa begäran

Använd följande GET-begäran för att samla in [procent cpu-måttet](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) från en virtuell dator

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | Namnet på azure-resursgruppen som är associerad med resursen. Du kan hämta det här värdet från Azure Resource Manager API, CLI eller portalen. |
| vmname (vmname) | Namnet på Den virtuella Azure-datorn. |
| metricnames | Kommaavgränsad lista över giltiga [belastningsutjämnad mått](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | DEN API-version som ska användas för begäran.<br /><br /> Det här dokumentet `2018-01-01`täcker api-version , som ingår i ovanstående URL.  |
| Gått | Sträng med följande `startDateTime_ISO/endDateTime_ISO` format som definierar tidsintervallet för de returnerade måtten. Den här valfria parametern är inställd på att returnera en dags data i exemplet. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Begärandetext

Det behövs ingen begärandeorgan för den här åtgärden.

## <a name="handle-the-response"></a>Hantera svaret

Statuskod 200 returneras när listan över måttvärden returneras. En fullständig lista över felkoder finns i [referensdokumentationen](/rest/api/monitor/metrics/list#errorresponse).

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
