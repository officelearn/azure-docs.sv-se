---
title: 'Azure ExpressRoute: Ändra en krets: PowerShell'
description: Skapa, etablera, verifiera, uppdatera, ta bort och avetablera en ExpressRoute-krets.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/08/2020
ms.author: cherylmc
ms.openlocfilehash: ab44d5358154c17622eef68205ac2326e81377a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770976"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Skapa och ändra en ExpressRoute-krets med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln hjälper dig att skapa en ExpressRoute-krets med PowerShell-cmdlets och Azure Resource Manager-distributionsmodellen. Du kan också kontrollera status, uppdatera, ta bort eller avetablera en krets.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar bör du granska [förutsättningarna](expressroute-prerequisites.md) och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Skapa och etablera en ExpressRoute-krets
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Logga in på ditt Azure-konto och välj din prenumeration

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Få en lista över leverantörer, platser och bandbredder som stöds
Innan du skapar en ExpressRoute-krets behöver du listan över anslutningsleverantörer, platser och bandbreddsalternativ som stöds.

PowerShell cmdlet **Get-AzExpressRouteServiceProvider** returnerar den här informationen, som du ska använda i senare steg:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Kontrollera om anslutningsleverantören finns med där. Anteckna följande information, som du behöver senare när du skapar en krets:

* Namn
* PeeringLocations
* Bandbredder Som erbjuds

Du är nu redo att skapa en ExpressRoute-krets.

### <a name="3-create-an-expressroute-circuit"></a>3. Skapa en ExpressRoute-krets
Om du inte redan har en resursgrupp måste du skapa en innan du skapar expressroutekretsen. Du kan göra det genom att köra följande kommando:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

Följande exempel visar hur du skapar en 200 Mbps ExpressRoute-krets via Equinix i Silicon Valley. Om du använder en annan leverantör och olika inställningar ersätter du den informationen när du gör din begäran. Använd följande exempel för att begära en ny tjänstnyckel:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Se till att du anger rätt SKU-nivå och SKU-familj:

* SKU-nivån avgör om en ExpressRoute-krets är [Lokal,](expressroute-faqs.md#expressroute-local)Standard eller [Premium](expressroute-faqs.md#expressroute-premium). Du kan ange *Lokal,* *Standard* eller *Premium*.
* SKU-familjen bestämmer faktureringstypen. Du kan ange *Datadata för datapriser* för ett dataabonnemang med datapriser och *Unlimiteddata* för ett obegränsat dataabonnemang. Du kan ändra *faktureringstypen* från Datadata till *Unlimiteddata,* men du kan inte ändra typen från *Unlimiteddata* till *Datapriser*. En *lokal* krets är alltid *Unlimiteddata*.

> [!IMPORTANT]
> Din ExpressRoute-krets faktureras från det att en servicenyckel utfärdas. Se till att du utför den här åtgärden när anslutningsleverantören är redo att etablera kretsen.
>
>

Svaret innehåller servicenyckeln. Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. Lista alla ExpressRoute-kretsar
Om du vill få en lista över alla ExpressRoute-kretsar som du har skapat kör du kommandot **Get-AzExpressRouteCircuit:**

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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

Du kan hämta den här informationen `Get-AzExpressRouteCircuit` när som helst med hjälp av cmdleten. Om du ringer samtalet utan parametrar visas alla kretsar. Din tjänstnyckel visas i fältet *ServiceKey:*

```azurepowershell-interactive
Get-AzExpressRouteCircuit
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


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. Skicka tjänstnyckeln till din anslutningsleverantör för etablering
*ServiceProviderProvisioningState* innehåller information om det aktuella läget för etablering på tjänsteleverantörens sida. Status ger tillståndet på Microsoft-sidan. Mer information om kretsetableringstillstånd finns i [Arbetsflöden](expressroute-workflows.md#expressroute-circuit-provisioning-states).

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Kretsen ändras till följande tillstånd när anslutningsleverantören är i färd med att aktivera det åt dig:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

För att du ska kunna använda en ExpressRoute-krets måste den vara i följande tillstånd:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. Kontrollera regelbundet kretsnyckelns status och tillstånd
Om du kontrollerar kretsnyckelns status och tillstånd får du veta när din leverantör har aktiverat din krets. När kretsen har konfigurerats visas *ServiceProviderProvisioningState* som *Etablerat*, vilket visas i följande exempel:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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
Steg-för-steg-instruktioner finns i konfigurationsartikeln för Routning av [ExpressRoute-kretsroutning](expressroute-howto-routing-arm.md) för att skapa och ändra krets peerings.

> [!IMPORTANT]
> Dessa instruktioner gäller endast för kretsar som skapas med tjänsteleverantörer som erbjuder anslutningstjänster för lager 2. Om du använder en tjänsteleverantör som erbjuder hanterade layer 3-tjänster (vanligtvis en IP VPN, till exempel MPLS), konfigurerar och hanterar anslutningsleverantören routning åt dig.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. Länka ett virtuellt nätverk till en ExpressRoute-krets
Länka sedan ett virtuellt nätverk till expressroutekretsen. Använd artikeln [Länka virtuella nätverk till ExpressRoute-kretsar](expressroute-howto-linkvnet-arm.md) när du arbetar med resurshanterarens distributionsmodell.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Hämta status för en ExpressRoute-krets
Du kan när som helst hämta den här informationen med hjälp av cmdleten **Get-AzExpressRouteCircuit.** Om du ringer samtalet utan parametrar visas alla kretsar.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
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


Du kan få information om en specifik ExpressRoute-krets genom att skicka resursgruppens namn och kretsnamn som en parameter till anropet:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
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
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Ändra en ExpressRoute-krets
Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen.

Du kan utföra följande uppgifter utan driftstopp:

* Aktivera eller inaktivera ett ExpressRoute premium-tillägg för din ExpressRoute-krets.
* Öka bandbredden på din ExpressRoute-krets förutsatt att det finns kapacitet tillgänglig på porten. Det går inte att nedgradera bandbredden för en krets.
* Ändra mätplanen från datapriser till obegränsad data. Det går inte att ändra avläsningsplanen från obegränsad data till datapriser.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Mer information om begränsningar och begränsningar finns i Vanliga frågor och svar om [ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Så här aktiverar du Premium-tillägget ExpressRoute
Du kan aktivera Premium-tillägget ExpressRoute för din befintliga krets med hjälp av följande PowerShell-kodavsnitt:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Kretsen har nu ExpressRoute premium tilläggsfunktioner aktiverade. Vi börjar fakturera dig för premiumtilläggsfunktionen så snart kommandot har körts.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Så här inaktiverar du Premium-tillägget ExpressRoute
> [!IMPORTANT]
> Om du använder resurser som är större än vad som är tillåtet för standardkretsen kan den här åtgärden misslyckas.
>
>

Notera följande information:

* Innan du nedgraderar från premium till standard måste du se till att antalet virtuella nätverk som är länkade till kretsen är mindre än 10. Om du inte gör det misslyckas din uppdateringsbegäran och vi fakturerar dig till högre priser.
* Du måste ta bort länken till alla virtuella nätverk i andra geopolitiska regioner. Om du inte gör det misslyckas din uppdateringsbegäran och vi fakturerar dig till högre priser.
* Din rutttabell måste vara mindre än 4 000 vägar för privat peering. Om flödestabellstorleken är större än 4 000 rutter sjunker BGP-sessionen och kan inte ändras igen förrän antalet annonserade prefix går under 4 000.

Du kan inaktivera Premium-tillägget ExpressRoute för den befintliga kretsen med hjälp av följande PowerShell-cmdlet:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Så här uppdaterar du ExpressRoute-kretsbandbredden
Om du vill ha bandbreddsalternativ som stöds för din leverantör kontrollerar du [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md). Du kan välja vilken storlek som helst som är större än storleken på din befintliga krets.

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-kretsen om det finns otillräcklig kapacitet på den befintliga porten. Du kan inte uppgradera kretsen om det inte finns någon ytterligare kapacitet tillgänglig på den platsen.
>
> Du kan inte minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradering bandbredd kräver att du avetablerar ExpressRoute-kretsen och sedan återetablerar en ny ExpressRoute-krets.
>

När du har bestämt vilken storlek du behöver använder du följande kommando för att ändra storlek på kretsen:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Din krets kommer att dimensioneras upp på Microsoft-sidan. Då måste du kontakta din anslutningsleverantör för att uppdatera konfigurationer på deras sida för att matcha den här ändringen. När du har gjort det här meddelandet börjar vi fakturera dig för det uppdaterade bandbreddsalternativet.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Så här flyttar du SKU:n från uppmätt till obegränsad
Du kan ändra SKU för en ExpressRoute-krets med hjälp av följande PowerShell-kodavsnitt:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Så här styr du åtkomsten till de klassiska miljöerna och Resurshanteraren
Granska instruktionerna i [Move ExpressRoute-kretsar från klassikern till Resurshanterarens distributionsmodell](expressroute-howto-move-arm.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Avetablera och ta bort en ExpressRoute-krets
Notera följande information:

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen. Om den här åtgärden misslyckas kontrollerar du om några virtuella nätverk är länkade till kretsen.
* Om tillståndet för tillhandahållare av ExpressRoute-kretstjänstleverantören **etablerar** eller **etableras** måste du samarbeta med din tjänsteleverantör för att avetablera kretsen på deras sida. Vi fortsätter att reservera resurser och fakturera dig tills tjänsteleverantören slutför avetablering av kretsen och meddelar oss.
* Om tjänsteleverantören har avetablerat kretsen (etableringstillståndet för tjänsteleverantören är inställt på **Inte etablerat)** kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.

Du kan ta bort expressroutekretsen genom att köra följande kommando:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Nästa steg

När du har skapat kretsen kontrollerar du att du gör följande steg:

* [Skapa och ändra routning för din ExpressRoute-krets](expressroute-howto-routing-arm.md)
* [Länka ditt virtuella nätverk till din ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
