---
title: Stöd för att använda Azure Site Recovery med Azure Backup
description: Innehåller en översikt över hur Azure Site Recovery och Azure Backup kan användas tillsammans.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: 2078aa33ca735b91b5923d94028a286427f107a6
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146863"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Stöd för att använda Site Recovery med Azure Backup

I den här artikeln sammanfattas stödet för att använda tjänsten [Site Recovery](site-recovery-overview.md) tillsammans med [tjänsten Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

**Åtgärd** | **Site Recovery support** | **Detaljer**
--- | --- | ---
**Distribuera tjänster tillsammans** | Stöds | Tjänsterna är kompatibla och kan konfigureras tillsammans.
**Säkerhets kopiering/återställning av fil** | Stöds | När säkerhets kopiering och replikering har Aktiver ATS för en virtuell dator och säkerhets kopior görs, finns det inga problem med att återställa filer på virtuella datorer på den virtuella datorn eller en grupp med virtuella datorer. Replikeringen fortsätter som vanligt utan att någon ändring av replikeringstillståndet har ändrats.
**Säkerhets kopiering/återställning av disk** | Inget aktuellt stöd | Om du återställer en säkerhets kopie rad disk måste du inaktivera och återaktivera replikering för den virtuella datorn igen.
**Säkerhets kopiering/återställning av virtuella datorer** | Inget aktuellt stöd | Om du säkerhetskopierar eller återställer en virtuell dator eller grupp av virtuella datorer måste du inaktivera och återaktivera replikering för den virtuella datorn.  


