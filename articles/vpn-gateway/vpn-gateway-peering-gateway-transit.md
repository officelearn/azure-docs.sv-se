---
title: Konfigurera VPN-gatewayöverföring för peer-kopplade virtuella nätverk
description: Konfigurera Gateway-överföring för peering av virtuella nätverk för att sömlöst ansluta två virtuella Azure-nätverk till ett för anslutnings syfte.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.tgt_pltfrm: na
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 3f90ccbc98dac957fcb9fb81cd24c7ae6c9ac664
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657558"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>Konfigurera VPN-gatewayöverföring för peer-kopplade virtuella nätverk

Den här artikeln beskriver hur du konfigurerar gatewayöverföring för peer-kopplade virtuella nätverk. [Peer-kopplade virtuella nätverk](../virtual-network/virtual-network-peering-overview.md) syftar på två virtuella Azure-nätverk som kopplats ihop och slagits samman för anslutningsändamål. [Gatewayöverföring](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) är en egenskap i peer-kopplade virtuella nätverk som gör att ett virtuellt nätverk kan att använda VPN-gatewayen i det peer-kopplade virtuella nätverket för anslutning mellan olika platser eller mellan olika virtuella nätverk. Följande diagram illustrerar hur gatewayöverföring fungerar med peer-kopplade virtuella nätverk.

![Gatewayöverföring](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

I diagrammet gör gatewayöverföringen att de peer-kopplade virtuella nätverken kan använda Azure VPN-gatewayen i Hub-RM. Anslutningar som är tillgängliga på VPN-gatewayen, inklusive S2S, P2S och anslutningar mellan virtuella nätverk gäller för alla tre virtuella nätverk. Överföringsalternativet är tillgängligt för peer-koppling mellan samma eller olika distributionsmodeller. Dock måste VPN-gatewayen finnas i det virtuella nätverk som använder Resource Manager-distributionsmodellen, som du ser i diagrammet.

I nätverksarkitekturer av typen ”nav och eker” (hub-and-spoke) innebär gatewayöverföring att virtuella ekernätverk kan dela VPN-gatewayen i navet, i stället för att en VPN-gateway distribueras i varje virtuellt ekernätverk. Vägarna till de gatewayanslutna virtuella nätverken eller till de lokala nätverken distribueras till de peer-kopplade virtuella nätverkens routningstabeller med hjälp av gatewayöverföring. Du kan inaktivera den automatiska distributionen av vägar från VPN-gatewayen. Skapa en routningstabell med alternativet ”**Inaktivera spridning av BGP-väg**” och associera routningstabellen till undernäten om du vill förhindra distribution av vägar till dessa undernät. Mer information finns i [routningstabellen för virtuella nätverk](../virtual-network/manage-route-table.md).

Två scenarier beskrivs i det här dokumentet:

1. Båda virtuella nätverken använder Resource Manager-distributionsmodellen
2. Det virtuella ekernätverket bygger på den klassiska modellen och det virtuella navnätverket med gateway använder Resource Manager-modellen


>[!NOTE]
> Om du gör en ändring i nätverkets topologi och har Windows VPN-klienter, måste VPN-klientprogrammet för Windows-klienter laddas ned och installeras igen för att ändringarna ska tillämpas på klienten.
>

## <a name="requirements"></a>Krav



Exemplet i det här dokumentet kräver att följande resurser skapas:

1. Virtuellt Resource Manager-navnätverk (Hub-RM) med en VPN-gateway
2. Virtuellt Resource Manager-ekernätverk (Spoke-RM)
3. Virtuellt klassiskt ekernätverk (Spoke-Classic) med den klassiska distributionsmodellen
4. Kontot som du använder måste ha nödvändiga roller och behörighet. Mer information finns i avsnittet [Behörigheter](#permissions) i den här artikeln.

Anvisningar finns i följande dokument:

1. [Skapa en VPN-gateway i ett virtuellt nätverk](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [Skapa peer-koppling av virtuella nätverk med samma distributionsmodell](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [Skapa peer-koppling av virtuella nätverk med olika distributionsmodeller](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a><a name="permissions"></a>Behörigheter

De konton som du använder för att skapa peer-kopplade virtuella nätverk måste ha nödvändiga roller eller behörigheter. Om du i exemplet nedan peer-kopplar två virtuella nätverk med namnen Hub-RM och Spoke-Classic måste ditt konto ha följande roller eller behörigheter för varje virtuellt nätverk:
    
|Virtuellt nätverk|Distributionsmodell|Roll|Behörigheter|
|---|---|---|---|
|Hub-RM|Resource Manager|[Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Klassisk|[Klassisk nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|E.t.|
|Spoke-Classic|Resource Manager|[Nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Klassisk|[Klassisk nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Lär dig mer om [inbyggda roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) och hur du tilldelar särskilda behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md?toc=%252fazure%252fvirtual-network%252ftoc.json) (endast Resource Manager).

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>Peer-koppling från Resource Manager till Resource Manager med gatewayöverföring

Aktivera gatewayöverföring genom att följa anvisningarna för att skapa eller uppdatera peer-kopplingen för virtuella nätverk.

1. Skapa eller uppdatera peer-kopplingen för de virtuella nätverken, från Spoke-RM till Hub-RM, på Azure Portal. Gå till den virtuella nätverksresursen Spoke-RM, klicka på ”Peering-sessioner” och sedan på ”Lägg till”:
    - Ange alternativet ”Resource Manager”
    - Välj det virtuella nätverket Hub-RM i motsvarande prenumeration
    - Kontrollera att ”Tillåt åtkomst till virtuellt nätverk” är ”Aktiverat”
    - Ange alternativet ”**Använd fjärrgateways**”
    - Klicka på ”OK”.

      ![spokerm-to-hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. Om peer-kopplingen redan har skapats navigerar du till peer-resursen och aktiverar sedan alternativet ”**Använd fjärrgateways**”, som du ser i skärmbilden i steg (1)

3. Skapa eller uppdatera peer-kopplingen för de virtuella nätverken, från Hub-RM till Spoke-RM, på Azure Portal. Gå till den virtuella nätverksresursen Hub-RM, klicka på ”Peering-sessioner” och sedan på ”Lägg till”:
    - Ange alternativet ”Resource Manager”
    - Kontrollera att ”Tillåt åtkomst till virtuellt nätverk” är ”Aktiverat”
    - Välj det virtuella nätverket ”Spoke-RM” i motsvarande prenumeration
    - Ange alternativet ”**Tillåt gatewayöverföring**”
    - Klicka på ”OK”.

      ![hubrm-to-spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. Om peer-kopplingen redan har skapats navigerar du till peer-resursen och aktiverar sedan alternativet ”**Tillåt gatewayöverföring**”, som du ser i skärmbilden i steg (3)

5. Kontrollera att statusen för peer-kopplingen är ”**Ansluten**” för båda virtuella nätverk

### <a name="powershell-sample"></a>PowerShell-exempel

Du kan också använda PowerShell för att skapa eller uppdatera peer-kopplingen i exemplet ovan. Ersätt variablerna med namnen på dina virtuella nätverk och resursgrupper.

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>Peer-koppling från Klassisk till Resource Manager med gatewayöverföring

Stegen liknar dem i Resource Manager-exemplet, förutom att åtgärderna bara tillämpas på det virtuella nätverket Hub-RM.

1. Skapa eller uppdatera peer-kopplingen för de virtuella nätverken, från Hub-RM till Spoke-RM, på Azure Portal. Gå till den virtuella nätverksresursen Hub-RM, klicka på ”Peering-sessioner” och sedan på ”Lägg till”:
   - Ange alternativet ”Klassisk” för distributionsmodellen för virtuella nätverk
   - Välj det virtuella nätverket ”Spoke-RM” i motsvarande prenumeration
   - Kontrollera att ”Tillåt åtkomst till virtuellt nätverk” är ”Aktiverat”
   - Ange alternativet ”**Tillåt gatewayöverföring**”
   - Klicka på ”OK”.

     ![hubrm-to-spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. Om peer-kopplingen redan har skapats navigerar du till peer-resursen och aktiverar sedan alternativet ”**Tillåt gatewayöverföring**”, som du ser i skärmbilden i steg (1)

3. Ingen åtgärd utförs på det virtuella nätverket Spoke-Classic

4. Kontrollera att statusen för peer-kopplingen är ”**Ansluten**” för det virtuella nätverket Hub-RM

När statusen är ”Ansluten” kan de virtuella ekernätverken börja använda anslutningar mellan virtuella nätverk eller lokala nätverk via VPN-gatewayen i det virtuella navnätverket.

### <a name="powershell-sample"></a>PowerShell-exempel

Du kan också använda PowerShell för att skapa eller uppdatera peer-kopplingen i exemplet ovan. Ersätt variablerna och prenumerations-ID:t med värdena för ditt virtuella nätverk, dina resursgrupper och din prenumeration. Du behöver bara skapa peer-kopplingen för det virtuella navnätverket.

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [begränsningar och beteenden vid peer-koppling av virtuella nätverk](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) samt om [inställningar för peer-koppling av virtuella nätverk](../virtual-network/virtual-network-manage-peering.md#create-a-peering) innan du skapar en peer-koppling av virtuella nätverk för användning i produktion.
* Lär dig hur du [skapar topologin för ett nav- och ekernätverk](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) med peer-koppling av virtuella nätverk och gatewayöverföring.