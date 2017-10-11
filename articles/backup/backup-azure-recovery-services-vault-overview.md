---
title: "Översikt över Recovery Services-valv | Microsoft Docs"
description: "En översikt och jämförelse mellan Recovery Services-valv och Azure Backup-valv."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.assetid: 38d4078b-ebc8-41ff-9bc8-47acf256dc80
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/15/2017
ms.author: markgal;arunak
ms.openlocfilehash: 19e2aafe3de106be32f3d90c63c0ea03c626f272
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="recovery-services-vaults-overview"></a>Översikt över Recovery Services-valv

Den här artikeln beskriver funktionerna i Recovery Services-valvet. Recovery Services-valvet är en enhet för lagring i Azure som innehåller data. Data är vanligtvis kopior av data eller konfigurationsinformation för virtuella datorer (VM), arbetsbelastningar, servrar eller arbetsstationer. Recovery Services-valvet är Resource Manager-version av en Backup-valvet. Microsoft rekommenderar att du att använda Recovery Services-valv och konvertera alla säkerhetskopieringsvalv till Recovery Services-valv.

## <a name="what-is-a-recovery-services-vault"></a>Vad är ett Recovery Services-valv?

Ett Recovery Services-valv är en onlinelagringsentitet i Azure som används för att lagra data, som säkerhetskopior, återställningspunkter och principer för säkerhetskopiering. Du kan använda Recovery Services-valv för att lagra säkerhetskopierade data för olika Azure-tjänster, till exempel virtuella IaaS-datorer (Linux eller Windows) och Azure SQL-databaser. Recovery Services valv stöd för System Center DPM, Windows Server, Azure Backup Server och mer. Med Recovery Services-valv är det enkelt att organisera dina säkerhetskopierade data samtidigt som du minimerar hanteringskostnaden.

Du kan skapa så många Recovery Services-valv som helst inom en Azure-prenumeration.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Jämför Recovery Services-valv och säkerhetskopieringsvalv

Recovery Services-valv baseras på Azure Resource Manager-modell för Azure-säkerhetskopieringsvalv bygger på Azure Service Manager-modellen. När du uppgraderar ett säkerhetskopieringsvalv till Recovery Services-valvet bevaras säkerhetskopierade data under och efter uppgraderingen. Recovery Services-valv innehåller funktioner som är inte tillgängligt för säkerhetskopieringsvalv, som:

- **Förbättrade funktioner för att säkra säkerhetskopierade data**: med Recovery Services-valv, Azure Backup tillhandahåller säkerhetsfunktioner för att skydda molnet säkerhetskopieringar. Dessa funktioner Se till att du kan skydda dina säkerhetskopieringar och på ett säkert sätt återställa data från molnet säkerhetskopior, även om produktions- och backup-servrar som har angripits. [Läs mer](backup-azure-security-feature.md)

- **Central övervakning av din IT-miljö för hybrid**: med Recovery Services-valv kan du övervaka inte bara din [Azure IaaS-VM](backup-azure-manage-vms.md) utan även din [lokala tillgångar](backup-azure-manage-windows-server.md#manage-backup-items) från en central portalen. [Läs mer](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Rollbaserad åtkomstkontroll (RBAC)**: RBAC ger detaljerade management åtkomstkontroll i Azure. [Azure tillhandahåller olika inbyggda roller](../active-directory/role-based-access-built-in-roles.md), och Azure Backup har tre [inbyggda roller för att hantera återställningspunkter](backup-rbac-rs-vault.md). Recovery Services-valv är kompatibla med RBAC som begränsar säkerhetskopia och återställa åtkomst till en definierad uppsättning användarroller. [Läs mer](backup-rbac-rs-vault.md)

- **Skydda alla konfigurationer av virtuella datorer i Azure**: Recovery Services-valv skydda Resource Manager-baserade virtuella datorer inklusive Premiumdiskar, hanterade diskar och krypterade virtuella datorer. Uppgradera ett säkerhetskopieringsvalv till Recovery Services-valvet ger dig möjlighet att uppgradera din Service Manager-baserade virtuella datorer till Resource Manager-baserade virtuella datorer. Du kan behålla dina återställningspunkter för Service Manager-baserade Virtuella och konfigurera skydd för uppgraderade (Resource Manager-aktiverat) virtuella datorer under uppgraderingen valvet. [Läs mer](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Omedelbar återställning för IaaS-VM**: med Recovery Services-valv kan du återställa filer och mappar från en IaaS-VM utan att återställa hela den virtuella datorn, vilket möjliggör snabbare återställning. Omedelbar återställning för IaaS-VM är tillgänglig för både Windows- och Linux virtuella datorer. [Läs mer](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Hantera din Recovery Services-valv i portalen
Skapande och hantering av Recovery Services-valv i Azure-portalen är enkelt eftersom Backup-tjänsten är integrerat i Azure-inställningar-bladet. Den här integreringen innebär att du kan skapa eller hantera ett Recovery Services-valv *i samband med Måltjänsten*. Till exempel om du vill visa återställningspunkter för en virtuell dator, markerar du den och klickar på **säkerhetskopiering** i bladet inställningar. Säkerhetskopierad information som är specifika för den virtuella datorn visas. I följande exempel **ContosoVM** är namnet på den virtuella datorn. **ContosoVM demovault** är namnet på Recovery Services-valvet. Du behöver inte ihåg namnet på Recovery Services-valvet som lagrar återställningspunkterna, du kan komma åt den här informationen från den virtuella datorn.  

![Recovery services-valvet information VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context.png)

Om flera servrar skyddas med hjälp av samma Recovery Services-valvet, kan det vara flera logiska titta på Recovery Services-valvet. Du kan söka efter alla Recovery Services-valv i prenumerationen och välj en från listan.

Följande avsnitt innehåller länkar till artiklar som förklarar hur du använder ett Recovery Services-valv i varje typ av aktivitet.

### <a name="back-up-data"></a>Säkerhetskopiera data
- [Säkerhetskopiera en Azure VM](backup-azure-vms-first-look-arm.md)
- [Säkerhetskopiera Windows Server eller Windows-arbetsstation](backup-try-azure-backup-in-10-mins.md)
- [Säkerhetskopiera DPM-arbetsbelastningar till Azure](backup-azure-dpm-introduction.md)
- [Förbereda för att säkerhetskopiera arbetsbelastningar med Azure Backup Server](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Hantera återställningspunkter
- [Hantera Virtuella Azure-säkerhetskopiering](backup-azure-manage-vms.md)
- [Hantera filer och mappar](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Återställa data från valvet
- [Återställa enskilda filer från en Azure VM](backup-azure-restore-files-from-vm.md)
- [Återställa en virtuell dator i Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Skydda valvet
- [Skydda säkerhetskopierade data från molnet i Recovery Services-valv](backup-azure-security-feature.md)



## <a name="next-steps"></a>Nästa steg
Använd följande artikel för:</br>
[Säkerhetskopiera en IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[Säkerhetskopiera en Azure Backup-Server](backup-azure-microsoft-azure-backup.md)</br>
[Säkerhetskopiera Windows Server](backup-configure-vault.md)
