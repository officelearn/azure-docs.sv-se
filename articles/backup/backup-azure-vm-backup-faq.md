---
title: Vanliga frågor om säkerhetskopiering av virtuell Azure-dator
description: Svar på vanliga frågor om hur virtuell Azure-säkerhetskopiering fungerar, begränsningar och vad som händer när principen ändras
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: 55fbc997f6aaf4cf99bfb8492a2e4ac608c72af8
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494909"
---
# <a name="frequently-asked-questions-azure-backup"></a>Vanliga frågor – Azure Backup

Den här artikeln innehåller vanliga frågor och svar om den [Azure Backup](backup-introduction-to-azure-backup.md) service.

## <a name="general-questions"></a>Allmänna frågor


### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>Vilka virtuella datorer i Azure kan du säkerhetskopiera med hjälp av Azure Backup?
[Granska](backup-azure-arm-vms-prepare.md#before-you-start) operativsystem och begränsningar som stöds.



## <a name="backup"></a>Backup

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Använder en säkerhetskopiering på begäran samma kvarhållningsschema som schemalagda säkerhetskopieringar?
Nej. Du måste ange kvarhållningsintervallet för en säkerhetskopiering på begäran. Som standard den finns kvar i 30 dagar när den utlöses från portalen.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Jag nyligen har aktiverat Azure Disk Encryption på vissa virtuella datorer. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Du måste ange behörigheter för Azure Backup för att få åtkomst till Key Vault. Ange behörigheter i PowerShell enligt beskrivningen i den **Aktivera säkerhetskopiering** i avsnittet den [Azure Backup PowerShell](backup-azure-vms-automation.md) dokumentation.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Jag har migrerat Virtuella datordiskar till hanterade diskar. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Ja, säkerhetskopieringen fungerar smidigt. Det finns inget behov att konfigurera om vad som helst.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Varför kan jag inte se min virtuella dator i guiden Konfigurera säkerhetskopiering?
Guiden visar endast virtuella datorer i samma region som valvet och som inte redan som säkerhetskopieras.


### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Den virtuella datorn är avstängd. Kommer en på begäran eller arbete med en schemalagd säkerhetskopiering?
Ja. Säkerhetskopieringar som körs när en dator är avstängd. Återställningspunkten har markerats som krascher konsekvent.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kan jag säga upp en pågående säkerhetskopiering?
Ja. Du kan avbryta säkerhetskopieringsjobbet på en **tar ögonblicksbild** tillstånd. Du kan inte avbryta ett jobb om dataöverföring från ögonblicksbilden håller på att skapas.

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>Jag har aktiverat grupp resurslås på mitt säkerhetskopierade hanterade diskar för virtuella datorer. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Om du låser resursgruppen, Azure Backup-tjänsten kan inte ta bort äldre återställningspunkter.
- Nya säkerhetskopior börjar misslyckas eftersom det inte finns en maxgräns på 18 återställningspunkter.
- Om säkerhetskopieringen misslyckas med ett internt fel när lås, [gör så här](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) att ta bort samlingen med återställningspunkter.

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>Betraktar säkerhetskopieringspolicyn sommartid (DST)?
Nej. Datum och tid på den lokala datorn är lokal med aktuella sommartid används. Den tid som angetts för schemalagda säkerhetskopieringar kan skilja sig från den lokala tiden på grund av Sommartid.

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>Hur många datadiskar kan jag ansluta till en virtuell dator som backas upp av Azure Backup?
Azure Backup kan säkerhetskopiera virtuella datorer med upp till 16 diskar. Stöd för 16 diskar finns i den [omedelbar återställning](backup-instant-restore-capability.md).

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Har Azure stöd för säkerhetskopiering SSD hanterade standarddiskar?
Azure Backup stöder [standard SSD-hanterade diskar](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). SSD managed disks ger en ny typ av beständig lagring för virtuella Azure-datorer. Stöd för SSD hanterade diskar finns i den [omedelbar återställning](backup-instant-restore-capability.md).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Kan vi säkerhetskopiera en virtuell dator med en WA Write Accelerator-aktiverade disk?
Ögonblicksbilder kan inte utföras på disken WA-aktiverade. Azure Backup-tjänsten kan dock utesluta WA-aktiverade disken från en säkerhetskopia. Disk-undantag för virtuella datorer med WA-aktiverade diskar stöds endast för prenumerationer som uppgraderas till omedelbar återställning.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Jag har en virtuell dator med Write Accelerator (WA) diskar och SAP HANA installerad. Hur kan jag säkerhetskopiera?
Azure Backup kan inte säkerhetskopiera disken WA-aktiverade men kan undanta den från en säkerhetskopia. Säkerhetskopian Ange inte dock databaskonsekvens eftersom informationen på WA-aktiverade inte har säkerhetskopierats. Du kan säkerhetskopiera diskar med den här konfigurationen om du vill att operativsystemdisken, och säkerhetskopiering av diskar som inte är WA-aktiverade.

Vi har en privat förhandsgranskning för en SAP HANA-säkerhetskopia med ett Återställningspunktmål på 15 minuter. Den bygger på ett liknande sätt till SQL DB-säkerhetskopiering och använder gränssnittet backInt för lösningar från tredje part som certifierats av SAP HANA. Om du är intresserad av den privata förhandsgranskningen kan du maila ` AskAzureBackupTeam@microsoft.com ` med ämnet **registrera dig för privat förhandsgranskning för säkerhetskopiering av SAP HANA på Azure virtuella datorer i**.


## <a name="restore"></a>Återställ

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hur avgör jag om du vill återställa endast diskar eller en fullständig virtuell dator?
Tänk på en återställning av virtuella datorer som ett alternativ för Snabbregistrering för en Azure virtuell dator. Det här alternativet ändras disk namn, behållare som används av diskar, offentliga IP-adresser och namn för nätverksgränssnitt. Ändringen har unika resurser när en virtuell dator skapas. Den virtuella datorn inte har lagts till i en tillgänglighetsuppsättning.

Du kan använda alternativet Återställ disk om du vill:
  * Anpassa den virtuella datorn som skapas. Till exempel ändra storlek.
  * Lägg till konfigurationsinställningar som inte var det vid tidpunkten för säkerhetskopiering
  * Kontrollen Namngivningskonventionen för resurser som skapas.
  * Lägg till den virtuella datorn i en tillgänglighetsuppsättning.
  * Lägga till någon annan inställning som måste konfigureras med PowerShell eller en mall.  w

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Kan jag återställa säkerhetskopior av ohanterade Virtuella datordiskar efter uppgraderingen till managed disks?
Ja, du kan använda säkerhetskopior som har gjorts innan diskar har migrerats från ohanterade till hanterade.
- Som standard skapar en ohanterad virtuell dator i en virtuell dator återställningsjobbet.
- Du kan dock återställa diskar och använda dem för att skapa en hanterad virtuell dator.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hur återställer jag en virtuell dator till en återställningspunkt innan den virtuella datorn har migrerats till managed disks?
Som standard skapar en virtuell dator med ohanterade diskar i en virtuell dator återställningsjobbet. Skapa en virtuell dator med hanterade diskar:
1. [Återställa till ohanterade diskar](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Konvertera de återställda diskarna till managed disks](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Skapa en virtuell dator med hanterade diskar](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Läs mer](backup-azure-vms-automation.md#restore-an-azure-vm) om hur du gör detta i PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Kan jag återställa den virtuella datorn som har tagits bort?
Ja. Även om du tar bort den virtuella datorn går du till motsvarande säkerhetskopiering objektet i valvet och återställa från en återställningspunkt.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Hur du återställer en virtuell dator med samma tillgänglighetsuppsättningar?
När du återställer till tillgänglighetsuppsättningar är aktiverat för hanterad Disk Azure-dator, genom att tillhandahålla ett alternativ i mallen vid återställning som hanterade diskar. Den här mallen har den indataparameter med namnet **tillgänglighetsuppsättningar**.

### <a name="how-do-we-get-faster-restore-performances"></a>Hur får vi snabbare återställning prestanda?
För snabbare återställningsprestanda vi flyttar till [omedelbar återställning](backup-instant-restore-capability.md) kapaciteten.

## <a name="manage-vm-backups"></a>Hantera säkerhetskopior av virtuella datorer

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Vad händer om jag ändrar en princip för säkerhetskopiering?
Den virtuella datorn säkerhetskopieras med schema och kvarhållning inställningarna i den ändrade eller nya principen.

- Om kvarhållningen utökas markeras befintliga återställningspunkter och sparas i enlighet med den nya principen.
- Om kvarhållningen minskar är återställningspunkter markerad för rensning under nästa rensningsjobb och tas sedan bort.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hur gör jag för att flytta en virtuell dator som backas upp av Azure Backup till en annan resursgrupp?

1. Tillfälligt stoppa säkerhetskopiering och behåller säkerhetskopierade data.
2. Flytta den virtuella datorn till målresursgruppen.
3. Reenabled säkerhetskopiering i samma eller ett nytt valv.

Du kan återställa den virtuella datorn från tillgängliga återställningspunkter som skapades innan åtgärden för att flytta.
