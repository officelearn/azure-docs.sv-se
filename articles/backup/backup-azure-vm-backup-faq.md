---
title: "Vanliga frågor och svar om säkerhetskopiering av virtuella datorer i Azure | Microsoft Docs"
description: "Svar på vanliga frågor om hur virtuell Azure-säkerhetskopiering fungerar, begränsningar och vad som händer när principen ändras"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "säkerhetskopiering av virtuella datorer i azure, återställning av virtuell dator i azure, säkerhetskopieringspolicy"
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: 1372a9e05cb47f6c68240bffccd46b0fbebb5464
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Frågor om tjänsten för säkerhetskopiering av virtuella datorer i Azure
Den här artikeln innehåller svar på vanliga frågor så att du snabbt kan förstå de komponenter som används i Azure-säkerhetskopieringen av virtuella datorer. I vissa svar finns det länkar till artiklar som har omfattande information. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Stöder Recovery Services-valv klassiska virtuella datorer eller Resource Manager-baserade virtuella datorer? <br/>
Recovery Services-valv stöder båda modellerna.  Du kan säkerhetskopiera en klassisk virtuell dator (som skapats på den klassiska portalen) eller en virtuell Resource Manager-dator (som skapats på Azure Portal) till ett Recovery Services-valv.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup-"></a>Vilka konfigurationer stöds inte av vid säkerhetskopiering av virtuella Azure-datorer?
Gå igenom avsnitten [Operativsystem som stöds](backup-azure-arm-vms-prepare.md#supported-operating-system-for-backup) och [Begränsningar för säkerhetskopiering](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm)

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>Varför kan jag inte se min virtuella dator i guiden Konfigurera säkerhetskopiering?
I guiden Konfigurera säkerhetskopiering visar Azure Backup endast virtuella datorer som är:
* Inte redan skyddad – Används för att kontrollera status för säkerhetskopiering av en virtuell dator genom att gå till den virtuella datorns blad och kontrollera status för säkerhetskopiering från menyn Inställningar på bladet. Lär dig mer om att [Kontrollera status för säkerhetskopiering av en virtuell dator](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade)
* Tillhör samma region som den virtuella datorn

## <a name="backup"></a>Backup
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>Kommer säkerhetskopiering på begäran att följa samma kvarhållningsschema som schemalagda säkerhetskopieringar?
Nej. Du måste ange kvarhållningsintervallet för en säkerhetskopiering på begäran. Som standard hålls den kvar under 30 dagar när den utlöses från portalen. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Jag nyligen har aktiverat Azure Disk Encryption på vissa virtuella datorer. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Du måste ge behörigheterna för Azure Backup-tjänsten för att få åtkomst till nyckelvalvet. Du kan ange dessa behörigheter i PowerShell med hjälp av anvisningarna i avsnittet *Aktivera säkerhetskopiering* i [PowerShell](backup-azure-vms-automation.md)-dokumentationen.

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>Jag har migrerat diskar på en virtuell dator till hanterade diskar. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Ja, säkerhetskopieringen fungerar smidigt och du behöver inte konfigurera om säkerhetskopieringen. 

## <a name="restore"></a>Återställ
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>Hur gör jag för att välja mellan diskåterställning och fullständig återställning av en virtuell dator?
Tänk på fullständig återställning av en virtuell Azure-dator som ett sätt att snabbt skapa ett alternativ för den återställda virtuella datorn. Alternativet för återställning av virtuella datorer ändrar namnen på diskar, behållare som används av diskar, offentliga IP-adresser, nätverksgränssnittsnamn för unika resurser som skapas som en del av att skapa en virtuell dator. Det lägger inte heller till den virtuella datorn i tillgänglighetsuppsättningen. 

Använd återställningsdiskar för att göra följande:
* Anpassa den virtuella datorn som skapas från tidpunktskonfigurationen, exempelvis genom att ändra storlek på konfigurationen för säkerhetskopiering
* Lägg till konfigurationer som inte är tillgängliga vid tidpunkten för säkerhetskopieringen 
* Kontrollen namngivningskonventionen för resurser som skapas
* Lägg till virtuell dator i tillgänglighetsuppsättningen
* Du har en konfiguration som kan uppnås endast med hjälp av PowerShell/en deklarativ malldefinition

## <a name="manage-vm-backups"></a>Hantera säkerhetskopior av virtuella datorer
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>Vad händer om jag ändrar en säkerhetskopieringspolicy på virtuella datorer?
När en ny princip tillämpas på virtuella datorer följs schemat för och kvarhållningen av den nya principen. Om kvarhållningen utökas markeras befintliga återställningspunkter för att behålla dem enligt den nya principen. Om kvarhållningen minskar markeras de för rensning under nästa rensningsjobb och tas bort. 
