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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680783"
---
PowerShell cmdlet **Get-AzPeeringLocation returnerar** en lista över peering-platser med den obligatoriska parametern `Kind`, som du ska använda i senare steg.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Direkt peering-platser innehåller följande fält:
* PeeringLocation 
* Land/region
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandbreddErbjuder

Verifiera att du är närvarande vid den önskade peering-funktionen genom att referera till [PeeringDB](https://wwww.peeringdb.com).

I det här exemplet visas hur du använder Seattle som peering-plats för att skapa en direkt peering.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```