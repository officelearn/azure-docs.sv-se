---
title: Stöd för användning av Azure Site Recovery med Azure Backup
description: Ger en översikt över hur Azure Site Recovery och Azure Backup kan användas tillsammans.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72376222"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Stöd för att använda webbplatsåterställning med Azure Backup

Den här artikeln sammanfattar stöd för att använda [tjänsten Site Recovery](site-recovery-overview.md) tillsammans med Azure [Backup-tjänsten](https://docs.microsoft.com/azure/backup/backup-overview).

**Åtgärd** | **Site Recovery-stöd** | **Detaljer**
--- | --- | ---
**Distribuera tjänster tillsammans** | Stöds | Tjänsterna är driftskompatibla och kan konfigureras tillsammans.
**Säkerhetskopiering/återställning av filer** | Stöds | När säkerhetskopiering och replikering är aktiverade för en virtuell dator och säkerhetskopieringar tas, finns det inget problem med att återställa filer på källsidan virtuella datorer eller grupp av virtuella datorer. Replikeringen fortsätter som vanligt utan någon förändring i replikeringshälsan.
**Diskåterställning** | Inget aktuellt stöd | Om du återställer en säkerhetskopierad disk måste du inaktivera och återaktivera replikering för den virtuella datorn igen.
**Återställning av virtuella datorer** | Inget aktuellt stöd | Om du återställer en virtuell dator eller grupp av virtuella datorer måste du inaktivera och återaktivera replikering för den virtuella datorn.  


