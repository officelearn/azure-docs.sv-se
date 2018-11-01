---
title: Vanliga frågor om säkerhetskopiering av virtuell Azure-dator
description: Svar på vanliga frågor om hur virtuell Azure-säkerhetskopiering fungerar, begränsningar och vad som händer när principen ändras
services: backup
author: trinadhk
manager: shreeshd
keywords: säkerhetskopiering av virtuella datorer i azure, återställning av virtuell dator i azure, säkerhetskopieringspolicy
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: trinadhk
ms.openlocfilehash: ba77ec34e7887f676ea3df101e87c1ea80fceec5
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414802"
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Frågor om tjänsten för säkerhetskopiering av virtuella datorer i Azure
Den här artikeln innehåller svar på vanliga frågor så att du snabbt kan förstå de komponenter som används i Azure-säkerhetskopieringen av virtuella datorer. I vissa svar finns det länkar till artiklar som har omfattande information. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Stöder Recovery Services-valv klassiska virtuella datorer eller Resource Manager-baserade virtuella datorer? <br/>
Recovery Services-valv stöder båda modellerna.  Du kan säkerhetskopiera en klassisk virtuell dator eller en Resource Manager-VM till ett Recovery Services-valv.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Vilka konfigurationer stöds inte av virtuell Azure-säkerhetskopiering?
Gå igenom [operativsystem som stöds](backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup) och [begränsningar för säkerhetskopiering](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Varför kan jag inte se min virtuella dator i guiden Konfigurera säkerhetskopiering?
I guiden Konfigurera säkerhetskopiering visar Azure Backup endast virtuella datorer som är:
  * Du kan inte redan skyddas för att kontrollera status för säkerhetskopiering av en virtuell dator genom att gå till VM-bladet och kontrollera status för säkerhetskopiering från menyn Inställningar. Lär dig mer om att [Kontrollera status för säkerhetskopiering av en virtuell dator](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu)
  * Tillhör samma region som den virtuella datorn

## <a name="backup"></a>Backup
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Kommer säkerhetskopiering på begäran att följa samma kvarhållningsschema som schemalagda säkerhetskopieringar?
Nej. Du måste ange kvarhållningsintervallet för en säkerhetskopiering på begäran. Som standard den finns kvar i 30 dagar när den utlöses från portalen.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Jag nyligen har aktiverat Azure Disk Encryption på vissa virtuella datorer. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Du måste ge behörigheterna för Azure Backup-tjänsten för att få åtkomst till nyckelvalvet. Du kan ange dessa behörigheter i PowerShell med hjälp av anvisningarna i avsnittet *Aktivera säkerhetskopiering* i [PowerShell](backup-azure-vms-automation.md)-dokumentationen.

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>Jag har migrerat diskar på en virtuell dator till hanterade diskar. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Ja, säkerhetskopieringen fungerar smidigt och du behöver inte konfigurera om säkerhetskopieringen.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Den virtuella datorn är avstängd. Kommer en på begäran eller arbete med en schemalagd säkerhetskopiering?
Ja. Även om en dator är avstängd säkerhetskopieringen fungerar och återställningspunkten är markerad som krascher konsekvent. Mer information finns i avsnittet data konsekvens i [i den här artikeln](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines)

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kan jag säga upp en pågående säkerhetskopiering?
Ja. Du kan avbryta jobbet om den finns i ”att möjliggöra ögonblicksbilder” fas. **Du kan inte avbryta ett jobb om dataöverföringar från ögonblicksbild håller på att**.

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>Jag har aktiverat resursgrupp lås på mitt säkerhetskopierade hanterade diskar för virtuella datorer. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Om användaren låser resursgruppen, kan Backup-tjänsten inte ta bort äldre återställningspunkter. På grund av detta nya säkerhetskopior börjar misslyckas eftersom det finns en gräns för maximal 18 återställningspunkter som införts från serverdelen. Om säkerhetskopieringarna misslyckas med ett internt fel när RG-lås, följer du dessa [steg för att ta bort återställningen samlingen med återställningspunkter](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-portal-created-by-backup-service).

### <a name="does-backup-policy-take-daylight-saving-timedst-into-account"></a>Tar säkerhetskopieringspolicyn sommartid sparar Time(DST) hänsyn?
Nej. Tänk på att datum och tid på den lokala datorn visas i din lokala tid och med din aktuella sommartid. Så kan den konfigurera tiden för schemalagda säkerhetskopieringar skilja sig från din lokala tid på grund av Sommartid.

### <a name="maximum-of-how-many-data-disks-can-i-attach-to-a-vm-to-be-backed-up-by-azure-backup"></a>Högst hur många datadiskar kan jag ansluta till en virtuell dator som ska säkerhetskopieras av Azure Backup?
Azure Backup har nu stöd för säkerhetskopiering av virtuella datorer med upp till 32 diskar. Att hämta 32 disksupport [uppgradera till säkerhetskopiering för Azure stack V2](backup-upgrade-to-vm-backup-stack-v2.md). Få kommer stöd för alla virtuella datorer att aktivera skydd som startar 24 september 2018.

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Azure stöd för säkerhetskopiering Standard SSD hanterad disk?
Azure Backup stöder [Standard SSD Managed Disks](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/), en ny typ av beständig lagring för Microsoft Azure virtuella datorer. Det finns stöd för hanterade diskar på [säkerhetskopiering för Azure stack V2](backup-upgrade-to-vm-backup-stack-v2.md).

## <a name="restore"></a>Återställ
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Hur gör jag för att välja mellan diskåterställning och fullständig återställning av en virtuell dator?
Tänk på Azure fullständig återställning av virtuella datorer som ett alternativ för Snabbregistrering. Återställ VM alternativet ändringar namnen på diskar, behållare som används av dessa diskar, offentliga IP-adresser och namn för nätverksgränssnitt. Ändringen krävs för att upprätthålla unikhet för resurser som skapades under skapandet av VM. Men det kommer inte att lägga till den virtuella datorn i tillgänglighetsuppsättningen.

Använd återställningsdiskar för att göra följande:
  * Anpassa den virtuella datorn som skapas från tidpunktskonfigurationen som ändrar storlek på
  * Lägg till konfigurationer som inte är tillgängliga vid tidpunkten för säkerhetskopieringen
  * Kontrollen namngivningskonventionen för resurser som skapas
  * Lägg till virtuell dator i tillgänglighetsuppsättningen
  * För en annan konfiguration som kan uppnås endast med hjälp av PowerShell/en deklarativ malldefinition

### <a name="can-i-use-backups-of-unmanaged-disk-vm-to-restore-after-i-upgrade-my-disks-to-managed-disks"></a>Kan jag använda säkerhetskopior av ohanterad disk VM för att återställa efter att jag uppgradera min diskar till managed disks?
Ja, du kan använda de säkerhetskopior som har gjorts innan du migrerar diskar från ohanterade till hanterade. Som standard skapar VM återställningsjobbet en virtuell dator med ohanterade diskar. Du kan använda diskar återställningsfunktionen för att återställa diskar och använda dem för att skapa en virtuell dator på hanterade diskar.

### <a name="what-is-the-procedure-to-restore-a-vm-to-a-restore-point-taken-before-the-conversion-from-unmanaged-to-managed-disks-was-done-for-a-vm"></a>Vad är proceduren för att återställa en virtuell dator till en återställningspunkt som vidtas innan konverteringen från ohanterade till hanterade diskar har utförts för en virtuell dator?
I det här scenariot skapar som standard VM återställningsjobbet en virtuell dator med ohanterade diskar. Skapa en virtuell dator med hanterade diskar:
1. [Återställa till ohanterade diskar](tutorial-restore-disk.md#restore-a-vm-disk)
2. [Konvertera de återställda diskarna till managed disks](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)
3. [Skapa en virtuell dator med hanterade diskar](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk) <br>
PowerShell-cmdletar finns [här](backup-azure-vms-automation.md#restore-an-azure-vm).

### <a name="can-i-restore-the-vm-if-my-vm-is-deleted"></a>Kan jag återställa den virtuella datorn om den virtuella datorn tas bort?
Ja. Livscykel för den virtuella datorn och dess motsvarande säkerhetskopieringsobjekt är olika. Så även om du tar bort den virtuella datorn går du till motsvarande Säkerhetskopiera objekt i Recovery Services-valvet och aktivera en återställning med någon av återställningspunkterna.

## <a name="manage-vm-backups"></a>Hantera säkerhetskopior av virtuella datorer
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Vad händer om jag ändrar en säkerhetskopieringspolicy på virtuella datorer?
När en ny princip har tillämpats på virtuella datorer följs schemat för och kvarhållningen av den nya principen. Om kvarhållningen utökas markeras befintliga återställningspunkter för att behålla dem enligt den nya principen. Om kvarhållningen minskar markeras de för rensning under nästa rensningsjobb och tas sedan bort.

### <a name="how-can-i-move-a-vm-enrolled-in-azure-backup-between-resource-groups"></a>Hur kan jag flytta en virtuell dator har registrerats i Azure backup mellan resursgrupper?
Följ de nedanstående steg för att flytta den säkerhetskopierade virtuella datorn till målresursgruppen
1. Tillfälligt stoppa säkerhetskopiering och behåller säkerhetskopierade data
2. Flytta den virtuella datorn till målresursgruppen
3. Skydda den igen med samma/nytt valv

Användare kan återställa från tillgängliga återställningspunkter som skapats före flyttåtgärden.
