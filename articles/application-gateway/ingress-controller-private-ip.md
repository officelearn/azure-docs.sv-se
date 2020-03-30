---
title: Använda privat IP-adress för intern routning för en ingångsslutpunkt
description: Den här artikeln innehåller information om hur du använder privata IPs för intern routning och därmed exponera ingress-slutpunkten i ett kluster för resten av det virtuella nätverket.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795496"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Använda privat IP för intern routning för en ingående slutpunkt 

Den här funktionen gör det möjligt `Virtual Network` att exponera ingångsslutpunkten i en privat IP.

## <a name="pre-requisites"></a>Förutsättningar  
Application Gateway med en [privat IP-konfiguration](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Det finns två sätt att konfigurera styrenheten så att den använder Privat IP för inträngning,

## <a name="assign-to-a-particular-ingress"></a>Tilldela en viss inträngning
Om du vill exponera en viss inträngning [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) över Privat IP använder du anteckning i Ingress.

### <a name="usage"></a>Användning
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

För programgateways utan en privat IP ignoreras Ingresses som kommenteras med. `appgw.ingress.kubernetes.io/use-private-ip: "true"` Detta kommer att anges i ingress händelsen och AGIC pod logg.

* Fel som anges i ingresshändelsen

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Fel som anges i AGIC Loggar

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Tilldela globalt
Om kravet är att begränsa alla Ingresses att exponeras över privat IP, användning `appgw.usePrivateIP: true` i `helm` config.

### <a name="usage"></a>Användning
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

Detta gör att ingressstyrenheten filtrerar IP-adresskonfigurationerna för en privat IP när frontend-lyssnarna konfigureras på Application Gateway.
AGIC kommer panik `usePrivateIP: true` och krasch om och ingen privat IP tilldelas.

> [!NOTE]
> Application Gateway v2 SKU kräver en offentlig IP. Om du kräver att Programgateway [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) ska vara privat, bifoga en till Programgateways undernät för att begränsa trafiken.
