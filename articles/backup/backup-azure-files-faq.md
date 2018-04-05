---
title: Vanliga frågor och svar om säkerhetskopiering av Azure Files
description: Den här artikeln innehåller information om hur du skyddar Azure-filresurser.
services: backup
keywords: Lägg inte till eller redigera nyckelord utan att först kontakta den SEO-ansvarige.
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: 850d4d1e2ef6a13fcd8a072e6da210d558c7769b
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="questions-about-backing-up-azure-files"></a>Frågor om hur du säkerhetskopierar Azure Files
Den här artikeln innehåller vanliga frågor och svar om hur du säkerhetskopierar Azure Files. I vissa svar finns det länkar till artiklar som har omfattande information. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Om du snabbt vill titta igenom avsnitten i denna artikel kan du använda länkarna till höger, under **Innehåll i artikeln**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurera säkerhetskopieringsjobbet för Azure Files

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>Varför kan jag inte se vissa av de lagringskonton som jag vill skydda, och som innehåller giltiga Azure-filresurser? <br/>
Under förhandsversionen stöder inte säkerhetskopieringen för Azure-filresurser alla typer av lagringskonton. Se listan [här](troubleshoot-azure-files.md#preview-boundaries) om du vill se en lista över Storage-konton som stöds.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Varför visas inte vissa av mina Azure-filresurser i lagringskontot när jag försöker konfigurera säkerhetskopiering? <br/>
Kontrollera om Azure-filresursen redan skyddas i samma Recovery Services-valv eller nyligen har tagits bort.

### <a name="why-cant-i-protect-file-shares-connected-to-a-sync-group-in-azure-file-sync-br"></a>Varför kan jag inte skydda filresurser som är anslutna till en synkroniseringsgrupp i Azure File Sync? <br/>
Skyddet av Azure-filresurser som är anslutna till synkroniseringsgrupper är i en begränsad förhandsversion. Skriv till [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) med ditt prenumerations-ID till begärd åtkomst. 

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>I vilka geografiska områden kan jag säkerhetskopiera Azure-filresurser? <br/>
Säkerhetskopieringen av Azure-filresurser är för närvarande en förhandsversion och är endast tillgängligt på följande platser: 
-   Australien, sydöstra (ASE) 
- Brasilien, södra (BRS)
- Kanada, centrala (CNC)
-   Kanada, östra (CE)
-   USA, centrala (CUS)
-   Asien, östra (EA)
-   Australien, östra (AE) 
-   USA, östra (EUS)
-   USA, östra 2 (EUS2)
-   Indien, centrala (INC) 
-   USA, norra centrala (NCUS) 
-   Europa, norra (NE) 
-   USA, södra centrala (SCUS) 
-   Asien, sydöstra (SEA)
-   Storbritannien, södra (UKS) 
-   Storbritannien, västra (UKW) 
-   Europa, västra (WE) 
-   USA, västra (WUS)
-   USA, västra centrala (WCUS)
-   USA, västra 2 (WUS 2)

Skriv till [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) om du behöver använda säkerhetskopieringen i ett visst område som inte anges ovan.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>Hur många Azure-filresurser kan jag skydda i ett valv?<br/>
I förhandsversionen kan du skydda Azure-filresurser från upp till 25 lagringskonton per valv. Du kan även skydda upp till 200 Azure-filresurser i ett enda valv. 

## <a name="backup"></a>Backup

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Hur många säkerhetskopior på begäran kan jag göra per filresurs? <br/>
Du kan ha upp till 200 ögonblicksbilder för en filresurs när som helst. Gränsen gäller ögonblicksbilderna som har tagits av Azure Backup enligt definitionen i din princip. Om dina säkerhetskopior börjar misslyckas när gränsen är nådd tar du bort återställningspunkter på begäran för lyckade säkerhetskopior i framtiden.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>När jag aktiverade virtuella nätverk på mitt lagringskonto började säkerhetskopieringen av filresurser på kontot att misslyckas. Varför?
Säkerhetskopiering för Azure-filresurser stöder för närvarande inte lagringskonton med aktiverade virtuella nätverk. Inaktivera virtuella nätverk i lagringskonton för att få en fungerande säkerhetskopiering. 

## <a name="restore"></a>Återställ

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>Kan jag återställa från en borttagen Azure-filresurs? <br/>
När du tar bort en Azure-filresurs visas listan över säkerhetskopieringar som också tas bort och en bekräftelse begärs. En borttagen Azure-filresurs går inte att återställa.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>Kan jag återställa från säkerhetskopior om jag har stoppat skyddet på en Azure-filresurs? <br/>
Ja. Om du valde **Behåll säkerhetskopieringsdata** när du stoppade skyddet kan du återställa från alla befintliga återställningspunkter.

## <a name="manage-backup"></a>Hantera säkerhetskopiering

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Kan jag få åtkomst till ögonblicksbilder som tagits av Azure Backups och montera dem? <br/>
Alla ögonblicksbilder som tas av Azure Backup kan nås via Visa ögonblicksbilder i Portal, PowerShell eller CLI. Läs mer om ögonblicksbilder av Azure-filresurser i [Översikt över resursögonblicksbilder för Azure Files (förhandsversion)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Vad är maximala kvarhållningstiden jag kan konfigurera för säkerhetskopiering? <br/>
Säkerhetskopieringen för Azure-filresurser ger dig möjlighet att behålla dina dagliga säkerhetskopior i upp till 120 dagar.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>Vad händer om jag ändrar säkerhetskopieringspolicyn för en Azure-filresurs? <br/>
När en ny princip tillämpas på en eller flera filresurser följs schemat och kvarhållningstiden för den nya principen. Om kvarhållningen utökas markeras befintliga återställningspunkter för att behålla dem enligt den nya principen. Om kvarhållningen minskar markeras de för rensning under nästa rensningsjobb och tas sedan bort.

## <a name="see-also"></a>Se även
Den här informationen handlar bara om säkerhetskopiering av Azure Files. Om du vill veta mer om andra användningsområden i Azure Backup kan du läsa andra vanliga frågor och svar om säkerhetskopiering:
-  [Recovery Services-valv – vanliga frågor och svar](backup-azure-backup-faq.md)
-  [Säkerhetskopiering av virtuella Azure-datorer – vanliga frågor och svar ](backup-azure-vm-backup-faq.md)
-  [Azure Backup-agent – vanliga frågor och svar](backup-azure-file-folder-backup-faq.md)