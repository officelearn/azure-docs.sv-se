---
title: 'Azure-ExpressRoute: ändra en krets: PowerShell: klassisk'
description: Den här artikeln vägleder dig igenom stegen för att kontrollera status, uppdatera eller ta bort och Avetablerar ExpressRoute-kretsen klassiska modellen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: e421a534b04f74d2a2eb0bc06aeffa52684ae17a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931968"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Ändra en ExpressRoute-krets med PowerShell (klassisk)

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln vägleder dig igenom stegen för att kontrollera status, uppdatera eller ta bort och Avetablerar ExpressRoute-kretsen klassiska modellen. Den här artikeln gäller den klassiska distributionsmodellen.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om distributionsmodeller för Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Hämta status för en krets

Du kan hämta den här informationen när som helst med hjälp av den `Get-AzureCircuit` cmdlet. Att göra anropet utan några parametrar visas alla kretsar.

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

Du kan få information om en viss ExpressRoute-krets genom att skicka tjänstnyckeln som en parameter i anropet.

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

Du kan utföra följande uppgifter utan avbrott:

* Aktivera eller inaktivera en ExpressRoute-premiumtillägget för ExpressRoute-krets.
* Öka bandbredden för ExpressRoute-kretsen under förutsättning att det finns tillgänglig kapacitet på porten. Nedgradera bandbredden för en krets stöds inte.
* Ändra Avläsning av planen från förbrukade Data till obegränsade Data. Prisplanen ändras Avläsning av programvara från obegränsade Data för förbrukade Data stöds inte.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Referera till den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md) mer information om gränser och begränsningar.

### <a name="enable-the-expressroute-premium-add-on"></a>Aktivera Expressroutes premiumtillägg

Du kan aktivera Expressroutes premiumtillägg för din befintliga krets med hjälp av följande PowerShell-cmdlet:

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

Kretsen har nu de ExpressRoute premium-tillägg-funktioner som aktiveras. När kommandot har körts utan problem, börjar faktureringen för funktionen för premium-tillägg.

### <a name="disable-the-expressroute-premium-add-on"></a>Inaktivera Expressroutes premiumtillägg

> [!IMPORTANT]
> Den här åtgärden kan misslyckas om du använder resurser som är större än vad som tillåts för standard-krets.
>
>

#### <a name="considerations"></a>Överväganden

* Se till att antalet virtuella nätverk som är länkade till kretsen är mindre än 10 innan du börjar Nedgradera från premium till standard. Om du inte gör det, din begäran om uppdatering misslyckas och du debiteras priserna för premium.
* Du måste ta bort länken alla virtuella nätverk i andra geopolitiska regioner. Om du inte din begäran om uppdatering misslyckas och du debiteras priserna för premium.
* Din routningstabell måste vara mindre än 4 000 vägar för privat peering. Om din väg tabell är större än 4 000 vägar, släpper BGP-sessionen och reenabled inte tills antalet annonserade prefix sjunker till under 4 000.

#### <a name="to-disable-the-premium-add-on"></a>Inaktivera premium-tillägget

Du kan inaktivera Expressroutes premiumtillägg för din befintliga krets med hjälp av följande PowerShell-cmdlet:

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

### <a name="update-the-expressroute-circuit-bandwidth"></a>Uppdatera ExpressRoute-kretsens bandbredd

Kontrollera den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md) för bandbreddsalternativ för din leverantör som stöds. Du kan välja valfri storlek som är större än storleken på din befintliga krets så länge som gör att den fysiska porten (där din krets skapas).

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-krets om det finns lite kapacitet på befintliga porten. Du kan inte uppgradera kretsen om det finns inga ytterligare kapacitet på den platsen.
>
> Du kan inte minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradera bandbredd måste du ta bort etableringen av ExpressRoute-kretsen och etablera om en ny ExpressRoute-krets.
>
>

#### <a name="resize-a-circuit"></a>Ändra storlek på en krets

Du kan använda följande kommando för att ändra storlek på din krets när du har bestämt vilken storlek som du behöver:

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

När kretsen har tagits anpassat på Microsoft-sida kan kontakta du din anslutningsleverantör om du vill uppdatera konfigurationer på sin sida för att matcha den här ändringen. Faktureringen börjar för alternativet uppdaterade bandbredd från och med nu på.

Om du ser följande fel om du vill öka kretsens bandbredd, betyder det lämnas utan tillräckligt mycket bandbredd på den fysiska porten där din befintliga krets skapas. Du måste ta bort den här kretsen och skapa en ny krets av samma storlek som du behöver.

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

* Du måste ta bort länken alla virtuella nätverk från ExpressRoute-krets för att åtgärden ska lyckas. Kontrollera om du har några virtuella nätverk som är länkade till kretsen om åtgärden misslyckas.
* Om leverantören för ExpressRoute-krets Etableringsstatus är **etablering** eller **etablerad** måste du samarbeta med din tjänstleverantör för att avetablera kretsen på sidan. Vi fortsätter att reservera resurser och debitera dig tills tjänstleverantören har slutförts avetablera kretsen och meddelar oss.
* Om tjänsteleverantören har tagit bort etableringen kretsen (tjänstleverantören Etableringsstatus är inställd på **inte etablerats**), du kan sedan ta bort kretsen. Därmed avbryts faktureringen för kretsen.

#### <a name="delete-a-circuit"></a>Ta bort en krets

Du kan ta bort ExpressRoute-kretsen genom att köra följande kommando:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
