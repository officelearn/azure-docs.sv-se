---
title: 'Azure-ExpressRoute: ändra en krets: PowerShell'
description: Skapa, etablera, verifiera, uppdatera, ta bort och avetablera en ExpressRoute-krets.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/08/2020
ms.author: duau
ms.openlocfilehash: e9bf9dbe0f4146101513ab9786b298ac6b43b6a3
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566305"
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell"></a>Skapa och ändra en ExpressRoute-krets med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln hjälper dig att skapa en ExpressRoute-krets med PowerShell-cmdletar och Azure Resource Manager distributions modell. Du kan också kontrol lera status, uppdatera, ta bort eller avetablera en krets.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar bör du gå igenom [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Skapa och etablera en ExpressRoute-krets
### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Logga in på ditt Azure-konto och välj din prenumeration

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. Hämta listan över providers, platser och bandbredder som stöds
Innan du skapar en ExpressRoute-krets behöver du en lista över anslutnings leverantörer, platser och bandbredds alternativ som stöds.

PowerShell-cmdleten **Get-AzExpressRouteServiceProvider** returnerar den här informationen, som du kommer att använda i senare steg:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Kontrol lera om din anslutnings leverantör visas där. Anteckna följande information som du behöver senare när du skapar en krets:

* Name
* PeeringLocations
* BandwidthsOffered

Nu är du redo att skapa en ExpressRoute-krets.

### <a name="3-create-an-expressroute-circuit"></a>3. skapa en ExpressRoute-krets
Om du inte redan har en resurs grupp måste du skapa en innan du skapar din ExpressRoute-krets. Du kan göra detta genom att köra följande kommando:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

I följande exempel visas hur du skapar en 200-Mbit/s ExpressRoute-krets via Equinix i Silicon dal. Om du använder en annan provider och olika inställningar ersätter du den informationen när du gör din begäran. Använd följande exempel för att begära en ny tjänst nyckel:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Se till att du anger rätt SKU-nivå och SKU-serien:

* SKU-nivån avgör om en ExpressRoute-krets är [lokal](expressroute-faqs.md#expressroute-local), standard eller [Premium](expressroute-faqs.md#expressroute-premium). Du kan ange *lokal*, *standard* eller *Premium*. Du kan inte ändra SKU: n från *Standard/Premium* till *lokal*.
* SKU-serien bestämmer fakturerings typen. Du kan ange *Metereddata* för ett data abonnemang med datapriser och *Unlimiteddata* för ett obegränsat data abonnemang. Du kan ändra fakturerings typen från *Metereddata* till *Unlimiteddata*, men du kan inte ändra typen från *Unlimiteddata* till *Metereddata*. En *lokal* krets är alltid *Unlimiteddata*.

> [!IMPORTANT]
> Din ExpressRoute-krets debiteras från den tidpunkt då en tjänst nyckel utfärdas. Se till att du utför den här åtgärden när anslutnings leverantören är redo att etablera kretsen.
>
>

Svaret innehåller tjänst nyckeln. Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="4-list-all-expressroute-circuits"></a>4. lista alla ExpressRoute-kretsar
Om du vill hämta en lista över alla ExpressRoute-kretsar som du har skapat kör du kommandot **Get-AzExpressRouteCircuit** :

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Svaret ser ut ungefär som i följande exempel:

```azurepowershell
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
```

Du kan hämta den här informationen när som helst genom att använda `Get-AzExpressRouteCircuit` cmdleten. Om du gör anropet utan parametrar visas alla kretsar. Din tjänst nyckel visas i fältet *serviceKey* :

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


Svaret ser ut ungefär som i följande exempel:

```azurepowershell
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
```


### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. skicka tjänst nyckeln till din anslutnings leverantör för etablering
*ServiceProviderProvisioningState* innehåller information om det aktuella etablerings läget på tjänstens leverantörs sida. Status ger tillståndet på Microsoft-sidan. Mer information om krets etablerings tillstånd finns i [arbets flöden](expressroute-workflows.md#expressroute-circuit-provisioning-states).

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```



-Kretsen ändras till följande tillstånd när anslutnings leverantören håller på att aktivera den åt dig:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

För att du ska kunna använda en ExpressRoute-krets måste den vara i följande tillstånd:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. kontrol lera regelbundet status och tillståndet för krets nyckeln
Genom att kontrol lera status och tillståndet för krets nyckeln kan du se när din provider har aktiverat din krets. När kretsen har kon figurer ATS visas *ServiceProviderProvisioningState* som *etablerad*, som du ser i följande exempel:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Svaret ser ut ungefär som i följande exempel:

```azurepowershell
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
```

### <a name="7-create-your-routing-configuration"></a>7. skapa konfigurationen för routning
Stegvisa instruktioner finns i [ExpressRoute-kretsens konfigurations](expressroute-howto-routing-arm.md) artikel för att skapa och ändra krets-peering.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapats med tjänst leverantörer som erbjuder Layer 2-anslutningstjänster. Om du använder en tjänst leverantör som erbjuder hanterade Layer 3-tjänster (vanligt vis ett IP-VPN, som MPLS) konfigurerar och hanterar din anslutnings leverantör routning åt dig.
>
>

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. länka ett virtuellt nätverk till en ExpressRoute-krets
Länka sedan ett virtuellt nätverk till din ExpressRoute-krets. Använd artikeln [Länka virtuella nätverk till ExpressRoute-kretsar](expressroute-howto-linkvnet-arm.md) när du arbetar med distributions modellen för Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Hämta status för en ExpressRoute-krets
Du kan hämta den här informationen när som helst med hjälp av cmdleten **Get-AzExpressRouteCircuit** . Om du gör anropet utan parametrar visas alla kretsar.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```


Svaret liknar följande exempel:

```azurepowershell
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
```


Du kan hämta information om en speciell ExpressRoute-krets genom att skicka resurs gruppens namn och krets namnet som en parameter till anropet:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```


Svaret ser ut ungefär som i följande exempel:

```azurepowershell
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
```


Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande kommando:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Ändra en ExpressRoute-krets
Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen.

Du kan utföra följande uppgifter utan avbrott:

* Aktivera eller inaktivera ett ExpressRoute Premium-tillägg för din ExpressRoute-krets. Det finns inte stöd för att ändra SKU: n från *Standard/Premium* till *lokal* .
* Öka bandbredden för din ExpressRoute-krets om det finns tillgänglig kapacitet på porten. Nedgradering av en kretss bandbredd stöds inte.
* Ändra Mät planen från datapriser till obegränsade data. Det finns inte stöd för att ändra Mät planen från obegränsade data till datapriser.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Mer information om begränsningar och begränsningar finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aktivera ExpressRoute Premium-tillägget
Du kan aktivera ExpressRoute Premium-tillägget för din befintliga krets med hjälp av följande PowerShell-kodfragment:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Kretsen har nu ExpressRoute Premium-tillägg aktiverade. Vi börjar med att debitera dig för Premium-tilläggs kapaciteten så snart kommandot har körts.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Så här inaktiverar du ExpressRoute Premium-tillägget
> [!IMPORTANT]
> Den här åtgärden kan inte utföras om du använder resurser som är större än vad som tillåts för standard kretsen.
>
>

Notera följande information:

* Innan du nedgraderar från Premium till standard måste du se till att antalet virtuella nätverk som är länkade till kretsen är mindre än 10. Om du inte gör det Miss lyckas din uppdateringsbegäran och vi debiteras till Premium priser.
* Du måste ta bort länken mellan alla virtuella nätverk i andra politiska regioner. Om du inte gör det Miss lyckas din uppdateringsbegäran och vi debiteras till Premium-priser.
* Routningstabellen måste vara mindre än 4 000 vägar för privat peering. Om din väg tabell storlek är större än 4 000 vägar släpps BGP-sessionen och aktive ras inte igen förrän antalet annonserade prefix går under 4 000.

Du kan inaktivera ExpressRoute Premium-tillägget för den befintliga kretsen med hjälp av följande PowerShell-cmdlet:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Så här uppdaterar du ExpressRoute-kretsens bandbredd
För de bandbredds alternativ som stöds för din Provider, se [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md). Du kan välja valfri storlek som är större än den befintliga kretsens storlek.

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-kretsen om det inte finns tillräckligt med kapacitet på den befintliga porten. Du kan inte uppgradera kretsen om det inte finns någon ytterligare kapacitet tillgänglig på den platsen.
>
> Du kan inte minska bandbredden för en ExpressRoute-krets utan avbrott. Med degradering av bandbredd måste du avetablera ExpressRoute-kretsen och sedan etablera om en ny ExpressRoute-krets.
>

När du har bestämt vilken storlek du behöver använder du följande kommando för att ändra storlek på kretsen:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```


Din krets är i storlek på Microsoft-sidan. Sedan måste du kontakta din anslutnings leverantör för att uppdatera konfigurationerna på sidan för att matcha den här ändringen. När du har gjort det här meddelandet kommer vi att börja fakturera dig för alternativet uppdaterad bandbredd.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Flytta SKU: n från avgiftsbelagd till obegränsad
Du kan ändra SKU för en ExpressRoute-krets med hjälp av följande PowerShell-kodfragment:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>Så här kontrollerar du åtkomsten till de klassiska och Resource Manager-miljöerna
Läs anvisningarna i [Flytta ExpressRoute-kretsar från den klassiska distributions modellen till Resource Manager](expressroute-howto-move-arm.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Avetablera och ta bort en ExpressRoute-krets
Notera följande information:

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen. Om den här åtgärden Miss lyckas kontrollerar du om det finns några virtuella nätverk som är länkade till kretsen.
* Om etablerings statusen för ExpressRoute-kretsen **etableras** eller **etableras** måste du arbeta med tjänst leverantören för att avetablera kretsen på sin sida. Vi fortsätter att reservera resurser och fakturera dig tills tjänste leverantören har slutfört avetableringen av kretsen och meddelar oss oss.
* Om tjänst leverantören har avetablerat kretsen (etablerings statusen för tjänst leverantören är inställd på **inte etablerad**) kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.

Du kan ta bort ExpressRoute-kretsen genom att köra följande kommando:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Nästa steg

När du har skapat din krets, se till att du utför följande steg:

* [Skapa och ändra routning för din ExpressRoute-krets](expressroute-howto-routing-arm.md)
* [Länka ditt virtuella nätverk till din ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
