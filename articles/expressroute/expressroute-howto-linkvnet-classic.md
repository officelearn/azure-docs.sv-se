---
title: 'Azure ExpressRoute: Länka ett VNet till en krets: klassisk'
description: Det här dokumentet innehåller en översikt över hur du länkar virtuella nätverk (VNets) till ExpressRoute-kretsar med hjälp av den klassiska distributionsmodellen och PowerShell.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: cherylmc
ms.openlocfilehash: 53c200b01dfa6bce09cfc058dc24ab8e38d253a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930041"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets med PowerShell (klassiskt)
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
>

Den här artikeln hjälper dig att länka virtuella nätverk (VNets) till Azure ExpressRoute-kretsar med PowerShell. Ett enda virtuella nätverk kan länkas till upp till fyra ExpressRoute-kretsar. Följ stegen i den här artikeln för att skapa en ny länk till varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsarna kan finnas i samma prenumeration, olika prenumerationer eller en blandning av båda. Den här artikeln gäller virtuella nätverk som skapats med den klassiska distributionsmodellen.

Du kan länka upp till 10 virtuella nätverk till en ExpressRoute-krets. Alla virtuella nätverk måste finnas i samma geopolitiska region. Du kan länka ett större antal virtuella nätverk till expressroutekretsen eller länka virtuella nätverk som finns i andra geopolitiska regioner om du aktiverar Premium-tillägget ExpressRoute. Mer information om premiumtillägget finns i [vanliga frågor](expressroute-faqs.md) och svar.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om Azures distributionsmodeller**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Granska [förutsättningarna,](expressroute-prerequisites.md) [routningskraven](expressroute-routing.md)och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.
* Du måste ha en aktiv ExpressRoute-krets.
   * Följ instruktionerna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) och låt din anslutningsleverantör aktivera kretsen.
   * Se till att du har Azure privat peering konfigurerad för din krets. Se artikeln [Konfigurera routning](expressroute-howto-routing-classic.md) för routningsinstruktioner.
   * Kontrollera att Azure-privat peering är konfigurerad och bgp-peering mellan nätverket och Microsoft är uppe så att du kan aktivera end-to-end-anslutning.
   * Du måste ha ett virtuellt nätverk och en virtuell nätverksgateway skapad och fullständigt etablerad. Följ instruktionerna för att [konfigurera ett virtuellt nätverk för ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Ladda ner de senaste PowerShell-cmdlets

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ansluta ett virtuellt nätverk i samma prenumeration till en krets
Du kan länka ett virtuellt nätverk till en ExpressRoute-krets med hjälp av följande cmdlet. Kontrollera att den virtuella nätverksgatewayen skapas och är klar för länkning innan du kör cmdleten.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Ta bort en virtuell nätverkslänk till en krets
Du kan ta bort en virtuell nätverkslänk till en ExpressRoute-krets med hjälp av följande cmdlet. Kontrollera att den aktuella prenumerationen är markerad för det angivna virtuella nätverket. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets
Du kan dela en ExpressRoute-krets över flera prenumerationer. Följande bild visar en enkel schematisk av hur delning fungerar för ExpressRoute-kretsar över flera prenumerationer.

Var och en av de mindre molnen i det stora molnet används för att representera prenumerationer som tillhör olika avdelningar inom en organisation. Var och en av avdelningarna inom organisationen kan använda sin egen prenumeration för att distribuera sina tjänster – men avdelningarna kan dela en enda ExpressRoute-krets för att ansluta tillbaka till ditt lokala nätverk. En enda avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer inom organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Anslutnings- och bandbreddsavgifter för den dedikerade kretsen kommer att tillämpas på ExpressRoute-kretsägaren. Alla virtuella nätverk har samma bandbredd.
> 
> 

![Anslutning mellan prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administration
*Kretsägaren* är administratör/coadministrator för prenumerationen där ExpressRoute-kretsen skapas. Kretsägaren kan auktorisera administratörer/coadministratorer för andra prenumerationer, så kallade *kretsanvändare,* att använda den dedikerade krets som de äger. Kretsanvändare som har behörighet att använda organisationens ExpressRoute-krets kan länka det virtuella nätverket i sin prenumeration till ExpressRoute-kretsen när de har auktoriserats.

Kretsägaren har befogenhet att ändra och återkalla auktoriseringar när som helst. Om du återkallar en auktorisering tas alla länkar bort från prenumerationen vars åtkomst återkallades.

### <a name="circuit-owner-operations"></a>Kretsägare verksamhet

**Skapa en auktorisering**

Kretsägaren auktoriserar administratörer av andra prenumerationer att använda den angivna kretsen. I följande exempel gör kretsadministratören (Contoso IT) det möjligt för administratören av en annan prenumeration (Dev-Test) att länka upp till två virtuella nätverk till kretsen. Contoso IT-administratören aktiverar detta genom att ange Microsoft-ID:et för dev-test. Cmdleten skickar inte e-post till det angivna Microsoft-ID:t. Kretsägaren måste uttryckligen meddela den andra prenumerationsägaren att auktoriseringen är klar.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Returnera:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Granska auktoriseringar**

Kretsägaren kan granska alla tillstånd som utfärdas på en viss krets genom att köra följande cmdlet:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Returnera:

  ```powershell
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
  ```

**Uppdatera auktoriseringar**

Kretsägaren kan ändra auktoriseringar med hjälp av följande cmdlet:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Returnera:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Ta bort auktoriseringar**

Kretsägaren kan återkalla/ta bort auktoriseringar till användaren genom att köra följande cmdlet:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Kretsanvändaråtgärder

**Granska auktoriseringar**

Kretsanvändaren kan granska auktoriseringar med hjälp av följande cmdlet:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Returnera:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Lösa in länkauktoriseringar**

Kretsanvändaren kan köra följande cmdlet för att lösa in en länkauktorisering:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Returnera:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Kör det här kommandot i den nyligen länkade prenumerationen för det virtuella nätverket:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
