---
title: 'Länka ett virtuellt nätverk till en ExpressRoute-krets: PowerShell: klassiska: Azure | Microsoft Docs'
description: Det här dokumentet innehåller en översikt över hur du länkar virtuella nätverk (Vnet) för ExpressRoute-kretsar med den klassiska distributionsmodellen och PowerShell.
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: ''
tags: azure-service-management
ms.assetid: 9b53fd72-9b6b-4844-80b9-4e1d54fd0c17
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: ganesr
ms.openlocfilehash: 2f0fed77a676bc46e8daa9c41efd533dffe72d8b
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
ms.locfileid: "29875005"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Ansluta ett virtuellt nätverk till en ExpressRoute-krets med hjälp av PowerShell (klassisk)
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
>

Den här artikeln beskriver hur du länka virtuella nätverk (Vnet) för Azure ExpressRoute-kretsar med hjälp av den klassiska distributionsmodellen och PowerShell. Virtuella nätverk kan antingen vara i samma prenumeration eller kan vara en del av en annan prenumeration.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om distributionsmodeller för Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration
1. Du behöver den senaste versionen av Azure PowerShell-moduler. Du kan hämta de senaste PowerShell-modulerna från PowerShell-avsnitt i den [Azure hämtar sidan](https://azure.microsoft.com/downloads/). Följ instruktionerna i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) stegvisa instruktioner om hur du konfigurerar din dator om du vill använda Azure PowerShell-moduler.
2. Du behöver gå igenom den [krav](expressroute-prerequisites.md), [routningskrav](expressroute-routing.md), och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.
3. Du måste ha en aktiv ExpressRoute-krets.
   * Följ instruktionerna för att [skapar du en ExpressRoute-krets](expressroute-howto-circuit-classic.md) och har anslutningsleverantören aktivera kretsen.
   * Se till att du har privat Azure-peering konfigurerats för kretsen. Finns det [konfigurera routning](expressroute-howto-routing-classic.md) artikel routning anvisningar.
   * Kontrollera att privat Azure-peering har konfigurerats och BGP-peering mellan ditt nätverk och Microsoft är igång så att du kan aktivera anslutning för slutpunkt till slutpunkt.
   * Du måste ha ett virtuellt nätverk och en virtuell nätverksgateway skapas och helt etablerad. Följ instruktionerna för att [konfigurera ett virtuellt nätverk för ExpressRoute](expressroute-howto-vnet-portal-classic.md).

Du kan länka upp till 10 virtuella nätverk till en ExpressRoute-krets. Alla virtuella nätverk måste vara i samma geopolitiska region. Du kan länka ett stort antal virtuella nätverk till ExpressRoute-krets eller länk virtuella nätverk som ingår i andra geopolitiska regioner om du har aktiverat ExpressRoute premium-tillägg. Kontrollera den [vanliga frågor och svar](expressroute-faqs.md) för mer information om premium-tillägg.

Ett enda VNet kan länkas till upp till fyra ExpressRoute-kretsar. Använd anvisningarna nedan om du vill skapa en ny länk till varje ExpressRoute-krets du ansluter till. ExpressRoute-kretsar kan finnas i samma prenumeration, olika prenumerationer eller en blandning av båda.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Ansluta ett virtuellt nätverk med samma prenumeration till en krets
Du kan länka ett virtuellt nätverk till en ExpressRoute-krets med hjälp av följande cmdlet. Kontrollera att den virtuella nätverksgatewayen har skapats och är redo för att länka innan du kör cmdlet.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Anslut ett virtuellt nätverk i en annan prenumeration till en krets
Du kan dela en ExpressRoute-krets över flera prenumerationer. Följande bild visar ett enkelt schema i så här fungerar för ExpressRoute-kretsar över flera prenumerationer.

Var och en av mindre molnen i stora molnet används för att representera prenumerationer som hör till olika avdelningar inom en organisation. Varje avdelning inom organisationen kan använda sina egna prenumeration för att distribuera sina tjänster, men avdelningarna kan dela en enda ExpressRoute-krets att ansluta till ditt lokala nätverk. En avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer inom organisationen kan använda ExpressRoute-kretsen.

> [!NOTE]
> Anslutning och bandbredd kostnader för dedikerade kretsen tillämpas på ExpressRoute-kretsen ägare. Alla virtuella nätverk dela på samma bandbredd.
> 
> 

![Anslutning över prenumerationer](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administration
Den *kretsägaren* är administratör/coadministrator för prenumerationen som ExpressRoute-kretsen har skapats. Kretsägaren kan tillåta administratörer/coadministrators av andra prenumerationer kallas *krets användare*, så att den dedikerade kretsen som de äger. Kretsen användare som har behörighet att använda organisationens ExpressRoute-kretsen kan koppla det virtuella nätverket i prenumerationen till ExpressRoute-kretsen när de har behörighet.

Kretsägaren har behörighet att ändra och återkalla tillstånd när som helst. Återkalla ett tillstånd som leder till att alla länkar tas bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Kretsen ägare åtgärder

**Skapa ett tillstånd**

Kretsägaren tillåter administratörer att använda den angivna kretsen andra prenumerationer. I följande exempel gör administratören för kretsen (Contoso IT) att administratören av en annan prenumeration (Dev-Test) att länka upp till två virtuella nätverk till kretsen. Contoso IT-administratören gör detta genom att ange Dev-Test Microsoft-ID. Cmdlet Skicka inte e-post till angivna Microsoft-ID. Kretsägaren måste uttryckligen meddela andra prenumerationsägaren att tillståndet har slutförts.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**Granska tillstånd**

Kretsägaren kan granska alla tillstånd som utfärdats för en viss krets genom att köra följande cmdlet:

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


**Uppdaterar tillstånd**

Kretsägaren kan ändra tillstånd genom att använda följande cmdlet:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**Ta bort tillstånd**

Kretsägaren kan återkalla/ta bort tillstånd till användaren genom att köra följande cmdlet:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Kretsen användaren åtgärder

**Granska tillstånd**

Kretsen användaren kan granska tillstånd genom att använda följande cmdlet:

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

**Löser länken tillstånd**

Kretsanvändare kan köra följande cmdlet för att lösa in en länk-tillstånd:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

Kör kommandot i den nya länkade prenumerationen för det virtuella nätverket:

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

