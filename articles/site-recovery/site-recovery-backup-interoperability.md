---
title: Stöd för användning av Azure Site Recovery med Azure Backup | Microsoft Docs
description: Innehåller en översikt över hur Azure Site Recovery och Azure Backup kan användas tillsammans.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035783"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Stöd för att använda Site Recovery med Azure Backup

Den här artikeln sammanfattas stöd för användning av den [Site Recovery-tjänsten](site-recovery-overview.md) tillsammans med den [Azure Backup-tjänsten](https://docs.microsoft.com/azure/backup/backup-overview).

**Åtgärd** | **Site Recovery-stöd** | **Detaljer**
--- | --- | ---
**Distribuera tjänster tillsammans** | Stöds | Tjänster är kompatibla och kan konfigureras tillsammans.
**Filen säkerhetskopiering/återställning** | Stöds | När säkerhetskopiering och replikering har aktiverats för en virtuell dator och säkerhetskopior tas, finns det något problem i återställningen på käll-virtuella datorer eller grupp med virtuella datorer. Replikering fortsätter som vanligt med ingen ändring i replikeringen.
**Disk säkerhetskopiering/återställning** | Inga aktuella support | Om du återställer en säkerhetskopierade disk måste du inaktivera och återaktivera replikering för den virtuella datorn igen.
**VM-säkerhetskopiering/återställning** | Inga aktuella support | Om du säkerhetskopierar eller återställer en virtuell dator eller en grupp med virtuella datorer måste du inaktivera och återaktivera replikering för den virtuella datorn.  


