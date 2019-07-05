---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457558"
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