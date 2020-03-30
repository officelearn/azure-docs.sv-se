---
title: IP-adresser till Azure API Management-tjänsten | Microsoft-dokument
description: Lär dig hur du hämtar IP-adresserna för en Azure API Management-tjänst och när de ändras.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047732"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP-adresser för Azure API Management

I den här artikeln beskriver vi hur du hämtar IP-adresserna för Azure API Management-tjänsten. IP-adresser kan vara offentliga eller privata om tjänsten finns i ett virtuellt nätverk.

Du kan använda IP-adresser för att skapa brandväggsregler, filtrera inkommande trafik till serverdtjänsterna eller begränsa den utgående trafiken.

## <a name="ip-addresses-of-api-management-service"></a>IP-adresser till API Management-tjänsten

Varje API Management-tjänstinstans på utvecklar-, basic-, standard- eller premium-nivå har offentliga IP-adresser, som endast är exklusiva för den tjänstinstansen (de delas inte med andra resurser). 

Du kan hämta IP-adresserna från översiktsinstrumentpanelen för din resurs i Azure-portalen.

![IP-adress för API-hantering](media/api-management-howto-ip-addresses/public-ip.png)

Du kan också hämta dem programmässigt med följande API-anrop:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Offentliga IP-adresser kommer att vara en del av svaret:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

I [flera regionala distributioner](api-management-howto-deploy-multi-region.md)har varje regional distribution en offentlig IP-adress.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP-adresser för API Management-tjänsten i VNet

Om api management-tjänsten finns i ett virtuellt nätverk har den två typer av IP-adresser – offentliga och privata.

Offentliga IP-adresser används för `3443` intern kommunikation i port - för att hantera konfiguration (till exempel via Azure Resource Manager). I den externa VNet-konfigurationen används de också för körning API-trafik. När en begäran skickas från API Management till en serverad servering med offentlig vänteläge visas en offentlig IP-adress som ursprunget för begäran.

Privata VIP-adresser (Virtual IP), **som endast** är tillgängliga i det interna [virtuella VNet-läget,](api-management-using-with-internal-vnet.md)används för att ansluta från nätverket till API Management-slutpunkter - gateways, utvecklarportalen och hanteringsplanet för direkt API-åtkomst. Du kan använda dem för att konfigurera DNS-poster i nätverket.

Du kommer att se adresser av båda typerna i Azure-portalen och i svaret på API-anropet:

![API-hantering i VNet IP-adress](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API Management använder en offentlig IP-adress för anslutningar utanför det virtuella nätverket och en privat IP-adress för anslutningar inom det virtuella nätverket.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>IP-adresser för api-hanteringstjänst för förbrukningsnivå

Om din API Management-tjänst är en tjänst på förbrukningsnivå har den ingen dedikerad IP-adress. Tjänsten Förbrukningsnivå körs på en delad infrastruktur och utan en deterministisk IP-adress. 

För trafikbegränsningsändamål kan du använda intervallet för IP-adresser för Azure-datacenter. Se [dokumentationsartikeln](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) för Azure Functions för exakta steg.

## <a name="changes-to-the-ip-addresses"></a>Ändringar i IP-adresserna

På nivåerna Utvecklare, Basic, Standard och Premium för API Management är de offentliga IP-adresserna (VIP) statiska under en tjänsts livstid, med följande undantag:

* Tjänsten tas bort och återskapas.
* Tjänstprenumerationen [avbryts](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) eller [varnas](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (till exempel för utebliven betalning) och återinförs sedan.
* Azure Virtual Network läggs till eller tas bort från tjänsten.
* API Management-tjänsten växlas mellan externt och internt VNet-distributionsläge.

I flera regioner ändras den regionala [IP-adressen](api-management-howto-deploy-multi-region.md)om en region töms och sedan återinförs.
