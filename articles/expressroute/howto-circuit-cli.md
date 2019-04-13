---
title: 'Skapa och ändra en ExpressRoute-krets: Azure CLI | Microsoft Docs'
description: Den här artikeln visar hur du skapar, etablera, verifiera, uppdatera, ta bort och Avetablerar en ExpressRoute-krets med hjälp av CLI.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman;cherylmc
ms.openlocfilehash: 556589aa7a0a577b9b1a010cf4811922ebc6de52
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524896"
---
# <a name="create-and-modify-an-expressroute-circuit-using-cli"></a>Skapa och ändra en ExpressRoute-krets med hjälp av CLI


Den här artikeln beskriver hur du skapar en Azure ExpressRoute-krets med hjälp av kommandoradsgränssnittet (CLI). Den här artikeln visar också hur du kontrollerar status-, update- eller delete och avetablera en krets. Om du vill använda en annan metod för att arbeta med ExpressRoute-kretsar kan du välja artikeln i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
> 

## <a name="before-you-begin"></a>Innan du börjar

* Innan du börjar ska du installera den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).
* Granska den [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

## <a name="create"></a>Skapa och etablera en ExpressRoute-krets

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Logga in på ditt Azure-konto och välj din prenumeration

Du börjar din konfiguration genom att logga in på ditt Azure-konto. Om du använder CloudShell ”prova” har du loggat in automatiskt. Använd följande exempel för att ansluta:

```azurecli
az login
```

Kontrollera prenumerationerna för kontot.

```azurecli-interactive
az account list
```

Välj den prenumeration som du vill skapa en ExpressRoute-krets.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Hämta listan över providers som stöds, platser och bandbredder

Innan du skapar en ExpressRoute-krets, behöver du lista över anslutningsleverantörer som stöds, platser och bandbreddsalternativ för. CLI-kommando `az network express-route list-service-providers` returnerar den här informationen som du ska använda i senare steg:

```azurecli-interactive
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

Kontrollera svaret för att se om din anslutningsleverantör finns. Anteckna följande information som du behöver när du skapar en krets:

* Namn
* PeeringLocations
* BandwidthsOffered

Du är nu redo att skapa en ExpressRoute-krets.

### <a name="3-create-an-expressroute-circuit"></a>3. Skapa en ExpressRoute-krets

> [!IMPORTANT]
> ExpressRoute-kretsen debiteras från den tidpunkt då en Tjänstnyckel utfärdas. Utför den här åtgärden när anslutningsleverantören är redo att lägga till kretsen.
> 
> 

Om du inte redan har en resursgrupp, måste du skapa en innan du skapar din ExpressRoute-krets. Du kan skapa en resursgrupp genom att köra följande kommando:

```azurecli-interactive
az group create -n ExpressRouteResourceGroup -l "West US"
```

I följande exempel visar hur du skapar en 200 Mbit/s ExpressRoute-krets via Equinix i Silicon Valley. Om du använder en annan leverantör och olika inställningar, ersätter du den informationen när du gör din begäran. 

Kontrollera att du anger rätt SKU-nivån och SKU-serien:

* SKU-nivån avgör om en ExpressRoute-standard eller en ExpressRoute-premiumtillägget är aktiverat. Du kan ange Standard om du får du standard-SKU eller ”Premium” på premium-tillägget.
* SKU-familjen avgör vilken fakturering. Du kan ange 'Metereddata' för ett abonnemang med förbrukade data och ”Unlimiteddata” för en obegränsad dataplan. Du kan ändra fakturering typen från 'Metereddata' till 'Unlimiteddata', men du kan inte ändra typen från 'Unlimiteddata' till 'Metereddata'.


ExpressRoute-kretsen debiteras från den tidpunkt då en Tjänstnyckel utfärdas. I följande exempel är en begäran om en ny Tjänstnyckel:

```azurecli-interactive
az network express-route create --bandwidth 200 -n MyCircuit --peering-location "Silicon Valley" -g ExpressRouteResourceGroup --provider "Equinix" -l "West US" --sku-family MeteredData --sku-tier Standard
```

Svaret innehåller tjänstnyckeln.

### <a name="4-list-all-expressroute-circuits"></a>4. Lista över alla ExpressRoute-kretsar

Om du vill hämta en lista över alla ExpressRoute-kretsar som du skapade, kör den `az network express-route list` kommando. Du kan hämta den här informationen när som helst med hjälp av det här kommandot. Visa alla kretsar genom att göra anrop utan parametrar.

```azurecli-interactive
az network express-route list
```

Din nyckel för tjänstens visas i den *ServiceKey* i svaret.

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

Du kan få detaljerade beskrivningar av alla parametrar genom att köra kommandot med den ”-h” parametern.

```azurecli-interactive
az network express-route list -h
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Skicka tjänstnyckeln till din anslutningsleverantör för etablering

'Korsanslutningens' innehåller information om det aktuella tillståndet för etablering på tjänstleverantör sida. Status innehåller tillståndet på Microsoft-sida. Mer information finns i den [arbetsflöden artikeln](expressroute-workflows.md#expressroute-circuit-provisioning-states).

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

```azurecli-interactive
"serviceProviderProvisioningState": "NotProvisioned"
"circuitProvisioningState": "Enabled"
```

Kretsen ändras till följande tillstånd när anslutningsleverantören håller på att aktivera den för du:

```azurecli-interactive
"serviceProviderProvisioningState": "Provisioning"
"circuitProvisioningState": "Enabled"
```

Du kan använda en ExpressRoute-krets, måste den vara i följande tillstånd:

```azurecli-interactive
"serviceProviderProvisioningState": "Provisioned"
"circuitProvisioningState": "Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Regelbundet kontrollera status och tillståndet för nyckeln krets

Kontrollera status och tillståndet för krets nyckeln får du reda på när din provider har aktiverat din krets. När kretsen har konfigurerats, visas ”Korsanslutningens” som etablerad, som visas i följande exempel:

```azurecli-interactive
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

Stegvisa instruktioner finns i den [ExpressRoute-krets routningskonfiguration](howto-routing-cli.md) artikeln om du vill skapa och ändra krets peerings.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapas med tjänstleverantörer som erbjuder tjänster för layer-2-anslutning. Om du använder en leverantör av tjänster som erbjuder Hanterade layer 3-tjänster (vanligtvis en IP VPN, t.ex. MPLS), anslutningsleverantören konfigurerar och hanterar routning åt dig.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Länka ett virtuellt nätverk till en ExpressRoute-krets

Därefter länka ett virtuellt nätverk till ExpressRoute-kretsen. Använd den [länka virtuella nätverk till ExpressRoute-kretsar](howto-linkvnet-cli.md) artikeln.

## <a name="modify"></a>Ändra en ExpressRoute-krets

Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen. Du kan göra följande ändringar utan avbrott:

* Du kan aktivera eller inaktivera en ExpressRoute-premiumtillägget för ExpressRoute-kretsen.
* Du kan öka bandbredden för ExpressRoute-kretsen, förutsatt att det finns tillgänglig kapacitet på porten. Dock stöds nedgradera bandbredden för en krets inte. 
* Du kan ändra Avläsning av planen från förbrukade Data till obegränsade Data. Men stöds ändrar Avläsning av planen från obegränsade Data till förbrukade Data inte.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Mer information om gränser och begränsningar finns i den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aktivera Expressroutes premiumtillägg

Du kan aktivera Expressroutes premiumtillägg för din befintliga krets med hjälp av följande kommando:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Premium
```

Kretsen har aktiverat ExpressRoute premium-tilläggsfunktioner. Vi börjar fakturering för premium-tillägg-funktionen när kommandot har körts.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Inaktivera Expressroutes premiumtillägg

> [!IMPORTANT]
> Den här åtgärden kan misslyckas om du använder resurser som är större än vad som tillåts för standard-krets.
> 
> 

Innan du inaktiverar Expressroutes premiumtillägg känna till följande kriterier:

* Innan du börjar Nedgradera från premium till standard måste du kontrollera att du har färre än 10 virtuella nätverk som är länkade till kretsen. Om du har fler än 10 din begäran om uppdatering misslyckas och vi fakturerar dig enligt priserna för premium.
* Du måste ta bort länken alla virtuella nätverk i andra geopolitiska regioner. Om du inte ta bort länken dina virtuella nätverk, din begäran om uppdatering misslyckas och debiteras du enligt priserna för premium.
* Din routningstabell måste vara mindre än 4 000 vägar för privat peering. Om din väg tabell är större än 4 000 vägar, utelämnar BGP-sessionen. Sessionen kommer inte att reenabled tills antalet annonserade prefix är under 4 000.

Du kan inaktivera ExpressRoute premium-tillägget för befintliga kretsen med hjälp av följande exempel:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-tier Standard
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Uppdatera ExpressRoute-kretsens bandbredd

Bandbreddsalternativ som stöds för din leverantör, kontrollera den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md). Du kan välja valfri storlek som är större än storleken på din befintliga krets.

> [!IMPORTANT]
> Om det finns lite kapacitet på befintliga porten, kan du behöva återskapa ExpressRoute-kretsen. Du kan inte uppgradera kretsen om det finns inga ytterligare kapacitet på den platsen.
>
> Du kan inte minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradera bandbredd måste du ta bort etableringen av ExpressRoute-kretsen och etablera om en ny ExpressRoute-krets.
>

När du har bestämt storleken som du behöver, Använd följande kommando för att ändra storlek på din krets:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --bandwidth 1000
```

Din krets storlek som stämmer på Microsoft-sida. Därefter måste du kontakta din anslutningsleverantör om du vill uppdatera konfigurationer på sin sida för att matcha den här ändringen. När du har gjort det här meddelandet börja vi fakturering du för alternativet uppdaterade bandbredd.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Om du vill flytta SKU: N från förbrukning till obegränsad

Du kan ändra SKU: N för en ExpressRoute-krets med hjälp av följande exempel:

```azurecli-interactive
az network express-route update -n MyCircuit -g ExpressRouteResourceGroup --sku-family UnlimitedData
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Styra åtkomsten till klassiskt och Resource Manager-miljöer

Granska anvisningarna i [flytta ExpressRoute-kretsar från klassiskt till Resource Manager-distributionsmodellen](expressroute-howto-move-arm.md).

## <a name="delete"></a>Avetablera och ta bort en ExpressRoute-krets

Om du vill avetablera och ta bort en ExpressRoute-krets, kontrollera att du förstår följande kriterier:

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen. Kontrollera om alla virtuella nätverken är länkade till kretsen om åtgärden misslyckas.
* Om leverantören för ExpressRoute-krets Etableringsstatus är **etablering** eller **etablerad**, måste du samarbeta med din tjänstleverantör för att avetablera kretsen på sidan. Vi fortsätter att reservera resurser och debitera dig tills tjänstleverantören har slutförts avetablera kretsen och meddelar oss.
* Du kan ta bort kretsen tjänstleverantören har tagit bort etableringen kretsen. När en krets datorn avetableras så tjänstleverantören Etableringsstatus anges till **inte etablerats**. Därmed avbryts faktureringen för kretsen.

Du kan ta bort ExpressRoute-kretsen genom att köra följande kommando:

```azurecli-interactive
az network express-route delete  -n MyCircuit -g ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Nästa steg

När du har skapat din krets, se till att du gör följande:

* [Skapa och ändra routning för ExpressRoute-krets](howto-routing-cli.md)
* [Länka ditt virtuella nätverk till ExpressRoute-krets](howto-linkvnet-cli.md)
