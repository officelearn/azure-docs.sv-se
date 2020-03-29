---
title: 'Azure ExpressRoute: Ändra en krets: PowerShell:classic'
description: I den här artikeln får du hjälp med stegen för att kontrollera status, uppdatera eller ta bort och avetablera din ExpressRoute-klassiska distributionsmodellkrets.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: e421a534b04f74d2a2eb0bc06aeffa52684ae17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931968"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Ändra en ExpressRoute-krets med PowerShell (klassisk)

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

I den här artikeln får du hjälp med stegen för att kontrollera status, uppdatera eller ta bort och avetablera din ExpressRoute-klassiska distributionsmodellkrets. Den här artikeln gäller den klassiska distributionsmodellen.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om Azures distributionsmodeller**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Få status för en krets

Du kan hämta den här informationen `Get-AzureCircuit` när som helst med hjälp av cmdleten. Om du ringer samtalet utan några parametrar visas alla kretsar.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Du kan få information om en specifik ExpressRoute-krets genom att skicka servicenyckeln som en parameter till anropet.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Du kan få detaljerade beskrivningar av alla parametrar genom att köra följande exempel:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Ändra en krets

Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen.

Du kan utföra följande uppgifter utan driftstopp:

* Aktivera eller inaktivera ett ExpressRoute premium-tillägg för din ExpressRoute-krets.
* Öka bandbredden på din ExpressRoute-krets förutsatt att det finns kapacitet tillgänglig på porten. Det går inte att nedgradera bandbredden för en krets.
* Ändra mätplanen från datapriser till obegränsad data. Det går inte att ändra avläsningsplanen från obegränsad data till datapriser.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Se Vanliga frågor och svar om [ExpressRoute](expressroute-faqs.md) för mer information om begränsningar och begränsningar.

### <a name="enable-the-expressroute-premium-add-on"></a>Aktivera Premiumtillägget ExpressRoute

Du kan aktivera Premium-tillägget ExpressRoute för din befintliga krets med hjälp av följande PowerShell-cmdlet:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

Din krets kommer nu att ha ExpressRoute premium-tilläggsfunktionerna aktiverade. Så snart kommandot har körts börjar fakturering för premiumtilläggsfunktionen.

### <a name="disable-the-expressroute-premium-add-on"></a>Inaktivera premiumtillägget ExpressRoute

> [!IMPORTANT]
> Den här åtgärden kan misslyckas om du använder resurser som är större än vad som är tillåtet för standardkretsen.
>
>

#### <a name="considerations"></a>Överväganden

* Se till att antalet virtuella nätverk som är länkade till kretsen är mindre än 10 innan du nedgraderar från premium till standard. Om du inte gör det misslyckas din uppdateringsbegäran och du faktureras premiepriserna.
* Du måste ta bort länken till alla virtuella nätverk i andra geopolitiska regioner. Om du inte gör det misslyckas din uppdateringsbegäran och du debiteras premiumpriserna.
* Din rutttabell måste vara mindre än 4 000 vägar för privat peering. Om flödestabellstorleken är större än 4 000 rutter sjunker BGP-sessionen och kan inte ändras igen förrän antalet annonserade prefix går under 4 000.

#### <a name="to-disable-the-premium-add-on"></a>Så här inaktiverar du premiumtillägget

Du kan inaktivera Premium-tillägget ExpressRoute för din befintliga krets med hjälp av följande PowerShell-cmdlet:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Uppdatera ExpressRoute-kretsbandbredden

Kontrollera [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) om det finns bandbreddsalternativ som stöds för din leverantör. Du kan välja vilken storlek som helst som är större än storleken på din befintliga krets så länge den fysiska porten (som kretsen skapas på).

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-kretsen om det finns otillräcklig kapacitet på den befintliga porten. Du kan inte uppgradera kretsen om det inte finns någon ytterligare kapacitet tillgänglig på den platsen.
>
> Du kan inte minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradering bandbredd kräver att du avetablerar ExpressRoute-kretsen och sedan återetablerar en ny ExpressRoute-krets.
>
>

#### <a name="resize-a-circuit"></a>Ändra storlek på en krets

När du har bestämt vilken storlek du behöver kan du använda följande kommando för att ändra storlek på kretsen:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

När din krets har dimensionerats på Microsoft-sidan måste du kontakta anslutningsleverantören för att uppdatera konfigurationer på deras sida för att matcha den här ändringen. Faktureringen börjar för alternativet uppdaterad bandbredd från och med nu.

Om du ser följande fel när du ökar kretsbandbredden betyder det att det inte finns tillräcklig bandbredd kvar på den fysiska porten där din befintliga krets skapas. Du måste ta bort den här kretsen och skapa en ny krets av den storlek du behöver.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Avetablera och ta bort en krets

### <a name="considerations"></a>Överväganden

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen för att åtgärden ska lyckas. Kontrollera om du har några virtuella nätverk som är länkade till kretsen om den här åtgärden misslyckas.
* Om tillståndet för tillhandahållare av ExpressRoute-kretstjänstleverantören **etablerar** eller **etableras** måste du samarbeta med din tjänsteleverantör för att avetablera kretsen på deras sida. Vi fortsätter att reservera resurser och fakturera dig tills tjänsteleverantören slutför avetablering av kretsen och meddelar oss.
* Om tjänsteleverantören har avetablerat kretsen (etableringstillståndet för tjänsteleverantören är inställt på **Inte etablerat)** kan du sedan ta bort kretsen. Därmed avbryts faktureringen för kretsen.

#### <a name="delete-a-circuit"></a>Ta bort en krets

Du kan ta bort expressroutekretsen genom att köra följande kommando:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
