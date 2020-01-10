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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774268"
---
I det här avsnittet beskrivs hur du utför följande ändrings åtgärder för direkt peering:

* Lägg till direkta peering-anslutningar
* Ta bort direkta peering-anslutningar
* Uppgradera eller nedgradera bandbredd på aktiva anslutningar.
* Lägg till IPv4/IPv6-session på aktiva anslutningar.
* Ta bort IPv4/IPv6-sessionen på aktiva anslutningar.

### <a name="add-direct-peering-connections"></a>Lägg till direkta peering-anslutningar

Nedan beskrivs hur du lägger till anslutningar till befintlig direkt peering

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Ta bort direkta peering-anslutningar

Det finns för närvarande inte stöd för att ta bort en anslutning på PowerShell. Kontakta [Microsoft-peering](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Uppgradera eller nedgradera bandbredd på aktiva anslutningar

Nedan visas ett exempel på hur du lägger till 10Gbps till en befintlig direkt anslutning.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Lägg till IPv4/IPv6-session på aktiva anslutningar.

Nedan visas ett exempel på hur du lägger till en IPv6-session på en befintlig direkt anslutning med endast IPv4-session. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Ta bort IPv4/IPv6-sessionen på aktiva anslutningar.

Det finns för närvarande inte stöd för att ta bort en IPv4/IPv6-session från en befintlig anslutning på PowerShell. Kontakta [Microsoft-peering](mailto:peeringexperience@microsoft.com).