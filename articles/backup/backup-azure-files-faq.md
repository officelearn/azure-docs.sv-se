---
title: Vanliga frågor och svar om säkerhetskopiering av Azure Files
description: I den här artikeln hittar du svar på vanliga frågor om hur du skyddar dina Azure-filresurser med Azure Backup-tjänsten.
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: bc4d7f9ad6512831687b974ae68870314775457d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892584"
---
# <a name="questions-about-backing-up-azure-files"></a>Frågor om hur du säkerhetskopierar Azure Files

Den här artikeln innehåller vanliga frågor och svar om hur du säkerhetskopierar Azure Files. I vissa svar finns det länkar till artiklar som har omfattande information. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Om du snabbt vill titta igenom avsnitten i denna artikel kan du använda länkarna till höger, under **Innehåll i artikeln**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurera säkerhetskopieringsjobbet för Azure Files

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares"></a>Varför kan jag inte se vissa av de lagringskonton som jag vill skydda, och som innehåller giltiga Azure-filresurser?

Under förhandsversionen stöder inte säkerhetskopieringen för Azure-filresurser alla typer av lagringskonton. Se listan [här](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview) om du vill se en lista över Storage-konton som stöds. Det är också möjligt att det Storage Account du letar efter redan är skyddat eller registrerat med ett annat valv. [Avregistrera](troubleshoot-azure-files.md#configuring-backup) dig från valvet för att upptäcka Storage-kontot i andra valv i skyddssyfte.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Varför visas inte vissa av mina Azure-filresurser i lagringskontot när jag försöker konfigurera säkerhetskopiering?

Kontrollera om Azure-filresursen redan skyddas i samma Recovery Services-valv eller om den nyligen har tagits bort.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Kan jag skydda filresurser som är anslutna till en synkroniseringsgrupp i Azure Files Sync?

Ja. Skydd av Azure-filresurser som är anslutna till synkroniseringsgrupper kan användas och ingår i förhandsversionen.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>När jag försökte säkerhetskopiera filresurser klickade jag på ett lagringskonto för att identifiera filresurserna i kontot, men jag skyddade dem inte. Hur gör jag för att skydda de här filresurserna med ett annat valv?

När du försöker säkerhetskopiera och väljer ett lagringskonto för att identifiera filresurser i kontot, registreras lagringskontot i det valv där detta görs från. Om du väljer att skydda filresurser med ett annat valv, måste du [avregistrera](troubleshoot-azure-files.md#configuring-backup) det valda lagringskontot från det här valvet.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Kan jag ändra valvet till vilket jag säkerhetskopierar mina fil resurser?

Ja. Men du måste [stoppa skyddet](backup-azure-files.md#stop-protecting-an-azure-file-share) från det anslutna valvet, [avregistrera](troubleshoot-azure-files.md#configuring-backup) det här lagringskontot och sedan skydda det från ett annat valv.

### <a name="in-which-geos-can-i-back-up-azure-file-shares"></a>I vilken geografiska områden kan jag säkerhetskopiera Azure-filresurser?

Säkerhetskopieringen av Azure-filresurser är för närvarande en förhandsversion och är endast tillgängligt på följande platser:

- Östra Australien (AE)
- Australien, sydöstra (ASE)
- Brasilien, södra (BRS)
- Kanada, centrala (CNC)
- Kanada, östra (CE)
- USA, centrala (CUS)
- Asien, östra (EA)
- USA, östra (EUS)
- USA, östra 2 (EUS2)
- Japan, östra (JPE)
- Japan, västra (JPW)
- Indien, centrala (INC)
- Indien, södra (INS)
- Sydkorea, centrala (KRC)
- Sydkorea, södra (KRS)
- USA, norra centrala (NCUS)
- Europa, norra (NE)
- USA, södra centrala (SCUS)
- Asien, sydöstra (SEA)
- Storbritannien, södra (UKS)
- Storbritannien, västra (UKW)
- Europa, västra (WE)
- USA, västra (WUS)
- USA, västra centrala (WCUS)
- USA, västra 2 (WUS 2)
- US Gov, Arizona (UGA)
- US Gov, Texas (UGT)
- US Gov, Virginia (UGV)
- Australien, centrala (ACL)
- Indien, väst (INW)
- Södra Afrika, norra (SAN)
- Förenade Arabemiraten Nord (UAN)
- Frankrike, centrala (FRC)
- Tyskland, norra (GN)                       
- Tyskland, västra centrala (GWC)
- Södra Afrika (såg)
- Förenade Arabemiraten Central (UAC)
- NVI (Norge, öst)     
- NWW (Norge, västra)
- SZN (Schweiz, norra)

Skriv till [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) om du behöver använda den i ett visst område som inte anges ovan.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Hur många Azure-filresurser kan jag skydda i ett valv?

I förhandsversionen kan du skydda Azure-filresurser från upp till 50 lagringskonton per valv. Du kan även skydda upp till 200 Azure-filresurser i ett enda valv.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Kan jag skydda två olika filresurser från samma lagringskonto till olika valv?

Nej. Filresurserna i ett lagringskonto kan endast skyddas med ett och samma valv.

## <a name="backup"></a>Backup

### <a name="how-many-scheduled-backups-can-i-configure-per-file-share"></a>Hur många schemalagda säkerhets kopieringar kan jag konfigurera per fil resurs?

Azure Backup stöder för närvarande att konfigurera schemalagda en gång – daglig säkerhets kopiering av Azure-filresurser.

### <a name="how-many-on-demand-backups-can-i-take-per-file-share"></a>Hur många säkerhetskopior på begäran kan jag göra per filresurs?

Du kan ha upp till 200 ögonblicksbilder för en filresurs vid varje tidpunkt. Gränsen gäller ögonblicksbilderna som har tagits av Azure Backup enligt definitionen i din princip. Om dina säkerhetskopior börjar misslyckas när gränsen är nådd tar du bort återställningspunkter på begäran för lyckade säkerhetskopior i framtiden.

## <a name="restore"></a>Återställ

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Kan jag återställa från en borttagen Azure-filresurs?

När du tar bort en Azure-filresurs visas listan över säkerhetskopieringar som också tas bort, och en bekräftelse begärs. Borttagna Azure-filresurs går inte att återställa.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Kan jag återställa från säkerhetskopior om jag har stoppat skyddet på en Azure-filresurs?

Ja. Om du valde **Behåll säkerhetskopieringsdata** när du stoppade skyddet kan du återställa från alla befintliga återställningspunkter.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Vad händer om jag avbryter ett pågående återställnings jobb?

Om ett pågående återställnings jobb avbryts stoppas återställnings processen och alla filer som återställs före annulleringen förblir i konfigurerat mål (ursprunglig eller alternativ plats) utan återställningar.

## <a name="manage-backup"></a>Hantera säkerhets kopiering

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Kan jag använda PowerShell för att konfigurera/hantera/återställa säkerhetskopior av Azure-filresurser?

Ja. Läs den detaljerade dokumentationen [här](backup-azure-afs-automation.md)

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it"></a>Kan jag få åtkomst till ögonblicksbilder som tagits av Azure Backups och montera dem?

Alla ögonblicksbilder som tas av Azure Backup kan nås via Visa ögonblicksbilder i Portal, PowerShell eller CLI. Läs mer om ögonblicksbilder av Azure-filresurser i [Översikt över resursögonblicksbilder för Azure Files (förhandsversion)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Vad är den maximala kvarhållning som jag kan konfigurera för säkerhets kopieringar?

Säkerhetskopiering för Azure-filresurser ger dig möjlighet att konfigurera principer med kvarhållning i upp till 180 dagar. Men med [alternativet för ”säkerhetskopiering på begäran” i PowerShell](backup-azure-afs-automation.md#trigger-an-on-demand-backup) kan du till och med behålla en återställningspunkt i tio år.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Vad händer om jag ändrar säkerhetskopieringspolicyn för en Azure-filresurs?

När en ny princip tillämpas på en eller flera filresurser följs schemat och kvarhållningstiden för den nya principen. Om kvarhållningen utökas markeras befintliga återställningspunkter för att behålla dem enligt den nya principen. Om kvarhållningen minskar markeras de för rensning vid nästa rensningsjobb och tas bort.

## <a name="next-steps"></a>Nästa steg

Mer information om andra områden i Azure Backup finns i några av de vanliga frågorna om säkerhets kopiering:

- [Recovery Services-valv – vanliga frågor och svar](backup-azure-backup-faq.md)
- [Säkerhetskopiering av virtuella Azure-datorer – vanliga frågor och svar ](backup-azure-vm-backup-faq.md)
- [Azure Backup-agent – vanliga frågor och svar](backup-azure-file-folder-backup-faq.md)
