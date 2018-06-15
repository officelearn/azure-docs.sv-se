---
title: 'Ändra en ExpressRoute-krets: PowerShell: klassiska Azure-portalen | Microsoft Docs'
description: Den här artikeln vägleder dig genom stegen för att kontrollera status, uppdatera eller ta bort och ta bort etableringen ExpressRoute klassisk distribution modellen kretsen.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 457bb74fa15d31fecbf668038ac880cafb8a897d
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
ms.locfileid: "24102842"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Ändra en ExpressRoute-krets med hjälp av PowerShell (klassisk)

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln visar även hur att kontrollera status, uppdatera eller ta bort och ta bort etableringen av en ExpressRoute-krets.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om distributionsmodeller för Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Innan du börjar

Installera de senaste versionerna av Azure Service Management (SM) PowerShell-moduler följer du anvisningarna i [komma igång med Azure PowerShell-cmdlets](/powershell/azure/overview) stegvisa instruktioner om hur du konfigurerar datorn att använda den Azure PowerShell-moduler.

## <a name="get-the-status-of-a-circuit"></a>Hämta status för en krets

Du kan hämta den här informationen när som helst genom att använda den `Get-AzureCircuit` cmdlet. Att göra anropet utan några parametrar visas alla kretsar.

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

Du kan få information om en specifik ExpressRoute-krets genom att överföra nyckeln för tjänsten som en parameter till anropet.

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

Du kan få en detaljerad beskrivning av alla parametrar genom att köra följande exempel:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Ändra en krets

Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen.

Du kan göra följande uppgifter utan avbrott:

* Aktivera eller inaktivera tillägget ExpressRoute premium för ExpressRoute-kretsen.
* Öka bandbredden för ExpressRoute-krets under förutsättning att det finns tillgänglig kapacitet på porten. Nedgradera bandbredden för en krets stöds inte. 
* Ändra avläsning planen från förbrukade Data till obegränsad. Om du ändrar avläsning planen från obegränsad till förbrukade Data stöds inte.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Referera till den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md) mer information om gränser och begränsningar.

### <a name="enable-the-expressroute-premium-add-on"></a>Aktivera ExpressRoute premium-tillägg

Du kan aktivera ExpressRoute premium-tillägg för en befintlig krets med hjälp av följande PowerShell-cmdlet:

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

Kretsen har nu tilläggsfunktioner för ExpressRoute premium aktiverat. När kommandot har utförts, börjar fakturering för premium-tillägg-funktionen.

### <a name="disable-the-expressroute-premium-add-on"></a>Inaktivera tillägget ExpressRoute premium

> [!IMPORTANT]
> Den här åtgärden kan misslyckas om du använder resurser som är större än vad som tillåts för standard-kretsen.
> 
> 

#### <a name="considerations"></a>Överväganden

* Se till att antalet virtuella nätverk som är kopplad till kretsen är mindre än 10 innan du Nedgradera från premium till standard. Om du inte gör det, uppdateringsbegäran misslyckas och du debiteras premium priser.
* Du måste ta bort länken alla virtuella nätverk i andra geopolitiska regioner. Om du inte din begäran om uppdatering misslyckas och du debiteras premium priser.
* Din routningstabellen måste vara mindre än 4 000 vägar för privat peering. Om din väg tabell är större än 4 000 vägar, släpper BGP-sessionen och reenabled inte tills antalet annonserade prefix hamnar under 4 000.

#### <a name="to-disable-the-premium-add-on"></a>Att inaktivera tillägget premium

Du kan inaktivera ExpressRoute premium-tillägg för en befintlig krets med hjälp av följande PowerShell-cmdlet:

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

### <a name="update-the-expressroute-circuit-bandwidth"></a>Uppdatera bandbredd för ExpressRoute-krets

Kontrollera den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md) stöd för alternativ för bandbredd för leverantören. Du kan välja valfri storlek som är större än storleken på befintliga kretsen som gör att den fysiska porten (där kretsen skapas).

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-kretsen om det finns för lite kapacitet på befintliga porten. Du kan inte uppgradera kretsen om det finns inga ytterligare kapacitet på den platsen.
>
> Du kan minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradera bandbredd måste du ta bort etableringen av ExpressRoute-kretsen och etablera en ny ExpressRoute-krets.
> 
> 

#### <a name="resize-a-circuit"></a>Ändra storlek på en krets

När du har bestämt vilken storlek måste använda du följande kommando för att ändra storlek på kretsen:

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

När kretsen har tagits storlek på Microsoft-sida, måste du kontakta anslutningsleverantören om du vill uppdatera konfigurationer på sidan så att den matchar den här ändringen. Fakturering börjar för alternativet uppdaterade bandbredd från och med nu.

Om du ser följande fel om du vill öka krets bandbredd, innebär det inte finns inte tillräckligt med bandbredd kvar på den fysiska porten där befintliga kretsen har skapats. Du måste ta bort den här kretsen och skapa en ny krets storlek som du behöver.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Ta bort etableringen och ta bort en krets

### <a name="considerations"></a>Överväganden

* Du måste ta bort länken alla virtuella nätverk från ExpressRoute-kretsen för den här åtgärden ska lyckas. Kontrollera om du har några virtuella nätverk som är länkade till kretsen om åtgärden misslyckas.
* Om leverantören för ExpressRoute-kretsen Etableringsläge är **etablering** eller **etablerad** du måste arbeta med tjänstleverantören för att ta bort etableringen krets på sidan. Vi kan fortsätta att reservera resurser och debitera dig tills tjänstleverantör har slutförts avetablering kretsen och meddela oss.
* Om tjänstleverantör har avetableras kretsen (Etableringsstatus tjänstleverantör har angetts till **inte etablerats**), du kan sedan ta bort kretsen. Detta stoppar faktureringen för kretsen.

#### <a name="delete-a-circuit"></a>Ta bort en krets

Du kan ta bort ExpressRoute-krets genom att köra följande kommando:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```