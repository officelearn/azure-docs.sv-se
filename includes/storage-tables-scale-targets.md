---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 2319a7620b70547d186a4ef5cb96f5ca6684c62c
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305341"
---
| Resource | Mål |
|----------|---------------|
| Maximal storlek för en enskild tabell | 500 TiB |
| Maximal storlek på en tabellentitet | 1 MiB |
| Maximalt antal egenskaper i en tabellentitet | 255, som innehåller tre Systemegenskaper: PartitionKey och RowKey tidsstämpel |
| Högsta totala storleken på egenskapsvärden i en entitet | 1 MiB |
| Totala storleken för en enskild egenskap i en entitet | Varierar beroende på typ vlastnosti. Mer information finns i **egenskapstyper** i [förstå den tabelltjänst-datamodellen](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Maximalt antal lagrade åtkomstprinciper per tabell | 5 |
| Maximala hastigheten per lagringskonto | 20 000 transaktioner per sekund, vilket förutsätter en storlek för 1 KiB entitet |
| Måldataflöde för en enskild tabellpartition (1 KiB-entiteter) | Upp till 2 000 enheter per sekund |
