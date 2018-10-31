---
title: 'Skapa och ändra en ExpressRoute-krets: PowerShell: Azure Resource Manager | Microsoft Docs'
description: Den här artikeln beskriver hur du skapar, etablera, verifiera, uppdatera, ta bort och Avetablerar en ExpressRoute-krets.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 10/18/2018
ms.author: ganesr;cherylmc
ms.openlocfilehash: e8af299967b3f7d8010004cc60058733d7c80163
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249330"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Skapa och ändra en ExpressRoute-krets med PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln beskriver hur du skapar en Azure ExpressRoute-krets med hjälp av PowerShell-cmdletar och Azure Resource Manager-distributionsmodellen. Den här artikeln beskriver också hur kontrollera status för kretsen, uppdatera, eller ta bort och ta bort etableringen av den.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar bör du granska den [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create"></a>Skapa och etablera en ExpressRoute-krets
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Logga in på ditt Azure-konto och välj din prenumeration
Du börjar din konfiguration genom att logga in på ditt Azure-konto. Använd följande exempel för att ansluta:

Om du använder Azure CloudShell, behöver du inte köra Connect-AzureRmAccount, som du ska ansluta automatiskt.

```azurepowershell
Connect-AzureRmAccount
```

Kontrollera prenumerationerna för kontot:

```azurepowershell-interactive
Get-AzureRmSubscription
```

Välj den prenumeration som du vill skapa en ExpressRoute-krets för:

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
```

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Hämta listan över providers som stöds, platser och bandbredder
Innan du skapar en ExpressRoute-krets, behöver du lista över anslutningsleverantörer som stöds, platser och bandbreddsalternativ för.

PowerShell-cmdleten **Get-AzureRmExpressRouteServiceProvider** returnerar den här informationen som du ska använda i senare steg:

```azurepowershell-interactive
Get-AzureRmExpressRouteServiceProvider
```

Kontrollera om din anslutningsleverantör finns med i listan. Anteckna följande information, som du behöver senare när du skapar en krets:

* Namn
* PeeringLocations
* BandwidthsOffered

Du är nu redo att skapa en ExpressRoute-krets.   

### <a name="3-create-an-expressroute-circuit"></a>3. Skapa en ExpressRoute-krets
Om du inte redan har en resursgrupp, måste du skapa en innan du skapar din ExpressRoute-krets. Du kan göra det genom att köra följande kommando:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```


I följande exempel visar hur du skapar en 200 Mbit/s ExpressRoute-krets via Equinix i Silicon Valley. Om du använder en annan leverantör och olika inställningar, ersätter du den informationen när du gör din begäran. Använd följande exempel för att begära en ny Tjänstnyckel:

```azurepowershell-interactive
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Kontrollera att du anger rätt SKU-nivån och SKU-serien:

* SKU-nivån avgör om en ExpressRoute-standard eller en ExpressRoute-premiumtillägget är aktiverat. Du kan ange *Standard* att hämta standard-SKU eller *Premium* premium-tillägg.
* SKU-familjen avgör vilken fakturering. Du kan ange *Metereddata* för ett abonnemang med förbrukade data och *Unlimiteddata* för en obegränsad dataplan. Du kan ändra typen fakturering från *Metereddata* till *Unlimiteddata*, men du kan inte ändra typen från *Unlimiteddata* till *Metereddata*.

> [!IMPORTANT]
> ExpressRoute-kretsen debiteras från den tidpunkt då en Tjänstnyckel utfärdas. Se till att du utför den här åtgärden när anslutningsleverantören är redo att lägga till kretsen.
> 
> 

Svaret innehåller tjänstnyckeln. Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```azurepowershell-interactive
get-help New-AzureRmExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Lista över alla ExpressRoute-kretsar
Om du vill hämta en lista över alla ExpressRoute-kretsar som du skapade, kör den **Get-AzureRmExpressRouteCircuit** kommando:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

Du kan hämta den här informationen när som helst med hjälp av den `Get-AzureRmExpressRouteCircuit` cmdlet. Att göra anrop utan parametrar visar en lista över alla kretsar. Din nyckel för tjänstens visas i den *ServiceKey* fält:

```azurepowershell-interactive
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
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                          }
    ServiceKey                       : **************************************
    Peerings                         : []


Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```azurepowershell-interactive
get-help Get-AzureRmExpressRouteCircuit -detailed
```

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Skicka tjänstnyckeln till din anslutningsleverantör för etablering
*Korsanslutningens* innehåller information om det aktuella tillståndet för etablering på tjänstleverantör sida. Status innehåller tillståndet på Microsoft-sida. Mer information om krets Etableringsstatus finns [arbetsflöden](expressroute-workflows.md#expressroute-circuit-provisioning-states).

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Kretsen ändras till följande tillstånd när anslutningsleverantören håller på att aktivera den för du:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Du kan använda en ExpressRoute-krets, måste den vara i följande tillstånd:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Regelbundet kontrollera status och tillståndet för nyckeln krets
Kontrollera status och tillståndet för krets nyckeln får du reda på när din provider har aktiverat din krets. När kretsen har konfigurerats, *Korsanslutningens* visas som *etablerad*, enligt följande exempel:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

### <a name="7-create-your-routing-configuration"></a>7. Skapa din routningskonfiguration
Stegvisa instruktioner finns i den [ExpressRoute-krets routningskonfiguration](expressroute-howto-routing-arm.md) artikeln om du vill skapa och ändra krets peerings.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapas med tjänstleverantörer som erbjuder tjänster för layer-2-anslutning. Om du använder en leverantör av tjänster som erbjuder Hanterade layer 3-tjänster (vanligtvis en IP VPN, t.ex. MPLS), anslutningsleverantören konfigurerar och hanterar routning åt dig.
> 
> 

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Länka ett virtuellt nätverk till en ExpressRoute-krets
Därefter länka ett virtuellt nätverk till ExpressRoute-kretsen. Använd den [länka virtuella nätverk till ExpressRoute-kretsar](expressroute-howto-linkvnet-arm.md) artikel när du arbetar med Resource Manager-distributionsmodellen.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Hämta status för en ExpressRoute-krets
Du kan hämta den här informationen när som helst med hjälp av den **Get-AzureRmExpressRouteCircuit** cmdlet. Att göra anrop utan parametrar visar en lista över alla kretsar.

```azurepowershell-interactive
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


Du kan få information om en viss ExpressRoute-krets genom att ange resursgruppens namn och kretsnamn som en parameter i anropet:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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


Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```azurepowershell-interactive
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify"></a>Ändra en ExpressRoute-krets
Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen.

Du kan utföra följande uppgifter utan avbrott:

* Aktivera eller inaktivera en ExpressRoute-premiumtillägget för ExpressRoute-krets.
* Öka bandbredden för ExpressRoute-kretsen under förutsättning att det finns tillgänglig kapacitet på porten. Nedgradera bandbredden för en krets stöds inte. 
* Ändra Avläsning av planen från förbrukade Data till obegränsade Data. Prisplanen ändras Avläsning av programvara från obegränsade Data för förbrukade Data stöds inte.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Mer information om gränser och begränsningar finns i den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aktivera Expressroutes premiumtillägg
Du kan aktivera Expressroutes premiumtillägg för din befintliga krets med hjälp av följande PowerShell-kodavsnitt:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Kretsen har aktiverat ExpressRoute premium-tilläggsfunktioner. Vi börjar fakturering för premium-tillägg-funktionen när kommandot har körts.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Inaktivera Expressroutes premiumtillägg
> [!IMPORTANT]
> Om du använder resurser som är större än vad som tillåts för standard-krets, kan den här åtgärden misslyckas.
> 
> 

Notera följande information:

* Innan du börjar Nedgradera från premium till standard måste du kontrollera att antalet virtuella nätverk som är länkade till kretsen är mindre än 10. Om du inte din begäran om uppdatering misslyckas och vi fakturerar dig enligt priserna för premium.
* Du måste ta bort länken alla virtuella nätverk i andra geopolitiska regioner. Om du inte gör detta kommer din begäran om uppdatering misslyckas och vi fakturerar dig enligt priserna för premium.
* Din routningstabell måste vara mindre än 4 000 vägar för privat peering. Om din väg tabell är större än 4 000 vägar, släpper BGP-sessionen och reenabled inte tills antalet annonserade prefix sjunker till under 4 000.

Du kan inaktivera ExpressRoute premium-tillägget för befintliga kretsen med hjälp av följande PowerShell-cmdlet:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Uppdatera ExpressRoute-kretsens bandbredd
Bandbreddsalternativ som stöds för din leverantör, kontrollera den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md). Du kan välja valfri storlek som är större än storleken på din befintliga krets.

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-krets om det finns lite kapacitet på befintliga porten. Du kan inte uppgradera kretsen om det finns inga ytterligare kapacitet på den platsen.
>
> Du kan inte minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradera bandbredd måste du ta bort etableringen av ExpressRoute-kretsen och etablera om en ny ExpressRoute-krets.
> 

När du har bestämt vilken storlek som du behöver, använder du följande kommando för att ändra storlek på din krets:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Din krets ska anpassas på Microsoft-sida. Sedan måste du kontakta din anslutningsleverantör om du vill uppdatera konfigurationer på sin sida för att matcha den här ändringen. När du har gjort det här meddelandet börjar vi fakturering du för alternativet uppdaterade bandbredd.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Om du vill flytta SKU: N från förbrukning till obegränsad
Du kan ändra SKU: N för en ExpressRoute-krets med hjälp av följande PowerShell-kodavsnitt:

```azurepowershell-interactive
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Styra åtkomsten till klassiskt och Resource Manager-miljöer
Granska anvisningarna i [flytta ExpressRoute-kretsar från klassiskt till Resource Manager-distributionsmodellen](expressroute-howto-move-arm.md).  

## <a name="delete"></a>Avetablera och ta bort en ExpressRoute-krets
Notera följande information:

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen. Kontrollera om alla virtuella nätverken är länkade till kretsen om åtgärden misslyckas.
* Om leverantören för ExpressRoute-krets Etableringsstatus är **etablering** eller **etablerad** måste du samarbeta med din tjänstleverantör för att avetablera kretsen på sidan. Vi fortsätter att reservera resurser och debitera dig tills tjänstleverantören har slutförts avetablera kretsen och meddelar oss.
* Om tjänsteleverantören har tagit bort etableringen kretsen (tjänstleverantören Etableringsstatus är inställd på **inte etablerats**), kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.

Du kan ta bort ExpressRoute-kretsen genom att köra följande kommando:

```azurepowershell-interactive
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Nästa steg

När du har skapat din krets, se till att du gör följande steg:

* [Skapa och ändra routning för ExpressRoute-krets](expressroute-howto-routing-arm.md)
* [Länka ditt virtuella nätverk till ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
