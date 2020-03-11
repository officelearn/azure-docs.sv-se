---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942061"
---
Följande tabell beskriver mål för kapacitet, skalbarhet och prestanda för tabellagring.

| Resurs | Mål |
|----------|---------------|
| Antalet tabeller i ett Azure Storage-konto | Begränsas endast av lagringskontots kapacitet |
| Antalet partitioner i en tabell | Begränsas endast av lagringskontots kapacitet |
| Antalet entiteter i en partition | Begränsas endast av lagringskontots kapacitet |
| Maximal storlek på en enskild tabell | 500 TiB |
| Maximal storlek på en enskild entitet, inklusive alla egenskapsvärden | 1 MiB |
| Maximalt antal egenskaper i en tabellentitet | 255 (inklusive de tre systemegenskaperna, **PartitionKey**, **RowKey**och **Timestamp**) |
| Maximal total storlek på en enskild egenskap i en entitet | Varierar beroende på egenskapstyp. Mer information finns i **Egenskapstyper** i [Förstå Tabelltjänst-datamodellen](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Storlek på **PartitionKey** | En sträng som är upp till 1 KiB stor |
| Storlek på **RowKey** | En sträng som är upp till 1 KiB stor |
| Storlek på en entitetsgruppstransaktion | En transaktion kan som högst innehålla 100 entiteter och nyttolasten måste vara mindre än 4 MiB stor. En entitetsgruppstransaktion kan endast innehålla en uppdatering till en entitet en gång. |
| Maximalt antal lagrade åtkomstprinciper per tabell | 5 |
| Maximal förfrågningsfrekvens per lagringskonto | 20 000 transaktioner per sekund, vilket förutsätter en entitetsstorlek på 1 KiB |
| Måldataflöde för en enskild tabell-partition (1 KiB-entiteter) | Upp till 2 000 entiteter per sekund |