---
title: Översikt över Recovery Services-valv
description: En översikt och jämförelse av Recovery Services-valv och Azure Backup-valv.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/10/2018
ms.author: markgal
ms.openlocfilehash: 38190cc613ea9d3723cc3f0f563842e8416689aa
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241352"
---
# <a name="recovery-services-vaults-overview"></a>Översikt över Recovery Services-valv

Den här artikeln beskriver funktionerna i Recovery Services-valvet. Ett Recovery Services-valv är en enhet för lagring i Azure som innehåller data. Data är vanligtvis kopior av data eller konfigurationsinformation för virtuella datorer (VM), arbetsbelastningar, servrar eller arbetsstationer. Du kan använda Recovery Services-valv för att lagra säkerhetskopierade data för olika Azure-tjänster, till exempel virtuella IaaS-datorer (Linux eller Windows) och Azure SQL-databaser. Recovery Services-valv stöd för System Center DPM, Windows Server, Azure Backup Server och mer. Med Recovery Services-valv är det enkelt att organisera dina säkerhetskopierade data samtidigt som du minimerar hanteringskostnaden.

Du kan skapa upp till 500 Recovery Services-valv per prenumeration per region i en Azure-prenumeration.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Jämförelse Recovery Services-valv och Backup-valv

Om du fortfarande har Backup-valv som de ska uppgraderas automatiskt till Recovery Services-valv. Från November 2017 kommer alla säkerhetskopieringsvalv har uppgraderats till Recovery Services-valv.

Recovery Services-valv baseras på Azure Resource Manager-modellen i Azure, medan säkerhetskopieringsvalv baserades på Azure Service Manager-modellen. När du uppgraderar ett säkerhetskopieringsvalv till Recovery Services-valvet bevaras säkerhetskopierade data under och efter uppgraderingen. Recovery Services-valv innehåller funktioner som är inte tillgängligt för säkerhetskopieringsvalv, som:

- **Förbättrade funktioner för att säkra säkerhetskopieringsdata**: med Recovery Services-valv, Azure Backup tillhandahåller funktioner för säkerhet att skydda säkerhetskopior i molnet. Säkerhetsfunktionerna Kontrollera kan du skydda dina säkerhetskopior och på ett säkert sätt återställa data, även om produktions- och backup-servrar har komprometterats. [Läs mer](backup-azure-security-feature.md)

- **Central övervakning av din IT-hybridmiljöer**: med Recovery Services-valv kan du övervaka inte bara din [Azure IaaS-VM](backup-azure-manage-vms.md) utan även din [lokala tillgångar](backup-azure-manage-windows-server.md#manage-backup-items) från en central portal. [Läs mer](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Rollbaserad åtkomstkontroll (RBAC)**: RBAC ger detaljerad hantering av åtkomstkontroll i Azure. [Azure erbjuder olika inbyggda roller](../role-based-access-control/built-in-roles.md), och Azure Backup har tre [inbyggda roller för att hantera återställningspunkter](backup-rbac-rs-vault.md). Recovery Services-valv är kompatibla med RBAC, vilket begränsar säkerhetskopiering utan att återställa åtkomst till en definierad uppsättning användarroller. [Läs mer](backup-rbac-rs-vault.md)

- **Skydda alla konfigurationer av virtuella datorer i Azure**: Recovery Services-valv skyddar Resource Manager-baserade virtuella datorer, inklusive Premium Disks Managed Disks och krypterade virtuella datorer. Uppgradera ett säkerhetskopieringsvalv till Recovery Services-valvet ger dig möjlighet att uppgradera din Service Manager-baserade virtuella datorer till Resource Manager-baserade virtuella datorer. När du uppgraderar valvet, kan du behålla dina återställningspunkter för Service Manager-baserad virtuell dator och konfigurera skydd för uppgraderade (Resource Manager-aktiverade) virtuella datorer. [Läs mer](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Omedelbar återställning för virtuella IaaS-datorer**: med Recovery Services-valv kan du återställa filer och mappar från en IaaS-VM utan att återställa hela VM, vilket gör att återställningen går snabbare. Omedelbar återställning för virtuella IaaS-datorer är tillgänglig för både Windows och Linux-datorer. [Läs mer](https://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Hantera dina Recovery Services-valv i portalen
Skapa och hantera Recovery Services-valv i Azure-portalen är enkelt eftersom Backup-tjänsten integreras i andra Azure-tjänster. Den här integrationen innebär att du kan skapa eller hantera ett Recovery Services-valv *i samband med Måltjänsten*. Till exempel om du vill visa återställningspunkter för en virtuell dator, Välj den virtuella datorn och på **Backup** på menyn åtgärder.

![Recovery services-valvinformationen VM](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Om den virtuella datorn inte har säkerhetskopiering har konfigurerats, och sedan uppmanas du att konfigurera säkerhetskopiering. Om säkerhetskopieringen har konfigurerats visas säkerhetskopierad information om den virtuella datorn, inklusive en lista över återställningspunkter.  

![Recovery services-valvinformationen VM](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

I exemplet ovan **ContosoVM** är namnet på den virtuella datorn. **ContosoVM-demovault** är namnet på Recovery Services-valvet. Du behöver inte kommer ihåg namnet på Recovery Services-valvet som lagrar återställningspunkterna kan du komma åt den här informationen från den virtuella datorn.  

Om ett Recovery Services-valv skyddar flera servrar kan vara det mer logiskt att titta på Recovery Services-valvet. Du kan söka efter alla Recovery Services-valv i prenumerationen och väljer ett i listan.

Följande avsnitt innehåller länkar till artiklar som beskriver hur du använder ett Recovery Services-valv i varje typ av aktivitet.

> [!NOTE]
> Recovery Services-valv kan inte skapas med samma namn om det har tagits bort inom 24 timmar. Använd ett annat resursnamn eller välj en annan resursgrupp eller försök igen om 24 timmar.

### <a name="back-up-data"></a>Säkerhetskopiera data
- [Säkerhetskopiera en virtuell Azure-dator](backup-azure-vms-first-look-arm.md)
- [Säkerhetskopiera Windows Server eller Windows-arbetsstation](backup-try-azure-backup-in-10-mins.md)
- [Säkerhetskopiera DPM-arbetsbelastningar till Azure](backup-azure-dpm-introduction.md)
- [Förbereda säkerhetskopiering av arbetsbelastningar med Azure Backup Server](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Hantera återställningspunkter
- [Hantera Virtuella Azure-säkerhetskopieringar](backup-azure-manage-vms.md)
- [Hantera filer och mappar](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Återställa data från valvet
- [Återställa enskilda filer från en Azure-dator](backup-azure-restore-files-from-vm.md)
- [Återställa en Azure virtuell dator](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Skydda valvet
- [Skydda molnet säkerhetskopierade data i Recovery Services-valv](backup-azure-security-feature.md)



## <a name="next-steps"></a>Nästa steg
Använd följande artiklar för att:</br>
[Säkerhetskopiera en IaaS VM](backup-azure-arm-vms-prepare.md)</br>
[Säkerhetskopiera en Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Säkerhetskopiera en Windows-Server](backup-configure-vault.md)
