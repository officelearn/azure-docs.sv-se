---
title: "Skapa och ändra en ExpressRoute-krets: PowerShell: klassiska Azure-portalen | Microsoft Docs"
description: "Den här artikeln vägleder dig genom stegen för att skapa och etablera en ExpressRoute-krets. Den här artikeln visar även hur att kontrollera status, uppdatera eller ta bort och ta bort etableringen kretsen."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 93ddc2975db34053c6a776d1c3b931536f3f8ec7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-modify-an-expressroute-circuit-using-powershell-classic"></a>Skapa och ändra en ExpressRoute-krets med hjälp av PowerShell (klassisk)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln vägleder dig genom stegen för att skapa en Azure ExpressRoute-krets med hjälp av PowerShell-cmdlets och den klassiska distributionsmodellen. Den här artikeln visar även hur att kontrollera status, uppdatera eller ta bort och ta bort etableringen av en ExpressRoute-krets.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]


**Om distributionsmodeller för Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Innan du börjar
### <a name="step-1-review-the-prerequisites-and-workflow-articles"></a>Steg 1. Granska krav och arbetsflöde artiklar
Kontrollera att du har granskat den [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.  

### <a name="step-2-install-the-latest-versions-of-the-azure-service-management-sm-powershell-modules"></a>Steg 2. Installera de senaste versionerna av Azure Service Management (SM) PowerShell-moduler
Följ instruktionerna i [komma igång med Azure PowerShell-cmdlets](/powershell/azure/overview) stegvisa instruktioner om hur du konfigurerar din dator om du vill använda Azure PowerShell-moduler.

### <a name="step-3-log-in-to-your-azure-account-and-select-a-subscription"></a>Steg 3. Logga in på ditt Azure-konto och välja en prenumeration
1. Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta:

        Login-AzureRmAccount

2. Kontrollera prenumerationerna för kontot.

        Get-AzureRmSubscription

3. Om du har mer än en prenumeration väljer du den du vill använda.

        Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_id"

4. Kontrollera om valda prenumerations-id har angetts som standard.

        Get-AzureSubscription -default

## <a name="create-and-provision-an-expressroute-circuit"></a>Skapa och etablera en ExpressRoute-krets
### <a name="step-1-import-the-powershell-modules-for-expressroute"></a>Steg 1. Importera PowerShell-moduler för ExpressRoute
 Om du inte redan har gjort det, måste du importera Azure och ExpressRoute-moduler i PowerShell-sessionen för att börja använda ExpressRoute-cmdlets. Du importerar moduler från den plats som de installerades för att lokalt på datorn. Beroende på vilken metod som du använde för att installera modulerna vara platsen densamma som visas i följande exempel. Ändra exemplet om det behövs.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="step-2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>Steg 2. Hämta en lista över providers som stöds, platser och bandbredder
Innan du skapar en ExpressRoute-krets måste listan över providers stöds anslutning, platser och alternativ för bandbredd.

PowerShell-cmdleten `Get-AzureDedicatedCircuitServiceProvider` returnerar informationen som du ska använda i senare steg:

    Get-AzureDedicatedCircuitServiceProvider

Kontrollera om anslutningsleverantören finns med i listan. Anteckna följande information eftersom du behöver det senare när du skapar en krets:

* Namn
* PeeringLocations
* BandwidthsOffered

Nu är du redo att skapa en ExpressRoute-krets.         

### <a name="step-3-create-an-expressroute-circuit"></a>Steg 3. Skapa en ExpressRoute-krets
I följande exempel visas hur du skapar en 200 Mbit/s ExpressRoute-krets via Equinix i Silicon dal. Om du använder en annan provider och andra inställningar, i stället informationen när du gör din begäran.

> [!IMPORTANT]
> ExpressRoute-krets kommer att debiteras från den tidpunkt då en nyckel har utfärdats. Se till att utföra den här åtgärden när anslutningen providern är redo att etablera kretsen.
> 
> 

Följande är ett exempelbegäran om en ny nyckel:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Eller, om du vill skapa en ExpressRoute-krets med premium-tillägg kan du använda följande exempel. Referera till den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md) för mer information om premium-tillägg.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


Svaret innehåller nyckeln för tjänsten. Du kan få en detaljerad beskrivning av alla parametrar genom att köra följande:

    get-help new-azurededicatedcircuit -detailed

### <a name="step-4-list-all-the-expressroute-circuits"></a>Steg 4. Visa en lista med alla ExpressRoute-kretsar
Du kan köra den `Get-AzureDedicatedCircuit` kommando för att hämta en lista över alla ExpressRoute-kretsar som du skapade:

    Get-AzureDedicatedCircuit

Svaret blir något som liknar följande exempel:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Du kan hämta den här informationen när som helst genom att använda den `Get-AzureDedicatedCircuit` cmdlet. Att göra anropet utan några parametrar visas alla kretsar. Nyckeln för tjänsten visas i den *ServiceKey* fältet.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Du kan få en detaljerad beskrivning av alla parametrar genom att köra följande:

    get-help get-azurededicatedcircuit -detailed

### <a name="step-5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Steg 5. Skicka tjänstnyckeln till anslutningsleverantören för etablering
*ServiceProviderProvisioningState* innehåller information om det aktuella tillståndet för etablering på sida-leverantör. *Status för* ger tillståndet på Microsoft-sida. Mer information om krets etablering tillstånd finns det [arbetsflöden](expressroute-workflows.md#expressroute-circuit-provisioning-states) artikel.

När du skapar en ny ExpressRoute-krets blir kretsen i följande tillstånd:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Kretsen kommer att gå till följande tillstånd när anslutningen providern håller på att du:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

En ExpressRoute-krets måste vara i följande läge att kunna använda den:

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="step-6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Steg 6. Regelbundet kontrollera status och tillståndet för nyckeln krets
På så sätt får du reda på om din leverantör har aktiverat kretsen. När kretsen har konfigurerats, *ServiceProviderProvisioningState* visas som *etablerad* som visas i följande exempel:

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="step-7-create-your-routing-configuration"></a>Steg 7. Skapa din routningskonfiguration
Referera till den [ExpressRoute-krets routningskonfiguration (skapa och ändra krets peerkopplingar)](expressroute-howto-routing-classic.md) artikel stegvisa instruktioner.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapats med leverantörer som erbjuder layer 2 tjänster. Om du använder en tjänstprovider som erbjuder Hanterade layer 3-tjänster (vanligtvis en IP VPN, t.ex. MPLS), anslutningsleverantören kan konfigurera och hantera routning av du.
> 
> 

### <a name="step-8-link-a-virtual-network-to-an-expressroute-circuit"></a>Steg 8. Länka ett virtuellt nätverk till en ExpressRoute-krets
Därefter länka ett virtuellt nätverk till ExpressRoute-kretsen. Referera till [länka ExpressRoute-kretsar till virtuella nätverk](expressroute-howto-linkvnet-classic.md) stegvisa instruktioner. Om du behöver skapa ett virtuellt nätverk med den klassiska distributionsmodellen expressroute, se [skapa ett virtuellt nätverk för ExpressRoute](expressroute-howto-vnet-portal-classic.md).

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Hämta status för en ExpressRoute-krets
Du kan hämta den här informationen när som helst genom att använda den `Get-AzureCircuit` cmdlet. Att göra anropet utan några parametrar visas alla kretsar.

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

Du kan få information om en specifik ExpressRoute-krets genom att överföra nyckeln för tjänsten som en parameter till anropet.

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Du kan få en detaljerad beskrivning av alla parametrar genom att köra följande exempel:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Ändra en ExpressRoute-krets
Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen.

Du kan göra följande med utan avbrott:

* Aktivera eller inaktivera tillägget ExpressRoute premium för ExpressRoute-kretsen.
* Öka bandbredden för ExpressRoute-krets under förutsättning att det finns tillgänglig kapacitet på porten. Observera att nedgradera bandbredden för en krets inte stöds. 
* Ändra avläsning planen från förbrukade Data till obegränsad. Observera att om du ändrar avläsning planen från obegränsad till förbrukade Data inte stöds.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

Referera till den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md) mer information om gränser och begränsningar.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Så här aktiverar du ExpressRoute premium-tillägg
Du kan aktivera ExpressRoute premium-tillägg för en befintlig krets med hjälp av följande PowerShell-cmdlet:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

Kretsen har nu tilläggsfunktioner för ExpressRoute premium aktiverat. Observera att vi startar fakturering för premium-tillägg-funktionen när kommandot har körts.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Att inaktivera tillägget ExpressRoute premium
> [!IMPORTANT]
> Den här åtgärden kan misslyckas om du använder resurser som är större än vad som tillåts för standard-kretsen.
> 
> 

#### <a name="considerations"></a>Överväganden

* Du måste se till att antalet virtuella nätverk som är kopplad till kretsen är mindre än 10 innan du Nedgradera från premium till standard. Om du inte gör det, uppdateringsbegäran misslyckas och du kommer att debiteras premium priser.
* Du måste ta bort länken alla virtuella nätverk i andra geopolitiska regioner. Om du inte gör det, uppdateringsbegäran misslyckas och du kommer att debiteras premium priser.
* Din routningstabellen måste vara mindre än 4 000 vägar för privat peering. Om din väg tabell är större än 4 000 vägar, kommer BGP-sessionen och reenabled inte tills antalet annonserade prefix hamnar under 4 000.

#### <a name="disable-the-premium-add-on"></a>Inaktivera tillägget premium
Du kan inaktivera ExpressRoute premium-tillägg för en befintlig krets med hjälp av följande PowerShell-cmdlet:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Uppdatera bandbredd för ExpressRoute-krets
Kontrollera den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md) stöd för alternativ för bandbredd för leverantören. Du kan välja valfri storlek som är större än storleken på befintliga kretsen som gör att den fysiska porten (där kretsen skapas).

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-kretsen om det finns för lite kapacitet på befintliga porten. Du kan inte uppgradera kretsen om det finns inga ytterligare kapacitet på den platsen.
>
> Du kan minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradera bandbredd måste du ta bort etableringen av ExpressRoute-kretsen och etablera en ny ExpressRoute-krets.
> 
> 

#### <a name="resize-a-circuit"></a>Ändra storlek på en krets

När du har bestämt vilken storlek måste använda du följande kommando för att ändra storlek på kretsen:

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Kretsen kommer har tagits stora på Microsoft-sida. Du måste kontakta anslutningsleverantören om du vill uppdatera konfigurationer på sidan så att den matchar den här ändringen. Observera att vi startar debitering du för alternativet uppdaterade bandbredd från och med nu.

Om du ser följande fel om du vill öka krets bandbredd, innebär det inte finns inte tillräckligt med bandbredd kvar på den fysiska porten där befintliga kretsen har skapats. Du måste ta bort den här kretsen och skapa en ny krets storlek som du behöver. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Borttagning och tar bort en ExpressRoute-krets

### <a name="considerations"></a>Överväganden

* Du måste ta bort länken alla virtuella nätverk från ExpressRoute-kretsen för den här åtgärden ska lyckas. Kontrollera om du har några virtuella nätverk som är länkade till kretsen om åtgärden misslyckas.
* Om leverantören för ExpressRoute-kretsen Etableringsläge är **etablering** eller **etablerad** du måste arbeta med tjänstleverantören för att ta bort etableringen krets på sidan. Vi kommer att fortsätta att reservera resurser och debitera dig tills tjänstleverantör har slutförts avetablering kretsen och meddela oss.
* Om tjänstleverantör har avetableras kretsen (Etableringsstatus tjänstleverantör har angetts till **inte etablerats**) du kan sedan ta bort kretsen. Detta förhindrar att faktureringen för kretsen.

#### <a name="delete-a-circuit"></a>Ta bort en krets

Du kan ta bort ExpressRoute-krets genom att köra följande kommando:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Nästa steg
När du har skapat kretsen, kontrollera att du gör följande:

* [Skapa och ändra routning för ExpressRoute-krets](expressroute-howto-routing-classic.md)
* [Länka ditt virtuella nätverk till ExpressRoute-krets](expressroute-howto-linkvnet-classic.md)

