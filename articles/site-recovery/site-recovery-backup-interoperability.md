---
title: Azure Site Recovery - säkerhetskopiering samverkan | Microsoft Docs
description: Innehåller en översikt över hur Azure Site Recovery och Azure Backup kan användas tillsammans.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/26/2019
ms.author: sideeksh
ms.openlocfilehash: 6658ab8c967c70ac1deaeba3d1dfeac602515591
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731878"
---
# <a name="about-site-recovery-and-backup-interoperability"></a>Om Site Recovery och säkerhetskopiering samverkan

Den här artikeln innehåller riktlinjer för säkerhetskopiering av Virtuella Azure IaaS och Virtuella Azure-haveriberedskap med.

## <a name="azure-backup"></a>Azure Backup

Azure Backup kan skydda data för lokala servrar, virtuella datorer, virtualiserade arbetsbelastningar, SQL-servrar, SharePoint-servrar och mer. Azure Site Recovery dirigerar och hanterar haveriberedskap för virtuella Azure-datorer, lokala virtuella datorer och fysiska servrar.

## <a name="azure-site-recovery"></a>Azure Site Recovery

Det är möjligt att konfigurera både Azure Backup och Azure Site Recovery på en virtuell dator eller en grupp virtuella datorer. Båda produkterna är kompatibla. Några scenarier där samverkan mellan Backup och Azure Site Recovery viktigare är följande:

### <a name="file-backuprestore"></a>Filen säkerhetskopiering/återställning

Om säkerhetskopiering och replikering finns både aktiverat och en säkerhetskopia görs, är det något problem med att återställa alla filer på käll-VM eller en grupp av virtuella datorer. Replikering fortsätter som vanligt med ingen ändring i replikeringen.

### <a name="disk-backuprestore"></a>Disk säkerhetskopiering/återställning

Om du vill återställa disken från säkerhetskopian har skyddet av den virtuella datorn aktiveras igen.

### <a name="vm-backuprestore"></a>VM-säkerhetskopiering/återställning

Säkerhetskopiering och återställning av en virtuell dator eller en grupp med virtuella datorer stöds inte. Skydd måste aktiveras på nytt så att den fungerar.

**Scenario** | **Stöds av Azure Site Recovery?** | **Lösning, om sådana**  
--- | --- | ---
Säkerhetskopiering av fil/mapp | Ja | Inte tillämpligt
Säkerhetskopiering på disk | För närvarande inte | Inaktivera och aktivera skydd
Säkerhetskopiering av virtuella datorer | Nej | Inaktivera och aktivera skydd
