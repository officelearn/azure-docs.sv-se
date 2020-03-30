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
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774294"
---
PowerShell cmdlet **Get-AzPeeringLocation returnerar** en lista över `Kind`peering-platser med den obligatoriska parametern , som du ska använda i senare steg:

```powershell
Get-AzPeeringLocation -Kind Direct
```

Direkt peering-platser innehåller följande fält:
* PeeringLocation 
* Land/region
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandbreddErbjuder

Verifiera att du är närvarande vid önskad peering-anläggning genom att referera till [PeeringDB](https://wwww.peeringdb.com).

Nedan finns ett exempel som visar hur du använder Seattle som peering-plats för att skapa en direkt peering:

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