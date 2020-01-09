---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392269"
---
| Resurs | Målinrikta        |
|----------|---------------|
| Maximal storlek för enskild BLOB-behållare | Samma som högsta kapacitet för lagrings konto |
| Maximalt antal block i en Block-Blob eller Lägg till BLOB | 50 000 block |
| Maximal storlek för ett block i en Block-Blob | 100 MiB |
| Maximal storlek för en Block-Blob | 50 000 X 100 MiB (cirka 4,75 TiB) |
| Maximal storlek för ett block i en tilläggs-BLOB | 4 MiB |
| Maximal storlek för en tilläggs-BLOB | 50 000 x 4 MiB (cirka 195 GiB) |
| Maximal storlek på en sid-BLOB | 8 TiB |
| Maximalt antal lagrade åtkomst principer per BLOB-behållare | 5 |
|Mål begär ande frekvens för en enskild BLOB | Upp till 500 begär Anden per sekund |
|Mål data flöde för en enda sid-BLOB | Upp till 60 MiB per sekund |
|Mål data flöde för en enda Block-Blob |Upp till lagrings kontots inkommande/utgående gränser<sup>1</sup> |

<sup>1</sup> data flöde för en enskild BLOB är beroende av flera faktorer, inklusive, men inte begränsat till: samtidighet, begär ande storlek, prestanda nivå, hastighet på källa för uppladdningar och mål för hämtningar. Om du vill dra nytta av prestanda förbättringarna för [block blobbar med höga data flöden](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)laddar du upp större blobbar eller block. Mer specifikt kan du anropa åtgärden för att [parkera](/rest/api/storageservices/put-blob) eller [blockera](/rest/api/storageservices/put-block) med en BLOB-eller block storlek som är större än 4 MIB för standard lagrings konton. För Premium Block-Blob eller för Data Lake Storage Gen2 lagrings konton använder du en block-eller BLOB-storlek som är större än 256 KiB.
