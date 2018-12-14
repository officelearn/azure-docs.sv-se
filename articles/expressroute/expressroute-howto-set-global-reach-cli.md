---
title: 'Konfigurera ExpressRoute Global räckvidd: Azure CLI | Microsoft Docs'
description: Den här artikeln hjälper dig att länka ExpressRoute-kretsar tillsammans för att göra ett privat nätverk mellan ditt lokala nätverk och aktivera Global räckvidd.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9a8e0a5df9383d8e3d7159aa916b0e4fbfeea948
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384076"
---
# <a name="configure-expressroute-global-reach-using-azure-cli-preview"></a>Konfigurera ExpressRoute Global räckvidd med Azure CLI (förhandsversion)
Den här artikeln hjälper dig att konfigurera ExpressRoute Global räckvidd med Azure CLI. Mer information finns i [ExpressRouteRoute Global räckvidd](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Innan du börjar
> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Innan du börjar konfigurationen måste du kontrollera följande krav.

* Installera den senaste versionen av Azure CLI. Se [installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).
* Förstå ExpressRoute kretsetablering [arbetsflöden](expressroute-workflows.md).
* Kontrollera att din ExpressRoute-kretsar har statusen etablerad.
* Kontrollera att Azures privata peering har konfigurerats på ExpressRoute-kretsar.  

### <a name="log-into-your-azure-account"></a>Logga in på ditt Azure-konto
Om du vill starta konfigurationen, måste du logga in ditt Azure-konto. Kommandot kommer din standardwebbläsare öppnas och du uppmanas att ange autentiseringsuppgifter för inloggning för ditt Azure-konto.  

```azurecli
az login
```

Om du har flera Azure-prenumerationer anger du prenumerationerna för kontot.

```azurecli
az account list
```

Ange den prenumeration som du vill använda.

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifiera din ExpressRoute-kretsar för konfiguration
Du kan aktivera ExpressRoute Global räckvidd mellan två ExpressRoute-kretsar så länge som de finns i de länder/regioner som stöds och de skapas vid en annan peering-platser. Om din prenumeration äger både kretsar kan du välja antingen krets för att köra konfigurationen i avsnitten nedan. Om två kretsar finns i olika Azure-prenumerationer, kommer du behöver tillstånd från en Azure-prenumeration och skicka in auktoriseringsnyckeln när du kör kommandot konfiguration i andra Azure-prenumeration.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Aktivera anslutning mellan ditt lokala nätverk

När du kör kommandot för att aktivera anslutning kan du överväga följande värden:

* *peer-krets* bör vara fullständiga resurs-ID. Exempel: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}
  ```
* *-AddressPrefix* måste vara ett/29 IPv4-undernät, t.ex. ”10.0.0.0/29”. Vi ska använda IP-adresser i det här undernätet för att upprätta en anslutning mellan de två ExpressRoute-kretsarna. Du måste inte använda adresser i det här undernätet i ditt virtuella Azure-nätverk eller i ditt lokala nätverk.

Kör följande CLI för att ansluta två ExpressRoute-kretsar. Använd följande exempel-kommando:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLI-utdata ser ut som i följande exempel:

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

När åtgärden ovan är klar bör du ha anslutning mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>ExpressRoute-kretsar i olika Azure-prenumerationer

Om två kretsar inte finns i samma Azure-prenumeration, måste auktorisering. I följande konfiguration auktorisering genereras i kretsen 2-prenumeration och auktoriseringsnyckeln skickas till krets 1.

Generera en auktoriseringsnyckel. 
```azurecli
az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
```

Det ser ut som följande CLI-utdata.

```azurecli
{
  "authorizationKey": "<authorizationKey>",
  "authorizationUseStatus": "Available",
  "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
  "name": "<AuthorizationName>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<Circuit2ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/authorizations"
}
```

Anteckna krets 2 resurs-Id samt auktoriseringsnyckeln.

Kör följande kommando mot krets 1. Skicka in krets 2 resurs-Id och auktoriseringsnyckeln 
```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
```

När åtgärden ovan är klar bör du ha anslutning mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

## <a name="get-and-verify-the-configuration"></a>Hämta och verifiera konfigurationen

Använder du följande kommando för att kontrollera konfigurationen på kretsen där konfigurationen har gjorts, d.v.s. krets 1 i exemplet ovan.

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

I CLI-utdata visas *CircuitConnectionStatus*. Det informerar dig om anslutningen mellan två kretsar har upprättats ”ansluten”, eller inte, ”frånkopplad”. 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Inaktivera anslutning mellan ditt lokala nätverk

Om du vill inaktivera det, kör du kommandon mot kretsen där konfigurationen har gjorts, d.v.s. krets 1 i exemplet ovan.

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Du kan köra visa CLI för att kontrollera statusen. 

När åtgärden ovan är klar kan har du inte längre anslutning mellan ditt lokala nätverk via ExpressRoute-kretsar. 


## <a name="next-steps"></a>Nästa steg
* [Mer information om ExpressRoute Global räckvidd](expressroute-global-reach.md)
* [Kontrollera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
* [Länka ExpressRoute-krets till Azure-nätverk](expressroute-howto-linkvnet-arm.md)


