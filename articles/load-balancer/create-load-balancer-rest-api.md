---
title: Skapa en Azure Load Balancer med REST API
titlesuffix: Azure Load Balancer
description: Lär dig hur du skapar en Azure-belastningsutjämnare med hjälp av REST API.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: kumud
ms.openlocfilehash: 159fe9d6a891858d8d2cc2315e9544b79eb44cff
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079729"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>Skapa en Azure belastningsutjämnare med hjälp av REST API

En Azure Load Balancer distribuerar nya inkommande flöden som inkommer till belastningsutjämnarens klientdel till backend-poolen instanser, enligt regler och hälsokontroller av slutpunkter. Belastningsutjämnaren är tillgänglig i två SKU: er: Basic och Standard. Förstå skillnaden mellan de två versionerna av SKU [Load Balancers SKU jämförelser](load-balancer-overview.md#skus).
 
Den här anvisningen visar hur du skapar en Azure belastningsutjämnare med hjälp av [Azure REST API](/rest/api/azure/) för att läsa in belastningsutjämna inkommande begäran mellan flera virtuella datorer inom en Azure-nätverk. Fullständig referensdokumentation och ytterligare exempel finns i den [Azure Load Balancer REST-referens för](/rest/api/load-balancer/).
 
## <a name="build-the-request"></a>Skapa begäran
Använd följande HTTP PUT-begäran för att skapa en ny Azure belastningsutjämnare.
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>URI-parametrar

|Namn  |I  |Krävs |Typ |Beskrivning |
|---------|---------|---------|---------|--------|
|subscriptionId   |  sökväg       |  True       |   sträng      |  Autentiseringsuppgifterna för prenumerationen som unikt identifierar Microsoft Azure-prenumeration. Prenumerations-ID utgör en del av URI: N för varje tjänstanrop.      |
|resourceGroupName     |     sökväg    | True        |  sträng       |   Namnet på resursgruppen.     |
|loadBalancerName     |  sökväg       |      True   |    sträng     |    Namnet på belastningsutjämnaren.    |
|API-versionen    |   DocumentDB     |  True       |     sträng    |  API-klientversionen.      |



### <a name="request-body"></a>Begärandetext

Den enda obligatoriska parametern är `location`. Om du inte definierar den *SKU* version, en grundläggande belastningsutjämnare skapas som standard.  Använd [valfria parametrar](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body) anpassa belastningsutjämnaren.

| Namn | Typ | Beskrivning |
| :--- | :--- | :---------- |
| location | sträng | Resursplats. Hämta en aktuell lista över platser med hjälp av den [List Locations](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations) igen. |


## <a name="example-create-and-update-a-basic-load-balancer"></a>Exempel: Skapa och uppdatera en grundläggande belastningsutjämnare

I det här exemplet skapar du först en grundläggande belastningsutjämnare tillsammans med dess resurser. Därefter konfigurerar du belastningsutjämningsresurserna som innehåller en klientdels-IP-konfiguration, en backend-adresspool, en belastningsutjämningsregel, en hälsoavsökning, och en inkommande NAT-regel.

Innan du skapar en belastningsutjämnare med hjälp av exemplet nedan skapar ett virtuellt nätverk med namnet *vnetlb* med ett undernät med namnet *subnetlb* i en resursgrupp med namnet *rg1* i den **USA, östra** plats.

### <a name="step-1-create-a-basic-load-balancer"></a>STEG 1. Skapa en grundläggande lastbalanserare
I det här steget skapar du en grundläggande belastningsutjämnare med namnet *lb* på den **USA, ÖSTRA** plats inom den *rg1* resursgrupp.
#### <a name="sample-request"></a>Exempelbegäran

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Begärandetext

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>STEG 2. Konfigurera resurser för belastningsutjämning
I det här steget konfigurerar du belastningsutjämnaren *lb* resurser som inkluderar en frontend-IP-konfiguration (*fe-lb*), en backend-adresspool (*vara lb*), en regel (för belastningsutjämning *rulelb*), en hälsoavsökning (*avsökningen-lb*), och en inkommande NAT-regel (*i-nat-rule*).
#### <a name="sample-request"></a>Exempelbegäran

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>Begärandetext

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```
