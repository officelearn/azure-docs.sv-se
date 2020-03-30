---
title: 'Azure ExpressRoute: Konfigurera ExpressRoute Global Reach: CLI'
description: Den här artikeln hjälper dig att länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk och aktivera Global Reach.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: jaredro
ms.openlocfilehash: a39cf4e09a70ca2b1225d699c84abf0e7f1d2eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476414"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Konfigurera ExpressRoute Global Reach med hjälp av Azure CLI

Den här artikeln hjälper dig att konfigurera Azure ExpressRoute Global Reach med hjälp av Azure CLI. Mer information finns i [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Innan du startar konfigurationen ska du uppfylla följande krav:

* Installera den senaste versionen av Azure CLI. Se [Installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).
* Förstå ExpressRoute-arbetsflödena [workflows](expressroute-workflows.md)för kretsetablering .
* Kontrollera att dina ExpressRoute-kretsar är i etablerat tillstånd.
* Kontrollera att Azure-privat peering är konfigurerad på dina ExpressRoute-kretsar.  

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Logga in på ditt Azure-konto för att starta konfigurationen. Följande kommando öppnar din standardwebbläsare och frågar dig om inloggningsuppgifter för ditt Azure-konto:  

```azurecli
az login
```

Om du har flera Azure-prenumerationer kontrollerar du prenumerationerna för kontot:

```azurecli
az account list
```

Ange den prenumeration som du vill använda:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifiera dina ExpressRoute-kretsar för konfiguration

Du kan aktivera ExpressRoute Global Reach mellan två ExpressRoute-kretsar, så länge de finns i länder/regioner som stöds och har skapats på olika peering-platser. Om din prenumeration äger båda kretsarna kan du välja vilken krets som helst för att köra konfigurationen som förklaras senare i den här artikeln. Om de två kretsarna finns i olika Azure-prenumerationer måste du ha auktorisering från en Azure-prenumeration och måste skicka in dess auktoriseringsnyckel när du kör konfigurationskommandot i den andra Azure-prenumerationen.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Aktivera anslutning mellan lokala nätverk

När du kör kommandot för att aktivera anslutning bör du tänka på följande krav för parametervärden:

* *peer-krets* bör vara det fullständiga resurs-ID. Ett exempel:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *adressprefixet* måste vara ett "/29" IPv4-undernät (till exempel "10.0.0.0/29"). Vi använder IP-adresser i det här undernätet för att upprätta anslutning mellan de två ExpressRoute-kretsarna. Du får inte använda adresser i det här undernätet i dina virtuella Azure-nätverk eller i lokala nätverk.

Kör följande CLI-kommando för att ansluta två ExpressRoute-kretsar:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLI-utgången ser ut så här:

```output
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

När den här åtgärden är klar har du anslutning mellan lokala nätverk på båda sidor via dina två ExpressRoute-kretsar.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Aktivera anslutning mellan ExpressRoute-kretsar i olika Azure-prenumerationer

Om de två kretsarna inte finns i samma Azure-prenumeration behöver du auktorisering. I följande konfiguration genererar du auktorisering i krets 2:s prenumeration och skickar auktoriseringsnyckeln till krets 1.

1. Generera en auktoriseringsnyckel:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   CLI-utgången ser ut så här:

   ```output
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

1. Anteckna både resurs-ID och auktoriseringsnyckel för krets 2.

1. Kör följande kommando mot krets 1 och skicka in krets 2:s resurs-ID och auktoriseringsnyckel:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

När den här åtgärden är klar har du anslutning mellan lokala nätverk på båda sidor via dina två ExpressRoute-kretsar.

## <a name="get-and-verify-the-configuration"></a>Hämta och verifiera konfigurationen

Använd följande kommando för att verifiera konfigurationen på kretsen där konfigurationen gjordes (krets 1 i föregående exempel):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

I CLI-utdata visas *CircuitConnectionStatus*. Den talar om för dig om anslutningen mellan de två kretsarna är etablerad ("Ansluten") eller inte upprättad ("Frånkopplad"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Inaktivera anslutning mellan lokala nätverk

Om du vill inaktivera anslutningen kör du följande kommando mot kretsen där konfigurationen gjordes (krets 1 i föregående exempel).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Använd ```show``` kommandot för att verifiera statusen.

När den här åtgärden är klar har du inte längre någon anslutning mellan dina lokala nätverk via dina ExpressRoute-kretsar.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om ExpressRoute Global Reach](expressroute-global-reach.md)
* [Verifiera ExpressRoute-anslutning](expressroute-troubleshooting-expressroute-overview.md)
* [Länka en ExpressRoute-krets till ett virtuellt nätverk](expressroute-howto-linkvnet-arm.md)
