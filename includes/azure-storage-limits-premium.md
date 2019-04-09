---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1cb353d22e39447d7aeb723b5506ac0209cf74d8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59287110"
---
### <a name="premium-performance-block-blob-storage"></a>Premium-prestanda block blob-lagring

Premium prestanda block blob storage-kontot är optimerad för program som använder mindre, kilobyte adressintervall objekt. Det är perfekt för program som kräver hög transaktioner eller konsekvent låg latens-lagring. Premium-prestanda block blob-lagring är utformat för att skala med dina program. Om du planerar att distribuera program som kräver hundratals tusentals förfrågningar per sekund eller petabyte lagringskapacitet du kontakta oss genom att skicka en supportförfrågan i den [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-filestorage-preview"></a>Premium-prestanda FileStorage (förhandsversion)

Premium-filerna använder ett unikt lagringskonto med namnet **FileStorage (förhandsversion)**. Den här kontotypen är utformad för arbetsbelastningar med hög IOPS, högt dataflöde med konsekvent låg latens. Premium-fillagring skalas med den etablerade resursstorleken.

|Område  |Mål  |
|---------|---------|
|Maxstorlek     |5 TiB (offentlig förhandsversion), 100 TiB (begränsad offentlig förhandsversion)     |
|Resurser   |Obegränsat  |
|IOPS     |100 000 (begränsad offentlig förhandsversion)    |
|Bandbredd|5 GB/s     |

 Dela skala mål för premium-fil, se den [Premium-filer skala mål](../articles/storage/common/storage-scalability-targets.md#premium-files-scale-targets) avsnittet.

### <a name="premium-performance-page-blob-storage"></a>Sidbloblagring för Premium-prestanda

Premium-prestanda, general-purpose v1 eller v2-konton har följande skalbarhetsmål:

| Total kapacitet                            | Totala bandbredden för ett lokalt redundant lagringskonto                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Kapacitet för disk: 35 TB <br>Kapacitet för ögonblicksbilder: 10 TB | Upp till 50 Gigabit per sekund för inkommande<sup>1</sup> + utgående<sup>2</sup> |

<sup>1</sup> alla data (förfrågningar) som skickas till ett lagringskonto

<sup>2</sup> alla data (svar) som tas emot från ett lagringskonto

Om du använder premium storage-konton för prestanda för ohanterade diskar och programmet överskrider det för skalbarhetsmål för ett enda lagringskonto, kanske du vill migrera till managed disks. Om du inte vill migrera till managed disks kan du skapa programmet så att flera lagringskonton. Sedan kan partitionera dina data på dessa lagringskonton. Till exempel om du vill koppla 51 TB diskar mellan flera virtuella datorer fördelade dem på två lagringskonton. 35 TB är gränsen för en enda premium storage-konto. Se till att ett enda premiumlagringskonto för prestanda aldrig har mer än 35 TB etablerade diskar.