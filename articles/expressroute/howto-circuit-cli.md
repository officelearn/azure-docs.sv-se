---
title: 'Skapa och ändra en ExpressRoute-krets: Azure CLI'
description: Den här artikeln visar hur du skapar, etablerar, verifierar, uppdaterar, tar bort och avetableras en ExpressRoute-krets med CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: cherylmc
ms.openlocfilehash: b967e1d8751a9c6a5214fef5241d57e954ad9f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476159"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Skapa och ändra en ExpressRoute-krets med hjälp av CLI


I den här artikeln beskrivs hur du skapar en Azure ExpressRoute-krets med hjälp av COMMAND Line Interface (CLI). Den här artikeln visar också hur du kontrollerar status, uppdaterar eller tar bort och avetablera en krets. Om du vill använda en annan metod för att arbeta med ExpressRoute-kretsar kan du välja artikeln i följande lista:

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

## <a name="before-you-begin"></a>Innan du börjar

* Innan du börjar ska du installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).
* Granska [förutsättningarna](expressroute-prerequisites.md) och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Skapa och etablera en ExpressRoute-krets

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Logga in på ditt Azure-konto och välj din prenumeration

Logga in på ditt Azure-konto för att starta konfigurationen. Om du använder CloudShell "Prova", loggas du in automatiskt. Använd följande exempel för att hjälpa dig att ansluta:

```azurecli-interactive
az login
```

Kontrollera prenumerationerna för kontot.

```azurecli-interactive
az account list
```

Välj den prenumeration som du vill skapa en ExpressRoute-krets för.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Få en lista över leverantörer, platser och bandbredder som stöds

Innan du skapar en ExpressRoute-krets behöver du listan över anslutningsleverantörer, platser och bandbreddsalternativ som stöds. CLI-kommandot `az network express-route list-service-providers` returnerar den här informationen som du ska använda i senare steg:

```azurecli-interactive
az network express-route list-service-providers
```

Svaret liknar följande exempel:

```output
[
  {
    "bandwidthsOffered": [
      {
        "offerName": "50Mbps",
        "valueInMbps": 50
      },
      {
        "offerName": "100Mbps",
        "valueInMbps": 100
      },
      {
        "offerName": "200Mbps",
        "valueInMbps": 200
      },
      {
        "offerName": "500Mbps",
        "valueInMbps": 500
      },
      {
        "offerName": "1Gbps",
        "valueInMbps": 1000
      },
      {
        "offerName": "2Gbps",
        "valueInMbps": 2000
      },
      {
        "offerName": "5Gbps",
        "valueInMbps": 5000
      },
      {
        "offerName": "10Gbps",
        "valueInMbps": 10000
      }
    ],
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/expressRouteServiceProviders/",
    "location": null,
    "name": "AARNet",
    "peeringLocations": [
      "Melbourne",
      "Sydney"
    ],
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "tags": null,
    "type": "Microsoft.Network/expressRouteServiceProviders"
  },
```

Kontrollera svaret för att se om din anslutningsleverantör finns med i listan. Anteckna följande information, som du behöver när du skapar en krets:

* Namn
* PeeringLocations
* Bandbredder Som erbjuds

Du är nu redo att skapa en ExpressRoute-krets.

### <a name="3-create-an-expressroute-circuit"></a>3. Skapa en ExpressRoute-krets

> [!IMPORTANT]
> Din ExpressRoute-krets faktureras från det att en servicenyckel utfärdas. Utför den här åtgärden när anslutningsprovidern är redo att etablera kretsen.
>
>

Om du inte redan har en resursgrupp måste du skapa en innan du skapar expressroutekretsen. Du kan skapa en resursgrupp genom att köra följande kommando:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

Följande exempel visar hur du skapar en 200 Mbps ExpressRoute-krets via Equinix i Silicon Valley. Om du använder en annan leverantör och olika inställningar ersätter du den informationen när du gör din begäran.

Se till att du anger rätt SKU-nivå och SKU-familj:

* SKU-nivån avgör om en ExpressRoute-krets är [Lokal,](expressroute-faqs.md#expressroute-local)Standard eller [Premium](expressroute-faqs.md#expressroute-premium). Du kan ange *Lokal,* *Standard* eller *Premium*.
* SKU-familjen bestämmer faktureringstypen. Du kan ange *Datadata för datapriser* för ett dataabonnemang med datapriser och *Unlimiteddata* för ett obegränsat dataabonnemang. Du kan ändra *faktureringstypen* från Datadata till *Unlimiteddata,* men du kan inte ändra typen från *Unlimiteddata* till *Datapriser*. En *lokal* krets är endast *Unlimiteddata.*


Din ExpressRoute-krets faktureras från det att en servicenyckel utfärdas. Följande exempel är en begäran om en ny tjänstnyckel:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Svaret innehåller servicenyckeln.

### <a name="4-list-all-expressroute-circuits"></a>4. Lista alla ExpressRoute-kretsar

Om du vill hämta en lista över alla ExpressRoute-kretsar som du har skapat kör du `az network express-route list` kommandot. Du kan hämta den här informationen när som helst med hjälp av det här kommandot. Om du vill visa en lista över alla kretsar ringer du samtalet utan parametrar.

```azurecli-interactive
az network express-route list
```

Din tjänstnyckel visas i fältet *ServiceKey* för svaret.

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

Du kan få detaljerade beskrivningar av alla parametrar genom att köra kommandot med parametern '-h'.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Skicka tjänstnyckeln till din anslutningsleverantör för etablering

"ServiceProviderProvisioningState" ger information om det aktuella läget för etablering på tjänsteleverantörens sida. Statusen ger tillståndet på Microsoft-sidan. Mer information finns i [artikeln Arbetsflöden](expressroute-workflows.md#expressroute-circuit-provisioning-states).

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

```output
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Kretsen ändras till följande tillstånd när anslutningsleverantören är i färd med att aktivera det åt dig:

```output
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

För att du ska kunna använda en ExpressRoute-krets måste den vara i följande tillstånd:

```output
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Kontrollera regelbundet kretsnyckelns status och tillstånd

Om du kontrollerar kretsnyckelns status och tillstånd får du veta när din leverantör har aktiverat din krets. När kretsen har konfigurerats visas "ServiceProviderProvisioningState" som "Etablerad", som visas i följande exempel:

```azurecli-interactive
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

Svaret liknar följande exempel:

```output
"allowClassicOperations": false,
"authorizations": [],
"circuitProvisioningState": "Enabled",
"etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
"gatewayManagerEtag": null,
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
"location": "westus",
"name": "MyCircuit",
"peerings": [],
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup",
"serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
"serviceProviderNotes": null,
"serviceProviderProperties": {
  "bandwidthInMbps": 200,
  "peeringLocation": "Silicon Valley",
  "serviceProviderName": "Equinix"
},
"serviceProviderProvisioningState": "NotProvisioned",
"sku": {
  "family": "UnlimitedData",
  "name": "Standard_MeteredData",
  "tier": "Standard"
},
"tags": null,
"type": "Microsoft.Network/expressRouteCircuits]
```

### <a name="7-create-your-routing-configuration"></a>7. Skapa din routningskonfiguration

Steg-för-steg-instruktioner finns i konfigurationsartikeln för Routning av [ExpressRoute-kretsroutning](howto-routing-cli.md) för att skapa och ändra krets peerings.

> [!IMPORTANT]
> Dessa instruktioner gäller endast för kretsar som skapas med tjänsteleverantörer som erbjuder anslutningstjänster för lager 2. Om du använder en tjänsteleverantör som erbjuder hanterade layer 3-tjänster (vanligtvis en IP VPN, till exempel MPLS), konfigurerar och hanterar anslutningsleverantören routning åt dig.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Länka ett virtuellt nätverk till en ExpressRoute-krets

Länka sedan ett virtuellt nätverk till expressroutekretsen. Använd artikeln [Länka virtuella nätverk till ExpressRoute-kretsar.](howto-linkvnet-cli.md)

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Ändra en ExpressRoute-krets

Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen. Du kan göra följande ändringar utan avbrott:

* Du kan aktivera eller inaktivera ett ExpressRoute premium-tillägg för din ExpressRoute-krets.
* Du kan öka bandbredden på din ExpressRoute-krets förutsatt att det finns kapacitet tillgänglig på porten. Det går dock inte att nedgradera bandbredden för en krets.
* Du kan ändra mätplanen från datapriser till obegränsad data. Det går dock inte att ändra avläsningsplanen från obegränsad data till datapriser.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Mer information om begränsningar och begränsningar finns i Vanliga frågor och svar om [ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Så här aktiverar du Premium-tillägget ExpressRoute

Du kan aktivera Premium-tillägget ExpressRoute för din befintliga krets med hjälp av följande kommando:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Kretsen har nu ExpressRoute premium tilläggsfunktioner aktiverade. Vi börjar fakturera dig för premiumtilläggsfunktionen så snart kommandot har körts.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Så här inaktiverar du Premium-tillägget ExpressRoute

> [!IMPORTANT]
> Den här åtgärden kan misslyckas om du använder resurser som är större än vad som är tillåtet för standardkretsen.
>
>

Innan du inaktiverar Premium-tillägget ExpressRoute bör du förstå följande kriterier:

* Innan du nedgraderar från premium till standard måste du se till att du har färre än 10 virtuella nätverk kopplade till kretsen. Om du har fler än 10 misslyckas din uppdateringsbegäran och vi fakturerar dig till högre priser.
* Du måste ta bort länken till alla virtuella nätverk i andra geopolitiska regioner. Om du inte tar bort länken till alla dina virtuella nätverk misslyckas din uppdateringsbegäran och vi fakturerar dig till högre priser.
* Din rutttabell måste vara mindre än 4 000 vägar för privat peering. Om flödestabellstorleken är större än 4 000 vägar sjunker BGP-sessionen. Sessionen kommer inte att kunna återsebar förrän antalet annonserade prefix är under 4 000.

Du kan inaktivera Premium-tillägget ExpressRoute för den befintliga kretsen med hjälp av följande exempel:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Så här uppdaterar du ExpressRoute-kretsbandbredden

Om du har de bandbreddsalternativ som stöds för din leverantör kontrollerar du [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md). Du kan välja vilken storlek som helst som är större än storleken på din befintliga krets.

> [!IMPORTANT]
> Om det finns otillräcklig kapacitet på den befintliga porten kan du behöva återskapa ExpressRoute-kretsen. Du kan inte uppgradera kretsen om det inte finns någon ytterligare kapacitet tillgänglig på den platsen.
>
> Du kan inte minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradering bandbredd kräver att du avetablerar ExpressRoute-kretsen och sedan återetablerar en ny ExpressRoute-krets.
>

När du har bestämt vilken storlek du behöver använder du följande kommando för att ändra storlek på kretsen:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Din krets är dimensionerad på Microsoft-sidan. Därefter måste du kontakta anslutningsleverantören för att uppdatera konfigurationer på deras sida för att matcha den här ändringen. När du har gjort det här meddelandet börjar vi fakturera dig för det uppdaterade bandbreddsalternativet.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Så här flyttar du SKU:n från uppmätt till obegränsad

Du kan ändra SKU för en ExpressRoute-krets med hjälp av följande exempel:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Så här styr du åtkomsten till de klassiska miljöerna och Resurshanteraren

Granska instruktionerna i [Move ExpressRoute-kretsar från klassikern till Resurshanterarens distributionsmodell](expressroute-howto-move-arm.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Avetablera och ta bort en ExpressRoute-krets

Om du vill avetablera och ta bort en ExpressRoute-krets måste du förstå följande kriterier:

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen. Om den här åtgärden misslyckas kontrollerar du om några virtuella nätverk är länkade till kretsen.
* Om etableringstillståndet för ExpressRoute-kretstjänstleverantören **etablerar** eller **etableras**måste du samarbeta med din tjänsteleverantör för att avetablera kretsen på deras sida. Vi fortsätter att reservera resurser och fakturera dig tills tjänsteleverantören slutför avetablering av kretsen och meddelar oss.
* Du kan ta bort kretsen om tjänsteleverantören har avetablerat kretsen. När en krets avetableras anges etableringstillståndet för tjänsteleverantören till **Inte etablerat**. Därmed avbryts faktureringen för kretsen.

Du kan ta bort expressroutekretsen genom att köra följande kommando:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Nästa steg

När du har skapat kretsen kontrollerar du att du utför följande uppgifter:

* [Skapa och ändra routning för din ExpressRoute-krets](howto-routing-cli.md)
* [Länka ditt virtuella nätverk till din ExpressRoute-krets](howto-linkvnet-cli.md)
