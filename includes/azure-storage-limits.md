---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/13/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: fb349c9ea2178d84e367e763797d5d93d3ae4c53
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46299132"
---
I följande tabell beskrivs standardgränser för Azure Storage. Den *ingående* gränsen syftar på alla data (förfrågningar) som skickas till ett lagringskonto. Den *utgående* gränsen syftar på alla data (svar) som tas emot från ett lagringskonto.

| Resurs | Standardgräns |
| --- | --- |
| Antal lagringskonton per region per prenumeration, inklusive konton för standard- och premium | 200 |
| Maximal kapacitet för lagringskonton | 500 TiB |
| Maxantal blob-behållare, blobar, filresurser, tabeller, köer, enheter eller meddelanden per lagringskonto | Obegränsat |
| Maximala hastigheten per lagringskonto | 20 000 begäranden per sekund |
| Max ingångshändelser per lagringskonto (oss regioner) | 10 Gbit/s om RA-GRS/GRS aktiverad 20 Gbit/s för LRS/ZRS<sup>1</sup> |
| Maximalt antal utgående per lagringskonto (oss regioner) | 50 Gbit/s |
| Max ingångshändelser per lagringskonto (icke-amerikanska regioner) | 5 Gbit/s om RA-GRS/GRS aktiverad, 10 Gbit/s för LRS/ZRS<sup>1</sup> |
| Maximalt antal utgående per lagringskonto (icke-amerikanska regioner) | 50 Gbit/s |

<sup>1</sup>[azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy) alternativ inkluderar:
* **RA-GRS**: Read-access geo-redundant lagring. Om RA-GRS aktiveras är utgående mål för den sekundära platsen samma som för den primära platsen.
* **GRS**: Geo-redundant lagring. 
* **ZRS**: zonredundant lagring.
* **LRS**: lokalt redundant lagring. 

