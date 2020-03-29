---
title: Översikt över Recovery Services-valv
description: En översikt och jämförelse mellan Recovery Services-valv och Azure Backup-valv.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 94a3e5a0865bcc8c0a9ecb866ca013f20a558e1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673244"
---
# <a name="recovery-services-vaults-overview"></a>Översikt över Recovery Services-valv

I den här artikeln beskrivs funktionerna i ett Recovery Services-valv. Ett Recovery Services-valv är en lagringsenhet i Azure som rymmer data. Data är vanligtvis kopior av data eller konfigurationsinformation för virtuella datorer, arbetsbelastningar, servrar eller arbetsstationer. Du kan använda Recovery Services-valv för att lagra säkerhetskopierade data för olika Azure-tjänster som virtuella IaaS-datorer (Linux eller Windows) och Azure SQL-databaser. Recovery Services-valv stöder System Center DPM, Windows Server, Azure Backup Server med mera. Med Recovery Services-valv är det enkelt att organisera dina säkerhetskopierade data samtidigt som du minimerar hanteringskostnaden.

Inom en Azure-prenumeration kan du skapa upp till 500 Recovery Services-valv per prenumeration per region.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Jämföra Recovery Services-valv och säkerhetskopieringsvalv

Om du fortfarande har valv för säkerhetskopiering uppgraderas de automatiskt till Recovery Services-valv. I november 2017 har alla säkerhetskopieringsvalv uppgraderats till Recovery Services-valv.

Recovery Services-valv baseras på Azure Resource Manager-modellen för Azure, medan säkerhetskopieringsvalv baserades på Azure Service Manager-modellen. När du uppgraderar ett valv för säkerhetskopiering till ett Recovery Services-valv förblir säkerhetskopieringsdata intakta under och efter uppgraderingsprocessen. Recovery Services-valv innehåller funktioner som inte är tillgängliga för valv för säkerhetskopiering, till exempel:

- **Förbättrade funktioner för att skydda säkerhetskopieringsdata:** Med Recovery Services-valv tillhandahåller Azure Backup säkerhetsfunktioner för att skydda säkerhetskopieringar i molnet. Säkerhetsfunktionerna säkerställer att du kan skydda dina säkerhetskopior och återställa data på ett säkert sätt, även om produktions- och säkerhetskopieringsservrar äventyras. [Läs mer](backup-azure-security-feature.md)

- **Central övervakning för din hybrid-IT-miljö:** Med Recovery Services-valv kan du övervaka inte bara dina [virtuella Azure IaaS-datorer](backup-azure-manage-vms.md) utan även dina [lokala resurser](backup-azure-manage-windows-server.md#manage-backup-items) från en central portal. [Läs mer](https://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Rollbaserad åtkomstkontroll (RBAC):** RBAC ger detaljerad åtkomsthanteringskontroll i Azure. [Azure tillhandahåller olika inbyggda roller](../role-based-access-control/built-in-roles.md)och Azure Backup har tre [inbyggda roller för att hantera återställningspunkter](backup-rbac-rs-vault.md). Recovery Services-valv är kompatibla med RBAC, vilket begränsar säkerhetskopiering och återställning av åtkomst till den definierade uppsättningen användarroller. [Läs mer](backup-rbac-rs-vault.md)

- **Skydda alla konfigurationer av virtuella Azure-datorer:** Recovery Services-valv skyddar Resource Manager-baserade virtuella datorer, inklusive Premium-diskar, hanterade diskar och krypterade virtuella datorer. Om du uppgraderar ett valv för säkerhetskopiering till ett Recovery Services-valv kan du uppgradera dina Service Manager-baserade virtuella datorer till Resource Manager-baserade virtuella datorer. När du uppgraderar valvet kan du behålla dina Service Manager-baserade VM-återställningspunkter och konfigurera skydd för de uppgraderade (Resource Manager-aktiverade) virtuella datorerna. [Läs mer](https://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Omedelbar återställning för virtuella IaaS-datorer:** Med hjälp av Recovery Services-valv kan du återställa filer och mappar från en IaaS-vm utan att återställa hela den virtuella datorn, vilket möjliggör snabbare återställningstider. Omedelbar återställning för virtuella IaaS-datorer är tillgänglig för både virtuella Datorer i Windows och Linux. [Läs mer](backup-instant-restore-capability.md)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Hantera dina Recovery Services-valv i portalen

Det är enkelt att skapa och hantera Recovery Services-valv i Azure-portalen eftersom säkerhetskopieringstjänsten integreras i andra Azure-tjänster. Den här integreringen innebär att du kan skapa eller hantera ett Recovery Services-valv *i samband med måltjänsten*. Om du till exempel vill visa återställningspunkterna för en virtuell dator väljer du den virtuella datorn och klickar på **Säkerhetskopiering** på Operations-menyn.

![Valv för återställningstjänster vm-information om återställningstjänster](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context-vm.png)

Om den virtuella datorn inte har konfigurerat säkerhetskopian uppmanas du att konfigurera säkerhetskopiering. Om säkerhetskopiering har konfigurerats visas information om säkerhetskopiering om den virtuella datorn, inklusive en lista över återställningspunkter.  

![Valv för återställningstjänster vm-information om återställningstjänster](./media/backup-azure-recovery-services-vault-overview/vm-recovery-point-list.png)

I föregående exempel är **ContosoVM** namnet på den virtuella datorn. **ContosoVM-demovault** är namnet på Recovery Services-valvet. Du behöver inte komma ihåg namnet på recovery services-valvet som lagrar återställningspunkterna, du kan komma åt den här informationen från den virtuella datorn.  

Om ett Recovery Services-valv skyddar flera servrar kan det vara mer logiskt att titta på valvet för Återställningstjänster. Du kan söka efter alla Recovery Services-valv i prenumerationen och välja ett i listan.

Följande avsnitt innehåller länkar till artiklar som förklarar hur du använder ett Recovery Services-valv i varje typ av aktivitet.

> [!NOTE]
> Valvet för återställningstjänster kan inte skapas med samma namn om det har tagits bort inom 24 timmar. Använd ett annat resursnamn eller välj en annan resursgrupp eller försök igen efter 24 timmar.

### <a name="back-up-data"></a>Säkerhetskopiera data

- [Säkerhetskopiera en virtuell Azure-dator](backup-azure-vms-first-look-arm.md)
- [Säkerhetskopiera Windows Server eller Windows-arbetsstation](backup-try-azure-backup-in-10-mins.md)
- [Säkerhetskopiera DPM-arbetsbelastningar till Azure](backup-azure-dpm-introduction.md)
- [Förbereda säkerhetskopiering av arbetsbelastningar med Azure Backup Server](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Hantera återställningspunkter

- [Hantera säkerhetskopior av virtuella Azure-datorer](backup-azure-manage-vms.md)
- [Hantera filer och mappar](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Återställa data från valvet

- [Återställa enskilda filer från en virtuell Azure-dator](backup-azure-restore-files-from-vm.md)
- [Återställa en virtuell Azure-dator](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Säkra valvet

- [Skydda molnsäkerhetsdata för säkerhetskopiering i Recovery Services-valv](backup-azure-security-feature.md)

## <a name="next-steps"></a>Nästa steg

Använd följande artiklar för att:</br>
[Säkerhetskopiera en virtuell IaaS-dator](backup-azure-arm-vms-prepare.md)</br>
[Säkerhetskopiera en Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Säkerhetskopiera en Windows Server](backup-windows-with-mars-agent.md)
