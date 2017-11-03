---
title: "Skapa och ändra en Azure ExpressRoute-krets: CLI | Microsoft Docs"
description: "Den här artikeln beskriver hur du skapar, etablera, verifiera, uppdatera, ta bort och ta bort etableringen av en ExpressRoute-krets med hjälp av CLI."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: cd4e31336fd0e90b13f1c3984de89f24e65b052b
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Skapa och ändra en ExpressRoute-krets med hjälp av CLI


Den här artikeln beskriver hur du skapar en Azure ExpressRoute-krets med hjälp av kommandoradsgränssnittet (CLI). Den här artikeln visar även hur att kontrollera status, uppdatera eller ta bort och ta bort etableringen av en krets. Om du vill använda en annan metod för att arbeta med ExpressRoute-kretsar kan du välja artikeln från följande lista:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>Innan du börjar

* Innan du börjar ska du installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI 2.0](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).
* Granska de [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.

## <a name="create"></a>Skapa och etablera en ExpressRoute-krets

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Logga in på ditt Azure-konto och välja din prenumeration

Påbörja din konfiguration genom att logga in på ditt Azure-konto. Använd följande exempel för att ansluta:

```azurecli
az login
```

Kontrollera prenumerationerna för kontot.

```azurecli
az account list
```

Välj den prenumeration som du vill skapa en ExpressRoute-krets.

```azurecli
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Hämta en lista över providers som stöds, platser och bandbredder

Innan du skapar en ExpressRoute-krets måste listan över providers stöds anslutning, platser och alternativ för bandbredd. De CLI kommandot 'az express route lista--leverantörer' returnerar informationen som du ska använda i senare steg:

```azurecli
az network express-route list-service-providers
```

Svaret liknar följande exempel:

```azurecli
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

Kontrollera svaret för att se om anslutningsleverantören anges. Anteckna följande information som du behöver när du skapar en krets:

* Namn
* PeeringLocations
* BandwidthsOffered

Nu är du redo att skapa en ExpressRoute-krets.

### <a name="3-create-an-expressroute-circuit"></a>3. Skapa en ExpressRoute-krets

> [!IMPORTANT]
> ExpressRoute-krets faktureras från den tidpunkt då en nyckel har utfärdats. Utför den här åtgärden när anslutningen providern är redo att etablera kretsen.
> 
> 

Om du inte redan har en resursgrupp kan skapa du en innan du skapar ExpressRoute-kretsen. Du kan skapa en resursgrupp genom att köra följande kommando:

```azurecli
az group create -n ExpressRouteResourceGroup -l "West US"
```

I följande exempel visas hur du skapar en 200 Mbit/s ExpressRoute-krets via Equinix i Silicon dal. Om du använder en annan provider och andra inställningar, i stället informationen när du gör din begäran. 

Se till att du anger rätt SKU-nivå och SKU-familjen:

* SKU-nivå bestämmer om en ExpressRoute-standard eller premium-tillägg ett ExpressRoute är aktiverad. Du kan ange Standard om du för att få standard SKU eller Premium-för premium-tillägg.
* SKU-familjen avgör vilken faktureringsinformation. Du kan ange 'Metereddata' för en plan för förbrukade data och 'Unlimiteddata' för en obegränsad plan. Du kan ändra fakturering typen från 'Metereddata' till 'Unlimiteddata', men du kan inte ändra typen från 'Unlimiteddata' till 'Metereddata'.


ExpressRoute-krets faktureras från den tidpunkt då en nyckel har utfärdats. Följande exempel är en begäran om en ny nyckel:

```azurecli
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Svaret innehåller nyckeln för tjänsten.

### <a name="4-list-all-expressroute-circuits"></a>4. Visa en lista med alla ExpressRoute-kretsar

Kör kommandot 'az express route nätverkslistan' om du vill hämta en lista över alla ExpressRoute-kretsar som du skapade. Du kan hämta den här informationen när som helst genom att använda det här kommandot. Om du vill visa alla kretsar ringa utan parametrar.

```azurecli
az network express-route list
```

Nyckeln för tjänsten finns i den *ServiceKey* i svaret.

```azurecli
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

Du kan få en detaljerad beskrivning av alla parametrar genom att köra kommandot med de '-h ”parametern.

```azurecli
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Skicka tjänstnyckeln till anslutningsleverantören för etablering

'ServiceProviderProvisioningState' innehåller information om det aktuella tillståndet för etablering på sida-leverantör. Status innehåller tillståndet för Microsoft-sida. Mer information finns i [arbetsflöden artikel](expressroute-workflows.md#expressroute-circuit-provisioning-states).

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

```azurecli
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Kretsen ändras till följande tillstånd när anslutningen providern håller på att du:

```azurecli
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Du kan använda en ExpressRoute-krets, måste den vara i följande tillstånd:

```azurecli
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Regelbundet kontrollera status och tillståndet för nyckeln krets

Kontrollera status och tillståndet för krets nyckeln får du reda på när din leverantör har aktiverat kretsen. När kretsen har konfigurerats, visas 'ServiceProviderProvisioningState' som etablerad, som visas i följande exempel:

```azurecli
az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
```

Svaret liknar följande exempel:

```azurecli
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

Stegvisa instruktioner finns i [ExpressRoute-krets routningskonfiguration](howto-routing-cli.md) artikeln om du vill skapa och ändra krets peerkopplingar.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapats med leverantörer som erbjuder layer 2 tjänster. Om du använder en tjänstprovider som erbjuder Hanterade layer 3-tjänster (vanligtvis en IP VPN, t.ex. MPLS), anslutningsleverantören konfigurerar och hanterar routning för dig.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Länka ett virtuellt nätverk till en ExpressRoute-krets

Därefter länka ett virtuellt nätverk till ExpressRoute-kretsen. Använd den [länka virtuella nätverk för ExpressRoute-kretsar](howto-linkvnet-cli.md) artikel.

## <a name="modify"></a>Ändra en ExpressRoute-krets

Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen. Du kan göra följande ändringar utan avbrott:

* Du kan aktivera eller inaktivera tillägget ExpressRoute premium för ExpressRoute-kretsen.
* Du kan öka bandbredden för ExpressRoute-krets, under förutsättning att det finns tillgänglig kapacitet på porten. Dock stöds nedgradera bandbredden för en krets inte. 
* Du kan ändra avläsning planen från förbrukade Data till obegränsad. Men stöds om du ändrar avläsning planen från obegränsad till förbrukade Data inte.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Mer information om gränser och begränsningar finns i [ExpressRoute vanliga frågor och svar](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Så här aktiverar du ExpressRoute premium-tillägg

Du kan aktivera ExpressRoute premium-tillägg för befintliga kretsen med hjälp av följande kommando:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Kretsen har nu tilläggsfunktioner för ExpressRoute premium aktiverat. Vi börjar fakturering för premium-tillägg-funktionen när kommandot har körts.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Att inaktivera tillägget ExpressRoute premium

> [!IMPORTANT]
> Den här åtgärden kan misslyckas om du använder resurser som är större än vad som tillåts för standard-kretsen.
> 
> 

Innan du inaktiverar ExpressRoute premium-tillägg, förstå följande kriterier:

* Innan du Nedgradera från premium till standard måste du kontrollera att du har färre än 10 virtuella nätverk som är kopplad till kretsen. Om du har fler än 10 din begäran om uppdatering misslyckas och vi debitera dig till Premier.
* Du måste ta bort länken alla virtuella nätverk i andra geopolitiska regioner. Om du inte ta bort länken alla virtuella nätverk, din begäran om uppdatering misslyckas och vi debitera dig till Premier.
* Din routningstabellen måste vara mindre än 4 000 vägar för privat peering. Om din väg tabell är större än 4 000 vägar, släpper BGP-sessionen. Sessionen kommer inte att reenabled tills antalet annonserade prefix understiger 4 000.

Du kan inaktivera ExpressRoute premium-tillägg för befintlig krets med hjälp av följande exempel:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Uppdatera bandbredd för ExpressRoute-krets

Alternativen stöds bandbredd för providern Kontrollera den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md). Du kan välja valfri storlek som är större än storleken på en befintlig krets.

> [!IMPORTANT]
> Om det finns för lite kapacitet på befintliga porten, kan du behöva återskapa ExpressRoute-kretsen. Du kan inte uppgradera kretsen om det finns inga ytterligare kapacitet på den platsen.
>
> Du kan minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradera bandbredd måste du ta bort etableringen av ExpressRoute-kretsen och etablera en ny ExpressRoute-krets.
>

När du väljer den storlek som du behöver, använder du följande kommando för att ändra storlek på kretsen:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Kretsen storlek på Microsoft-sida. Därefter måste du kontakta anslutningsleverantören om du vill uppdatera konfigurationer på sidan så att den matchar den här ändringen. När du har gjort det här meddelandet börjar fakturering du för alternativet uppdaterade bandbredd.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Om du vill flytta SKU: N från förbrukning till obegränsad

Du kan ändra en ExpressRoute-krets SKU med hjälp av följande exempel:

```azurecli
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Styra åtkomsten till klassiskt och Resource Manager-miljöer

Granska instruktionerna i [flytta ExpressRoute-kretsar från klassiskt till Resource Manager-distributionsmodellen](expressroute-howto-move-arm.md).

## <a name="delete"></a>Borttagning och tar bort en ExpressRoute-krets

Om du vill ta bort etableringen och tar bort en ExpressRoute-krets, kontrollera att du är medveten om följande villkor:

* Du måste ta bort länken alla virtuella nätverk från ExpressRoute-kretsen. Kontrollera om något virtuellt nätverk är kopplade till kretsen om åtgärden misslyckas.
* Om leverantören för ExpressRoute-kretsen Etableringsläge är **etablering** eller **etablerad**, du måste arbeta med tjänstleverantören för att ta bort etableringen krets på sidan. Vi kan fortsätta att reservera resurser och debitera dig tills tjänstleverantör har slutförts avetablering kretsen och meddela oss.
* Du kan ta bort kretsen tjänstleverantör har avetableras kretsen. När en anslutning är avetableras tjänstleverantör Etableringsstatus som **inte etablerats**. Detta stoppar faktureringen för kretsen.

Du kan ta bort ExpressRoute-krets genom att köra följande kommando:

```azurecli
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Nästa steg

När du har skapat kretsen gör att du följande uppgifter:

* [Skapa och ändra routning för ExpressRoute-krets](howto-routing-cli.md)
* [Länka ditt virtuella nätverk till ExpressRoute-krets](howto-linkvnet-cli.md)