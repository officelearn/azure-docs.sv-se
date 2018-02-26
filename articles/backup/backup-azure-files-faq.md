---
title: "Vanliga frågor och svar om säkerhetskopiering av Azure Files"
description: "Den här artikeln innehåller information om hur du skyddar Azure Files i Azure. Den här sammanfattningen visas i sökresultatet."
services: backup
keywords: "Lägg inte till eller redigera nyckelord utan att först kontakta den SEO-ansvarige."
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: d37e119709bc9d4643fcaa9512b5209d4139515e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="questions-about-backing-up-azure-files"></a>Frågor om hur du säkerhetskopierar Azure Files
Den här artikeln innehåller vanliga frågor och svar om hur du säkerhetskopierar Azure Files. I vissa svar finns det länkar till artiklar som har omfattande information. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Om du snabbt vill titta igenom avsnitten i denna artikel kan du använda länkarna till höger, under **Innehåll i artikeln**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurera säkerhetskopieringsjobbet för Azure Files

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-file-shares-br"></a>Varför kan jag inte se vissa av de lagringskonton som jag vill skydda (kontona innehåller giltiga filresurser)?  <br/>
Azure Files Backup är för närvarande en förhandsversion och endast lagringskonton som stöds kan konfigureras för säkerhetskopiering. Kontrollera att ditt lagringskonto stöds.

### <a name="why-cant-i-see-some-of-my-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Varför visas inte vissa av mina filresurser i lagringskontot när jag försöker konfigurera säkerhetskopiering? <br/>
Kontrollera om filresursen redan skyddas i samma Recovery Services-valv. Kontrollera att filresursen inte nyligen har tagits bort.

### <a name="why-cant-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>Varför kan jag inte skydda filresurser som är anslutna till en synkroniseringsgrupp i Azure Files Sync? <br/>
Skyddet av Azure-filresurser som är anslutna till synkroniseringsgrupper är i en begränsad förhandsversion. Skriv till [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) med ditt prenumerations-ID till begärd åtkomst. 

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>I vilka geografiska områden kan jag säkerhetskopiera Azure-filresurser? <br/>
Säkerhetskopieringen av Azure-filresurser är för närvarande en förhandsversion och är endast tillgängligt på följande platser. 
-   Kanada, centrala (CNC)
-   Kanada, östra (CE) 
-   USA, centrala (CUS)
-   Asien, östra (EA)
-   Australien, östra (AE) 
-   Indien, centrala (INC) 
-   USA, norra centrala (NCUS) 
-   Storbritannien, södra (UKS) 
-   Storbritannien, västra (UKW) 
-   USA, västra centrala (WCUS)
-   USA, västra 2 (WUS 2)

Säkerhetskopiering av Azure-filresurser kommer att finnas i följande områden från och med *23 februari*.
-   Australien, sydöstra (ASE) 
-   Brasilien, södra (BRS) 
-   USA, östra (EUS) 
-   USA, östra 2 (EUS2) 
-   Europa, norra (NE) 
-   USA, södra centrala (SCUS) 
-   Asien, sydöstra (SEA)
-   Europa, västra (WE) 
-   USA, västra (WUS)  

Skriv till [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) om du behöver använda säkerhetskopieringen i ett visst område som inte anges ovan.

### <a name="how-many-file-shares-can-i-protect-in-a-vaultbr"></a>Hur många filresurser kan jag skydda i ett valv?<br/>
I förhandsversionen kan du skydda filresurser från upp till 25 lagringskonton per valv. Du kan även skydda upp till 200 filresurser i ett enda valv. 

## <a name="backup"></a>Backup

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Hur många säkerhetskopior på begäran kan jag göra per filresurs? <br/>
Du kan ha upp till 200 ögonblicksbilder för en filresurs, inklusive de som tas av Azure Backup enligt definitionen i din princip. Om säkerhetskopieringarna börjar misslyckas på grund av att den här gränsen har nåtts tar du bort återställningspunkter för säkerhetskopiering på begäran.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>När jag aktiverade virtuella nätverk på mitt lagringskonto började säkerhetskopieringen av filresurser på kontot att misslyckas. Varför?
Azure Files Backup stöder för närvarande inte lagringskonton med aktiverade virtuella nätverk. Inaktivera de virtuella nätverken i de lagringskonton som du vill säkerhetskopiera. 

## <a name="restore"></a>Återställ

### <a name="can-i-recover-from-a-deleted-file-share-br"></a>Kan jag återställa från en borttagen filresurs? <br/>
När du försöker ta bort en filresurs visas en lista över säkerhetskopior som också tas bort om du fortsätter, och du ombeds bekräfta borttagningen. Du kan inte återställa från en borttagen filresurs.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-a-file-share-br"></a>Kan jag återställa från säkerhetskopior om jag har stoppat skyddet på en filresurs? <br/>
Ja. Om du valde **Behåll säkerhetskopieringsdata** när du stoppade skyddet kan du återställa från alla befintliga återställningspunkter.

## <a name="manage-backup"></a>Hantera säkerhetskopiering

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Kan jag få åtkomst till ögonblicksbilder som tagits av Azure Backups och montera dem? <br/>
Alla ögonblicksbilder som tas av Azure Backup kan nås via Visa ögonblicksbilder i Portal, PowerShell eller CLI. Du kan montera dem med hjälp av följande anvisningar.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Vad är maximala kvarhållningstiden jag kan konfigurera för säkerhetskopiering? <br/>
Säkerhetskopieringen för Azure-filresurser ger dig möjlighet att behålla dina dagliga säkerhetskopior i upp till 120 dagar.

### <a name="what-happens-when-i-change-the-backup-policy-for-a-file-share-br"></a>Vad händer om jag ändrar säkerhetskopieringspolicyn för en filresurs? <br/>
När en ny princip tillämpas på en eller flera filresurser följs schemat och kvarhållningstiden för den nya principen. Om kvarhållningen utökas markeras befintliga återställningspunkter för att behålla dem enligt den nya principen. Om kvarhållningen minskar markeras de för rensning under nästa rensningsjobb och tas sedan bort.


## <a name="see-also"></a>Se även
Den här informationen handlar bara om säkerhetskopiering av Azure Files. Om du vill veta mer om andra användningsområden i Azure Backup kan du läsa andra vanliga frågor och svar om säkerhetskopiering:
-  [Recovery Services-valv – vanliga frågor och svar](backup-azure-backup-faq.md)
-  [Säkerhetskopiering av virtuella Azure-datorer – vanliga frågor och svar ](backup-azure-vm-backup-faq.md)
-  [Azure Backup-agent – vanliga frågor och svar](backup-azure-file-folder-backup-faq.md)
