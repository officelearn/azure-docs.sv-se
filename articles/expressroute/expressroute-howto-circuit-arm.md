---
title: 'Skapa och ändra en ExpressRoute-krets: PowerShell: Azure Resource Manager | Microsoft Docs'
description: Den här artikeln beskriver hur du skapar, etablera, verifiera, uppdatera, ta bort och ta bort etableringen av en ExpressRoute-krets.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: f997182e-9b25-4a7a-b079-b004221dadcc
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 0d45e97cc42826375a99df16a73c9a7b0c359224
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31594057"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Skapa och ändra en ExpressRoute-krets med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln beskriver hur du skapar en Azure ExpressRoute-krets med hjälp av PowerShell-cmdlets och Azure Resource Manager-distributionsmodellen. Den här artikeln visar även hur du kontrollerar statusen för kretsen, uppdatera eller ta bort och ta bort etableringen av den.

## <a name="before-you-begin"></a>Innan du börjar
* Installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information finns i [översikt av Azure PowerShell](/powershell/azure/overview).
* Granska de [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.


## <a name="create"></a>Skapa och etablera en ExpressRoute-krets
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Logga in på ditt Azure-konto och välja din prenumeration
Påbörja din konfiguration genom att logga in på ditt Azure-konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzureRmAccount
```

Kontrollera prenumerationerna för kontot:

```powershell
Get-AzureRmSubscription
```

Välj den prenumeration som du vill skapa en ExpressRoute-krets för:

```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Hämta en lista över providers som stöds, platser och bandbredder
Innan du skapar en ExpressRoute-krets måste listan över providers stöds anslutning, platser och alternativ för bandbredd.

PowerShell-cmdleten **Get-AzureRmExpressRouteServiceProvider** returnerar informationen som du ska använda i senare steg:

```powershell
Get-AzureRmExpressRouteServiceProvider
```

Kontrollera om anslutningsleverantören finns med i listan. Anteckna följande information som du behöver senare när du skapar en krets:

* Namn
* PeeringLocations
* BandwidthsOffered

Nu är du redo att skapa en ExpressRoute-krets.   

### <a name="3-create-an-expressroute-circuit"></a>3. Skapa en ExpressRoute-krets
Om du inte redan har en resursgrupp kan skapa du en innan du skapar ExpressRoute-kretsen. Du kan göra det genom att köra följande kommando:

```powershell
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```


I följande exempel visas hur du skapar en 200 Mbit/s ExpressRoute-krets via Equinix i Silicon dal. Om du använder en annan provider och andra inställningar, i stället informationen när du gör din begäran. Använd följande exempel för att begära en ny nyckel:

```powershell
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Se till att du anger rätt SKU-nivå och SKU-familjen:

* SKU-nivå bestämmer om en ExpressRoute-standard eller premium-tillägg ett ExpressRoute är aktiverad. Du kan ange *Standard* att hämta standard SKU: N eller *Premium* för premium-tillägg.
* SKU-familjen avgör vilken faktureringsinformation. Du kan ange *Metereddata* för en plan för förbrukade data och *Unlimiteddata* för en obegränsad plan. Du kan ändra fakturering från *Metereddata* till *Unlimiteddata*, men du kan inte ändra typen från *Unlimiteddata* till *Metereddata*.

> [!IMPORTANT]
> ExpressRoute-krets faktureras från den tidpunkt då en nyckel har utfärdats. Se till att utföra den här åtgärden när anslutningen providern är redo att etablera kretsen.
> 
> 

Svaret innehåller nyckeln för tjänsten. Du kan få en detaljerad beskrivning av alla parametrar genom att köra följande kommando:

```powershell
get-help New-AzureRmExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Visa en lista med alla ExpressRoute-kretsar
Om du vill hämta en lista över alla ExpressRoute-kretsar som du har skapat kör den **Get-AzureRmExpressRouteCircuit** kommando:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Svaret ser ut ungefär så här:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Du kan hämta den här informationen när som helst genom att använda den `Get-AzureRmExpressRouteCircuit` cmdlet. Att göra anropet utan parametrar visar alla kretsar. Nyckeln för tjänsten finns i den *ServiceKey* fält:

```powershell
Get-AzureRmExpressRouteCircuit
```


Svaret ser ut ungefär så här:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Du kan få en detaljerad beskrivning av alla parametrar genom att köra följande kommando:

```powershell
get-help Get-AzureRmExpressRouteCircuit -detailed
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Skicka tjänstnyckeln till anslutningsleverantören för etablering
*ServiceProviderProvisioningState* innehåller information om det aktuella tillståndet för etablering på sida-leverantör. Status innehåller tillståndet för Microsoft-sida. Mer information om krets etablering tillstånd finns [arbetsflöden](expressroute-workflows.md#expressroute-circuit-provisioning-states).

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Kretsen ändras till följande tillstånd när anslutningen providern håller på att du:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Du kan använda en ExpressRoute-krets, måste den vara i följande tillstånd:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Regelbundet kontrollera status och tillståndet för nyckeln krets
Kontrollera status och tillståndet för krets nyckeln får du reda på när din leverantör har aktiverat kretsen. När kretsen har konfigurerats, *ServiceProviderProvisioningState* visas som *etablerad*som visas i följande exempel:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Svaret ser ut ungefär så här:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []

### <a name="7-create-your-routing-configuration"></a>7. Skapa din routningskonfiguration
Stegvisa instruktioner finns i [ExpressRoute-krets routningskonfiguration](expressroute-howto-routing-arm.md) artikeln om du vill skapa och ändra krets peerkopplingar.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapats med leverantörer som erbjuder layer 2 tjänster. Om du använder en tjänstprovider som erbjuder Hanterade layer 3-tjänster (vanligtvis en IP VPN, t.ex. MPLS), anslutningsleverantören konfigurerar och hanterar routning för dig.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Länka ett virtuellt nätverk till en ExpressRoute-krets
Därefter länka ett virtuellt nätverk till ExpressRoute-kretsen. Använd den [länka virtuella nätverk för ExpressRoute-kretsar](expressroute-howto-linkvnet-arm.md) artikeln när du arbetar med Resource Manager-distributionsmodellen.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Hämta status för en ExpressRoute-krets
Du kan hämta den här informationen när som helst genom att använda den **Get-AzureRmExpressRouteCircuit** cmdlet. Att göra anropet utan parametrar visar alla kretsar.

```powershell
Get-AzureRmExpressRouteCircuit
```


Svaret liknar följande exempel:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                            "ServiceProviderName": "Equinix",
                                            "PeeringLocation": "Silicon Valley",
                                            "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Du kan få information om en specifik ExpressRoute-krets genom att skicka resursgruppens namn och krets namn som en parameter till anropet:

```powershell
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Svaret ser ut ungefär så här:

    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : westus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                            "Tier": "Standard",
                                            "Family": "MeteredData"
                                          }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Du kan få en detaljerad beskrivning av alla parametrar genom att köra följande kommando:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Ändra en ExpressRoute-krets
Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen.

Du kan göra följande uppgifter utan avbrott:

* Aktivera eller inaktivera tillägget ExpressRoute premium för ExpressRoute-kretsen.
* Öka bandbredden för ExpressRoute-krets under förutsättning att det finns tillgänglig kapacitet på porten. Nedgradera bandbredden för en krets stöds inte. 
* Ändra avläsning planen från förbrukade Data till obegränsad. Om du ändrar avläsning planen från obegränsad till förbrukade Data stöds inte.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Mer information om gränser och begränsningar finns i [ExpressRoute vanliga frågor och svar](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Så här aktiverar du ExpressRoute premium-tillägg
Du kan aktivera ExpressRoute premium-tillägg för en befintlig krets med hjälp av följande PowerShell-fragment:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Kretsen har nu tilläggsfunktioner för ExpressRoute premium aktiverat. Vi börjar fakturering för premium-tillägg-funktionen när kommandot har körts.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Att inaktivera tillägget ExpressRoute premium
> [!IMPORTANT]
> Om du använder resurser som är större än vad som tillåts för standard-kretsen kan den här åtgärden misslyckas.
> 
> 

Observera följande information:

* Innan du Nedgradera från premium-standarden, måste du kontrollera att antalet virtuella nätverk som är länkade till kretsen är mindre än 10. Om du inte din begäran om uppdatering misslyckas och vi debitera dig till Premier.
* Du måste ta bort länken alla virtuella nätverk i andra geopolitiska regioner. Om du inte gör det, uppdateringsbegäran misslyckas och vi debitera dig till Premier.
* Din routningstabellen måste vara mindre än 4 000 vägar för privat peering. Om din väg tabell är större än 4 000 vägar, släpper BGP-sessionen och reenabled inte tills antalet annonserade prefix hamnar under 4 000.

Du kan inaktivera ExpressRoute premium-tillägg för befintlig krets med hjälp av följande PowerShell-cmdlet:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Uppdatera bandbredd för ExpressRoute-krets
Stöds bandbredd alternativ för din leverantör, kontrollera den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md). Du kan välja valfri storlek som är större än storleken på en befintlig krets.

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-kretsen om det finns för lite kapacitet på befintliga porten. Du kan inte uppgradera kretsen om det finns inga ytterligare kapacitet på den platsen.
>
> Du kan minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradera bandbredd måste du ta bort etableringen av ExpressRoute-kretsen och etablera en ny ExpressRoute-krets.
> 

När du har bestämt vilken storlek du använder du följande kommando för att ändra storlek på kretsen:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Kretsen ska storleksändras på Microsoft-sida. Sedan måste du kontakta anslutningsleverantören om du vill uppdatera konfigurationer på sidan så att den matchar den här ändringen. När du har gjort det här meddelandet börjar vi fakturering du för alternativet uppdaterade bandbredd.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Om du vill flytta SKU: N från förbrukning till obegränsad
Du kan ändra en ExpressRoute-krets SKU med hjälp av följande PowerShell-fragment:

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Styra åtkomsten till klassiskt och Resource Manager-miljöer
Granska instruktionerna i [flytta ExpressRoute-kretsar från klassiskt till Resource Manager-distributionsmodellen](expressroute-howto-move-arm.md).  

## <a name="delete"></a>Borttagning och tar bort en ExpressRoute-krets
Observera följande information:

* Du måste ta bort länken alla virtuella nätverk från ExpressRoute-kretsen. Kontrollera om något virtuellt nätverk är kopplade till kretsen om åtgärden misslyckas.
* Om leverantören för ExpressRoute-kretsen Etableringsläge är **etablering** eller **etablerad** du måste arbeta med tjänstleverantören för att ta bort etableringen krets på sidan. Vi kan fortsätta att reservera resurser och debitera dig tills tjänstleverantör har slutförts avetablering kretsen och meddela oss.
* Om tjänstleverantör har avetableras kretsen (Etableringsstatus tjänstleverantör har angetts till **inte etablerats**), kan du ta bort kretsen. Detta stoppar faktureringen för kretsen.

Du kan ta bort ExpressRoute-krets genom att köra följande kommando:

```powershell
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Nästa steg

När du har skapat kretsen gör att du följande steg:

* [Skapa och ändra routning för ExpressRoute-krets](expressroute-howto-routing-arm.md)
* [Länka ditt virtuella nätverk till ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
