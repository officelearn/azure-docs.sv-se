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
ms.openlocfilehash: 6f5d2dc30ac0f6316587fa0836b87cbd4efc0a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774398"
---
PowerShell cmdlet **Get-AzPeeringLocation returnerar** en lista över `Kind`peering-platser med den obligatoriska parametern , som du ska använda i senare steg:

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Exchange peering-platser innehåller följande fält:
* ExchangeName (ExchangeName)
* PeeringLocation
* Land/region
* PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Adress
* MicrosoftIPv6Adress

Verifiera att du är närvarande vid önskad peering-anläggning genom att referera till [PeeringDB](https://wwww.peeringdb.com).

Nedan finns ett exempel som visar hur du använder Seattle som peering-plats för att skapa en peering:

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```