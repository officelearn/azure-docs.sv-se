---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392269"
---
| Resurs | Mål        |
|----------|---------------|
| Maximal storlek för enblobbehållare | Samma som maximal lagringskontokapacitet |
| Maximalt antal block i en blockblobb eller tilläggsblob | 50 000 block |
| Maximal storlek för ett block i en blockblob | 100 MiB (100 MiB) |
| Maximal storlek för en blockblob | 50.000 X 100 MiB (ca 4,75 TiB) |
| Maximal storlek för ett block i en tilläggsblob | 4 MiB (4 MiB) |
| Maximal storlek för en tilläggsblob | 50.000 x 4 MiB (ca 195 GiB) |
| Maximal storlek på en sidblob | 8 TiB |
| Maximalt antal lagrade åtkomstprinciper per blob-behållare | 5 |
|Målbegäran för en enskild blob | Upp till 500 begäranden per sekund |
|Måldataflöde för en enskild sidblob | Upp till 60 MiB per sekund |
|Måldataflöde för en enda blockblob |Upp till lagringskonto ingångs-/utgående gränser<sup>1</sup> |

<sup>1</sup> Dataflöde för en enda blob beror på flera faktorer, inklusive, men inte begränsat till: samtidighet, begäran storlek, prestandanivå, hastighet källa för uppladdningar och destination för nedladdningar. Om du vill dra nytta av prestandaförbättringarna för [blockblobar](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)med högt dataflöde laddar du upp större blobbar eller block. Anropa åtgärden [Placera blob](/rest/api/storageservices/put-blob) eller [Placera block](/rest/api/storageservices/put-block) med en blob eller blockstorlek som är större än 4 MiB för standardlagringskonton. För premiumblockblob eller för Lagringsgend2-lagringskonton för datasjölagring använder du en block- eller blobstorlek som är större än 256 KiB.
