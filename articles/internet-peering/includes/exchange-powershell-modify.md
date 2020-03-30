---
title: ta med fil
titleSuffix: Azure
description: ta med fil
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 86e1a9cce1864ce259fe07b6949be2e32be242a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774424"
---
Följande ändringsåtgärd stöds för Exchange peering
1. Lägga till Exchange-peering-anslutningar
1. Ta bort Exchange-peering-anslutningar
1. Lägg till IPv4/IPv6-session på aktiva anslutningar.
1. Ta bort IPv4/IPv6-session på aktiva anslutningar.


### <a name="add-exchange-peering-connections"></a>Lägga till Exchange-peering-anslutningar

Nedan beskrivs hur du lägger till anslutningar till befintlig Exchange-peering

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Ta bort Exchange-peering-anslutningar

Nedan beskrivs hur du tar bort anslutningar till befintlig Exchange-peering

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Visa alla anslutningar och markera den anslutning som du vill ta bort. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

I kommandot nedan anger du indexnumret för den anslutning som du vill ta bort i kommandot nedan.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Lägga till IPv4/IPv6-session vid aktiva anslutningar

Nedan beskrivs hur du lägger till IPv6-session i befintlig exchange-anslutning.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Ta bort IPv4/IPv6-session på aktiva anslutningar

Det går inte att ta bort en IPv4/IPv6-session från en befintlig anslutning i PowerShell. Kontakta [Microsoft peering](mailto:peeringexperience@microsoft.com).