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
ms.openlocfilehash: e3d2466f05028a963256dbcc052c46650857836d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372721"
---
### <a name="premium-performance-block-blob-storage"></a>Premium-prestanda block blob-lagring

Premium prestanda block blob storage-kontot är optimerad för program som använder mindre, kilobyte adressintervall objekt. Det är perfekt för program som kräver mycket hög transaktioner eller konsekvent låg latens-lagring. Premium-prestanda block blob-lagring är utformat för att skala med dina program. Om du planerar att distribuera program som kräver hundratals tusentals förfrågningar per sekund eller petabyte lagringskapacitet du kontakta oss genom att skicka en supportförfrågan i den [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-page-blob-storage"></a>Sidbloblagring för Premium-prestanda

Premium-prestanda i allmänna v1 eller v2-konton har följande skalbarhetsmål:

| Total kapacitet                            | Totala bandbredden för ett lokalt redundant lagringskonto                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Kapacitet för disk: 35 TB <br>Kapacitet för ögonblicksbilder: 10 TB | Upp till 50 Gigabit per sekund för inkommande<sup>1</sup> + utgående<sup>2</sup> |

<sup>1</sup> alla data (förfrågningar) som skickas till ett lagringskonto

<sup>2</sup> alla data (svar) som tas emot från ett lagringskonto

Om du använder premium storage-konton för prestanda för ohanterade diskar och programmet överskrider det för skalbarhetsmål för ett enda lagringskonto, kanske du vill migrera till managed disks. Om du inte vill migrera till managed disks kan du skapa programmet så att flera lagringskonton. Sedan kan partitionera dina data på dessa lagringskonton. Till exempel om du vill koppla 51 TB diskar mellan flera virtuella datorer fördelade dem på två lagringskonton. 35 TB är gränsen för en enda premium storage-konto. Se till att ett enda premiumlagringskonto för prestanda aldrig har mer än 35 TB etablerade diskar.