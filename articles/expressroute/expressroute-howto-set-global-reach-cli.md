---
title: 'Konfigurera ExpressRoute Global räckvidd: Azure CLI | Microsoft Docs'
description: Den här artikeln hjälper dig att länka ExpressRoute-kretsar tillsammans för att göra ett privat nätverk mellan ditt lokala nätverk och aktivera Global räckvidd.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: 28df8f59944ccac9d731e15a558d864beed2f3ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60364222"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Konfigurera ExpressRoute Global räckvidd med hjälp av Azure CLI

Den här artikeln hjälper dig att konfigurera Azure ExpressRoute Global räckvidd med hjälp av Azure CLI. Mer information finns i [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Innan du börjar konfigurationen måste du uppfylla följande krav:

* Installera den senaste versionen av Azure CLI. Se [Installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).
* Förstå ExpressRoute-krets etablering [arbetsflöden](expressroute-workflows.md).
* Kontrollera att din ExpressRoute-kretsar har statusen etablerad.
* Kontrollera att Azures privata peering har konfigurerats på ExpressRoute-kretsar.  

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Starta konfigurationen genom att logga in på ditt Azure-konto. Följande kommando öppnas din standardwebbläsare och du uppmanas att ange autentiseringsuppgifter för inloggning för dina Azure-konto:  

```azurecli
az login
```

Om du har flera Azure-prenumerationer kan du kontrollera prenumerationerna för kontot:

```azurecli
az account list
```

Ange den prenumeration som du vill använda:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifiera din ExpressRoute-kretsar för konfiguration

Du kan aktivera ExpressRoute Global räckvidd mellan alla två ExpressRoute-kretsar, så länge de befinner dig i länder som stöds och har skapats på olika peering-platser. Om din prenumeration äger både kretsar kan välja du antingen krets för att köra konfigurationen som beskrivs senare i den här artikeln. Om två kretsar finns i olika Azure-prenumerationer, du måste ha tillstånd från en Azure-prenumeration och måste klara i dess auktoriseringsnyckeln när du kör kommandot konfiguration i andra Azure-prenumeration.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Aktivera anslutning mellan ditt lokala nätverk

Observera följande krav för parametervärden när du kör kommandot genom att aktivera anslutning:

* *peer-krets* bör vara fullständiga resurs-ID. Exempel:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *adressprefixet* måste vara ett ”/ 29” IPv4-undernät (till exempel ”10.0.0.0/29”). Vi använder IP-adresser i det här undernätet för att upprätta en anslutning mellan de två ExpressRoute-kretsarna. Du måste inte använda adresser i det här undernätet i din Azure-nätverk eller i ditt lokala nätverk.

Kör följande CLI-kommando för att ansluta två ExpressRoute-kretsar:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLI-utdata ser ut så här:

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

När den här åtgärden är klar har du anslutningen mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Aktivera anslutning mellan ExpressRoute-kretsar i olika Azure-prenumerationer

Om två-kretsar som inte är i samma Azure-prenumeration kan behöver du auktorisering. I följande konfiguration, generera auktorisering i kretsen 2-prenumerationen och skicka auktoriseringsnyckeln så att den krets 1.

1. Generera en auktoriseringsnyckel:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   CLI-utdata ser ut så här:

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

1. Anteckna både resurs-ID och auktoriseringsnyckeln för krets 2.

1. Kör följande kommando mot krets 1, skicka i kretsen 2 resurs-ID och auktorisering nyckel:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

När den här åtgärden är klar har du anslutningen mellan ditt lokala nätverk på båda sidorna via två ExpressRoute-kretsarna.

## <a name="get-and-verify-the-configuration"></a>Hämta och verifiera konfigurationen

Använder du följande kommando för att kontrollera konfigurationen på kretsen där konfigurationen gjordes (kretsen 1 i föregående exempel):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

I CLI-utdata visas *CircuitConnectionStatus*. Du kan ta reda om anslutningen mellan två kretsar är upprätta (”ansluten”) eller inte upprätta (”frånkopplad”). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Inaktivera anslutning mellan ditt lokala nätverk

Om du vill inaktivera anslutning kör du följande kommando mot kretsen där konfigurationen gjordes (kretsen 1 i exemplet ovan).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Använd den ```show``` kommando för att kontrollera statusen.

När den här åtgärden är klar har du inte längre anslutning mellan ditt lokala nätverk via ExpressRoute-kretsar.

## <a name="next-steps"></a>Nästa steg

* [Mer information om ExpressRoute Global räckvidd](expressroute-global-reach.md)
* [Kontrollera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
* [Länka en ExpressRoute-krets till ett virtuellt nätverk](expressroute-howto-linkvnet-arm.md)
