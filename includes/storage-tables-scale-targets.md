---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942061"
---
I följande tabell beskrivs kapacitet, skalbarhet och prestandamål för tabelllagring.

| Resurs | Mål |
|----------|---------------|
| Antal tabeller i ett Azure-lagringskonto | Begränsas endast av lagringskontots kapacitet |
| Antal partitioner i en tabell | Begränsas endast av lagringskontots kapacitet |
| Antal entiteter i en partition | Begränsas endast av lagringskontots kapacitet |
| Maximal storlek för en enskild tabell | 500 TiB (09) |
| Maximal storlek för en enskild entitet, inklusive alla egenskapsvärden | 1 MiB (1 MiB) |
| Maximalt antal egenskaper i en tabellentitet | 255 (inklusive de tre systemegenskaperna, **PartitionKey,** **RowKey**och **Timestamp)** |
| Maximal total storlek för en enskild egenskap i en entitet | Varierar beroende på egenskapstyp. Mer information finns i **Egenskapstyper** i [Förstå tabelltjänstdatamodellen](/rest/api/storageservices/understanding-the-table-service-data-model). |
| **Partitionsnyckelns** storlek | En sträng upp till 1 KiB i storlek |
| **Radnyckelns** storlek | En sträng upp till 1 KiB i storlek |
| Storleken på en entitetsgruppstransaktion | En transaktion kan omfatta högst 100 enheter och nyttolasten måste vara mindre än 4 MiB i storlek. En entitetsgruppstransaktion kan endast innehålla en uppdatering till en entitet en gång. |
| Maximalt antal principer för lagrad åtkomst per tabell | 5 |
| Maximal begärandegrad per lagringskonto | 20 000 transaktioner per sekund, vilket förutsätter en enhetsstorlek på 1 KiB |
| Måldataflöde för en enda tabellpartition (1 KiB-entiteter) | Upp till 2 000 enheter per sekund |