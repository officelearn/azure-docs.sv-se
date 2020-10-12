---
title: 'Azure-ExpressRoute: Konfigurera ExpressRoute Global Reach: CLI'
description: Lär dig hur du länkar ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk och aktivera Global Reach med hjälp av Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/12/2018
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 16a86982813b667ed5c761da27c8e9e5a43ab6cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322503"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Konfigurera ExpressRoute Global Reach med hjälp av Azure CLI

Den här artikeln hjälper dig att konfigurera Azure ExpressRoute-Global Reach med hjälp av Azure CLI. Mer information finns i [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Innan du startar konfigurationen måste du uppfylla följande krav:

* Installera den senaste versionen av Azure CLI. Se [Installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).
* Förstå ExpressRoute-krets-och etablerings [arbets flöden](expressroute-workflows.md).
* Se till att dina ExpressRoute-kretsar är i det tillstånd som har allokerats.
* Kontrol lera att Azures privata peering har kon figurer ATS på dina ExpressRoute-kretsar.  

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Logga in på ditt Azure-konto för att starta konfigurationen. Följande kommando öppnar standard webbläsaren och du uppmanas att ange inloggnings uppgifterna för ditt Azure-konto:  

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

Du kan aktivera ExpressRoute Global Reach mellan två ExpressRoute-kretsar, förutsatt att de finns i länder/regioner som stöds och har skapats på olika peering-platser. Om din prenumeration äger båda kretsarna kan du välja någon av kretsarna för att köra konfigurationen på det sätt som beskrivs längre fram i den här artikeln. Om de två kretsarna finns i olika Azure-prenumerationer, måste du ha behörighet från en Azure-prenumeration och måste skicka in dess verifierings nyckel när du kör konfigurations kommandot i den andra Azure-prenumerationen.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Aktivera anslutningar mellan dina lokala nätverk

Observera följande krav för parameter värden när du kör kommandot för att aktivera anslutning:

* *peer-kretsen* bör vara det fullständiga resurs-ID: t. Exempel:

  > /Subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}

* *address-prefixet* måste vara ett "/29"-IPv4-undernät (till exempel "10.0.0.0/29"). Vi använder IP-adresser i det här under nätet för att upprätta anslutningar mellan de två ExpressRoute-kretsarna. Du får inte använda adresser i det här under nätet i dina virtuella Azure-nätverk eller i dina lokala nätverk.

Kör följande CLI-kommando för att ansluta två ExpressRoute-kretsar:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLI-utdata ser ut så här:

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

När den här åtgärden har slutförts har du anslutning mellan dina lokala nätverk på båda sidorna genom dina två ExpressRoute-kretsar.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Aktivera anslutningar mellan ExpressRoute-kretsar i olika Azure-prenumerationer

Om de två kretsarna inte finns i samma Azure-prenumeration behöver du behörighet. I följande konfiguration genererar du auktorisering i krets 2-prenumerationen och överför auktoriseringsregeln till kretsen 1.

1. Generera en nyckel för autentisering:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   CLI-utdata ser ut så här:

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

1. Anteckna både resurs-ID och verifierings nyckel för krets 2.

1. Kör följande kommando mot krets 1, och skicka i krets 2 resurs-ID och auktoriseringskod:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

När den här åtgärden har slutförts har du anslutning mellan dina lokala nätverk på båda sidorna genom dina två ExpressRoute-kretsar.

## <a name="get-and-verify-the-configuration"></a>Hämta och verifiera konfigurationen

Använd följande kommando för att kontrol lera konfigurationen på kretsen där konfigurationen gjordes (krets 1 i föregående exempel):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

I CLI-utdata ser du *CircuitConnectionStatus*. Du får information om huruvida anslutningen mellan de två kretsarna är upprättad ("ansluten") eller inte upprättad ("frånkopplad"). 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Inaktivera anslutning mellan dina lokala nätverk

Om du vill inaktivera anslutningen kör du följande kommando mot den krets där konfigurationen gjordes (krets 1 i det tidigare exemplet).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Använd ```show``` kommandot för att kontrol lera statusen.

När den här åtgärden har slutförts kommer du inte längre att ha anslutning mellan dina lokala nätverk via dina ExpressRoute-kretsar.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om ExpressRoute Global Reach](expressroute-global-reach.md)
* [Verifiera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
* [Länka en ExpressRoute-krets till ett virtuellt nätverk](expressroute-howto-linkvnet-arm.md)
