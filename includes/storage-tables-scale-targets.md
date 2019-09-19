---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "67457558"
---
| Resource | Mål |
|----------|---------------|
| Maximal storlek för en enskild tabell | 500 TiB |
| Maximal storlek för en tabell enhet | 1 MiB |
| Maximalt antal egenskaper i en tabell enhet | 255, som innehåller tre system egenskaper: PartitionKey, RowKey och tidsstämpel |
| Maximal total storlek för egenskaps värden i en entitet | 1 MiB |
| Maximal total storlek för en enskild egenskap i en entitet | Varierar beroende på egenskaps typ. Mer information finns i **egenskaps typer** i [förstå tabell tjänstens data modell](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Maximalt antal lagrade åtkomst principer per tabell | 5 |
| Högsta begär ande frekvens per lagrings konto | 20 000 transaktioner per sekund, vilket förutsätter en 1-KiB enhets storlek |
| Mål data flöde för en enskild Table-partition (1 KiB-entiteter) | Upp till 2 000 enheter per sekund |