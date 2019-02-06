---
title: Hämta användningsdata för Azure-dator med hjälp av REST-API | Microsoft Docs
description: 'Använd Azure REST API: er för att samla in mätvärden för resursutnyttjande för en virtuell dator.'
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 924154a64673b4ff646f3b6ece373b278ee37181
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754854"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Hämta användningsstatistik för virtuell dator med hjälp av REST-API

Det här exemplet visar hur du hämtar processoranvändningen för en [Linux-dator](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) med hjälp av den [Azure REST API](/rest/api/azure/).

Fullständig referensdokumentation och ytterligare exempel för REST API finns i den [Azure Monitor REST-referens för](/rest/api/monitor). 

## <a name="build-the-request"></a>Skapa begäran

Använd följande GET-begäran för att samla in den [procent CPU mått](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) från en virtuell dator

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | Namnet på Azure-resursgrupp som hör till resursen. Du kan hämta det här värdet från Azure Resource Manager-API, CLI eller portalen. |
| vmname | Namnet på den Azure-datorn. |
| metricnames | Kommaavgränsad lista över giltiga [belastningsutjämnaren mått](/azure/load-balancer/load-balancer-standard-diagnostics). |
| API-versionen | API-version för begäran.<br /><br /> Det här dokumentet beskriver api-versionen `2018-01-01`och ingår i URL: en ovan.  |
| Tidsintervall | Sträng med formatet `startDateTime_ISO/endDateTime_ISO` som definierar tidsintervallet för den returnerade måtten. Valfria parametern är inställd att returnera en dags data i det här exemplet. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Begärandetext

Inga begärandetexten krävs för den här åtgärden.

## <a name="handle-the-response"></a>Hantera svaret

Statuskod 200 returneras när listan över måttvärden returnerades. En fullständig lista över felkoder finns i den [referensdokumentation](/rest/api/monitor/metrics/list#errorresponse).

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
