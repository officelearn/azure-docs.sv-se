---
title: Vanliga frågor och svar om säkerhetskopiering av virtuella datorer i Azure | Microsoft Docs
description: Svar på vanliga frågor om hur virtuell Azure-säkerhetskopiering fungerar, begränsningar och vad som händer när principen ändras
services: backup
documentationcenter: ''
author: trinadhk
manager: shreeshd
editor: ''
keywords: säkerhetskopiering av virtuella datorer i azure, återställning av virtuell dator i azure, säkerhetskopieringspolicy
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: e0337a7ce1392d78eba9791095f5d7a9c7d4afdd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Frågor om tjänsten för säkerhetskopiering av virtuella datorer i Azure
Den här artikeln innehåller svar på vanliga frågor så att du snabbt kan förstå de komponenter som används i Azure-säkerhetskopieringen av virtuella datorer. I vissa svar finns det länkar till artiklar som har omfattande information. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Stöder Recovery Services-valv klassiska virtuella datorer eller Resource Manager-baserade virtuella datorer? <br/>
Recovery Services-valv stöder båda modellerna.  Du kan säkerhetskopiera en klassisk virtuell dator (som skapats på den klassiska portalen) eller en virtuell Resource Manager-dator (som skapats på Azure Portal) till ett Recovery Services-valv.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup"></a>Vilka konfigurationer stöds inte av Virtuella Azure-säkerhetskopiering?
Gå igenom [operativsystem](backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup) och [begränsningar för säkerhetskopiering](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Varför kan jag inte se min virtuella dator i guiden Konfigurera säkerhetskopiering?
Konfigurera säkerhetskopiering i guiden för visas Azure Backup endast virtuella datorer som är:
  * Du kan inte redan skyddad för att kontrollera status för säkerhetskopiering av en virtuell dator genom att gå till VM-bladet och kontrollera status för säkerhetskopiering från menyn Inställningar. Lär dig mer om att [Kontrollera status för säkerhetskopiering av en virtuell dator](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-operations-menu)
  * Tillhör samma region som den virtuella datorn

## <a name="backup"></a>Backup
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Kommer säkerhetskopiering på begäran att följa samma kvarhållningsschema som schemalagda säkerhetskopieringar?
Nej. Du måste ange kvarhållningsintervallet för en säkerhetskopiering på begäran. Som standard finns kvar i 30 dagar när den utlöses från portalen. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Jag nyligen har aktiverat Azure Disk Encryption på vissa virtuella datorer. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Du måste ge behörigheterna för Azure Backup-tjänsten för att få åtkomst till nyckelvalvet. Du kan ange dessa behörigheter i PowerShell med hjälp av anvisningarna i avsnittet *Aktivera säkerhetskopiering* i [PowerShell](backup-azure-vms-automation.md)-dokumentationen.

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>Jag har migrerat diskar på en virtuell dator till hanterade diskar. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Ja, säkerhetskopieringen fungerar smidigt och du behöver inte konfigurera om säkerhetskopieringen. 

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Den virtuella datorn är avstängd. Kommer en på-begäran eller en schemalagd säkerhetskopiering arbete?
Ja. Även om en dator är avstängd säkerhetskopieringen fungerar och återställningspunkten har markerats som krascher konsekvent. Mer information finns i avsnittet för konsekvenskontroll av data i [i den här artikeln](backup-azure-vms-introduction.md#how-does-azure-back-up-virtual-machines)

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kan jag avbryta en pågående säkerhetskopiering?
Ja. Du kan avbryta jobbet om den finns i ”ta ögonblicksbilder” fas. **Du kan inte avbryta ett jobb om dataöverföring från en ögonblicksbild pågår**. 

### <a name="i-enabled-resource-group-lock-on-my-backed-up-managed-disk-vms-will-my-backups-continue-to-work"></a>Jag har aktiverat resursgruppen lås på min säkerhetskopierade hanterade diskar för virtuella datorer. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Om användaren låser resursgruppen, kan Backup-tjänsten inte ta bort äldre återställningspunkter. På grund av detta starta nya säkerhetskopior misslyckas eftersom det finns en gräns på högsta 18 återställningspunkter införts från serverdelen. Om säkerhetskopieringarna misslyckas med ett internt fel när RG låset, följer du dessa [steg för att ta bort återställningen peka samlingen](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#backup-service-does-not-have-permission-to-delete-the-old-restore-points-due-to-resource-group-lock).

## <a name="restore"></a>Återställ
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Hur gör jag för att välja mellan diskåterställning och fullständig återställning av en virtuell dator?
Se Azure fullständig VM återställning som ett alternativ för Snabbregistrering. Återställa Virtuella alternativet ändringar namnen på diskar och behållare som används av dessa diskar, offentliga IP-adresser och namn för nätverksgränssnitt. Ändringen krävs att underhålla unika resurser som skapades under skapande av virtuell dator. Men det kommer inte att lägga till den virtuella datorn till tillgänglighetsuppsättningen. 

Använd återställningsdiskar för att göra följande:
  * Anpassa den virtuella datorn som skapas från punkten i konfiguration av som ändrar storlek på
  * Lägger till konfigurationer som inte är tillgänglig vid tidpunkten för säkerhetskopieringen 
  * Kontrollen namngivningskonventionen för resurser som skapas
  * Lägg till virtuell dator i tillgänglighetsuppsättningen
  * För andra konfiguration som kan ske via PowerShell/en deklarativ mall definition
  
### <a name="can-i-use-backups-of-unmanaged-disk-vm-to-restore-after-i-upgrade-my-disks-to-managed-disks"></a>Kan jag använda säkerhetskopior av ohanterade disk VM för att återställa efter att du har uppgraderat min diskar till hanterade diskar?
Ja, du kan använda de säkerhetskopior som har gjorts innan du migrerar diskar från ohanterade till hanterade. Standard skapar VM återställningsjobbet en virtuell dator med ohanterad diskar. Du kan använda restore diskar funktionen för att återställa diskar och använda dem för att skapa en virtuell dator på hanterade diskar. 

### <a name="what-is-the-procedure-to-restore-a-vm-to-a-restore-point-taken-before-the-conversion-from-unmanaged-to-managed-disks-was-done-for-a-vm"></a>Vad är proceduren för att återställa en virtuell dator till en återställningspunkt innan konverteringen från ohanterade till hanterade diskar har utförts för en virtuell dator?
I det här scenariot skapar som standard återställningsjobbet VM en virtuell dator med ohanterad diskar. Skapa en virtuell dator med hanterade diskar:
1. [Återställa till ohanterad diskar](tutorial-restore-disk.md#restore-a-vm-disk)
2. [Konvertera återställda diskar till hanterade diskar](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)
3. [Skapa en virtuell dator med hanterade diskar](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk) <br>
Powershell-cmdlets finns [här](backup-azure-vms-automation.md#restore-an-azure-vm).

## <a name="manage-vm-backups"></a>Hantera säkerhetskopior av virtuella datorer
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Vad händer om jag ändrar en säkerhetskopieringspolicy på virtuella datorer?
När en ny princip tillämpas på virtuella datorer, följs schema och lagring av den nya principen. Om kvarhållningen utökas markeras befintliga återställningspunkter för att behålla dem enligt den nya principen. Om kvarhållningen minskar markeras de för rensning under nästa rensningsjobb och tas sedan bort. 

### <a name="how-can-i-move-a-vm-enrolled-in-azure-backup-between-resource-groups"></a>Hur kan jag flytta en virtuell dator har registrerats i Azure-säkerhetskopiering mellan resursgrupper?
Följ de nedanstående steg för att flytta den virtuella datorn säkerhetskopierade till målresursgruppen 
1. Tillfälligt stoppa säkerhetskopiering och behåller säkerhetskopierade data
2. Flytta den virtuella datorn till målresursgruppen
3. Skydda den på nytt med samma/nytt valv

Användare kan återställa från tillgängliga återställningspunkter som skapats före flyttningen.


