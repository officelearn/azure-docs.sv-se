---
title: 'Länka ett virtuellt nätverk till en ExpressRoute-krets: PowerShell: klassiska: Azure | Microsoft Docs'
description: Det här dokumentet innehåller en översikt över hur du länka virtuella nätverk (Vnet) till ExpressRoute-kretsar med hjälp av den klassiska distributionsmodellen och PowerShell.
services: expressroute
documentationcenter: na
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: ganesr
ms.openlocfilehash: e598249d0065bde8b3fe74883da8a0e39c9bc7c7
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308093"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets med PowerShell (klassisk)
> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
>

Den här artikeln beskriver hur du länka virtuella nätverk (Vnet) till Azure ExpressRoute-kretsar med hjälp av den klassiska distributionsmodellen och PowerShell. Virtuella nätverk kan vara i samma prenumeration eller kan vara en del av en annan prenumeration.

Ett enskilt virtuellt nätverk kan länkas till upp till fyra ExpressRoute-kretsar. Använd stegen i den här artikeln för att skapa en ny länk till varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsar kan finnas i samma prenumeration, olika prenumerationer eller en blandning av båda.

Du kan länka upp till 10 virtuella nätverk till en ExpressRoute-krets. Alla virtuella nätverken måste finnas i samma geopolitiska region. Du kan länka ett större antal virtuella nätverk till ExpressRoute-krets eller länka virtuella nätverk som är i andra geopolitiska regioner, om du har aktiverat ExpressRoute premium-tillägget. Kontrollera den [vanliga frågor och svar](expressroute-faqs.md) för mer information om premium-tillägget.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om distributionsmodeller för Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Granska den [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets.
   * Följ anvisningarna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) och låt anslutningsleverantören aktivera kretsen.
   * Kontrollera att du har Azure privat peering har konfigurerats för din krets. Se den [konfigurera routning](expressroute-howto-routing-classic.md) artikeln routning anvisningar.
   * Se till att Azures privata peering har konfigurerats och BGP-peering mellan ditt nätverk och Microsoft är igång så att du kan aktivera anslutning för slutpunkt till slutpunkt.
   * Du måste ha ett virtuellt nätverk och en virtuell nätverksgateway skapas och helt etablerad. Följ anvisningarna för att [konfigurera ett virtuellt nätverk för ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Ladda ned det senaste PowerShell-cmdlet

Du behöver den senaste versionen av Azure PowerShell-moduler. Du kan hämta de senaste PowerShell-modulerna från PowerShell-avsnittet i den [Azure hämtar sidan](https://azure.microsoft.com/downloads/). Följ instruktionerna i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) stegvisa instruktioner om hur du konfigurerar din dator om du vill använda Azure PowerShell-moduler.

Du måste även hämta modulen för ExpressRoute. Du kan använda kommandona i följande exempel för att ladda ned Azure- och ExpressRoute-moduler. När du använder dessa kommandon, Observera att versionsnumret (i det här exemplet 5.1.1) kommer att ändras när nyare versioner av cmdletarna är tillgängliga.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i samma prenumeration till en krets
Du kan länka ett virtuellt nätverk till en ExpressRoute-krets med hjälp av följande cmdlet. Se till att den virtuella nätverksgatewayen har skapats och är redo för att länka innan du kör cmdlet: en.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Ta bort en länk för virtuellt nätverk till en krets
Du kan ta bort en länk för virtuellt nätverk till en ExpressRoute-krets med hjälp av följande cmdlet. Kontrollera att den aktuella prenumerationen har valts för det angivna virtuella nätverket. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets
Du kan dela en ExpressRoute-krets över flera prenumerationer. Följande bild visar ett enkelt schema i så här fungerar för ExpressRoute-kretsar över flera prenumerationer.

Var och en av de mindre moln inom det stora molnet används för att representera prenumerationer som hör till olika avdelningar inom en organisation. Var och en av avdelningarna i organisationen kan använda sin egen prenumeration för att distribuera sina tjänster, men avdelningarna kan dela en enda ExpressRoute-krets för att ansluta till ditt lokala nätverk. En avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer i organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Avgifter för anslutning och bandbredd för dedikerade kretsen tillämpas till ExpressRoute-krets ägare. Alla virtuella nätverk delar samma bandbredden.
> 
> 

![Anslutningen mellan prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administration
Den *kretsägaren* är administratör/medadministratör för prenumerationen som ExpressRoute-kretsen har skapats. Kretsägaren kan auktorisera administratörer/medadministratörer för andra prenumerationer, kallas *krets användare*ska gå att använda dedikerade kretsen som de äger. Kretsanvändare som har behörighet att använda organisationens ExpressRoute-krets kan koppla det virtuella nätverket i sin prenumeration till ExpressRoute-krets när de har behörighet.

Kretsägaren har rätt att ändra och återkalla auktoriseringar när som helst. Om du återkallar en auktorisering resulterar i alla länkar som tas bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Kretsen ägare åtgärder

**Skapa en auktorisering**

Kretsägaren auktoriserar administratörerna av andra prenumerationer att använda den angivna kretsen. I följande exempel kan administratören för kretsen (Contoso IT) administratör för en annan prenumeration (utveckling och testning) länka upp till två virtuella nätverk till kretsen. Contoso IT-administratören gör detta genom att ange utvecklings-och Microsoft-ID. Cmdlet: en Skicka inte e-postmeddelande till den angivna Microsoft-ID. Kretsägaren måste uttryckligen meddela andra prenumerationsägaren att auktoriseringen har slutförts.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**Granska auktoriseringar**

Kretsägaren kan granska alla auktoriseringar som utfärdas på en viss krets genom att köra följande cmdlet:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


**Uppdaterar auktoriseringar**

Kretsägaren kan ändra tillstånd med hjälp av följande cmdlet:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**Tar bort auktoriseringar**

Kretsägaren kan återkalla/ta bort auktoriseringar för användaren genom att köra följande cmdlet:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Kretsen användaråtgärder

**Granska auktoriseringar**

Kretsanvändare kan granska tillstånd med hjälp av följande cmdlet:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

**Löser in auktoriseringar**

Kretsanvändare kan köra följande cmdlet för att lösa in en länk-auktorisering:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

Kör följande kommando i den nya länkade prenumerationen för det virtuella nätverket:

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

