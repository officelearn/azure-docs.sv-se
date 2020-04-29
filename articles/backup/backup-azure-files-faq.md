---
title: Vanliga frågor och svar om säkerhetskopiering av Azure Files
description: I den här artikeln hittar du svar på vanliga frågor om hur du skyddar dina Azure-filresurser med Azure Backup-tjänsten.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: d7b19fd11e6784a188a18f6a613eef5ff4f77764
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105649"
---
# <a name="questions-about-backing-up-azure-files"></a>Frågor om hur du säkerhetskopierar Azure Files

Den här artikeln innehåller vanliga frågor och svar om hur du säkerhetskopierar Azure Files. I vissa svar finns det länkar till artiklar som har omfattande information. Du kan också ställa frågor om Azure Backup-tjänsten i [diskussionsforumet](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Om du snabbt vill titta igenom avsnitten i denna artikel kan du använda länkarna till höger, under **Innehåll i artikeln**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurera säkerhetskopieringsjobbet för Azure Files

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Varför kan jag inte se vissa av de lagrings konton som jag vill skydda, vilket innehåller giltiga Azure-filresurser?

Se [support mat ris för Azure-filresurser](azure-file-share-support-matrix.md) för att se till att lagrings kontot tillhör en av de typer av lagrings konton som stöds. Det är också möjligt att det lagrings konto som du letar efter redan är skyddat eller registrerat i ett annat valv. [Avregistrera lagrings kontot](manage-afs-backup.md#unregister-a-storage-account) från valvet för att identifiera lagrings kontot i andra valv för skydd.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Varför visas inte vissa av mina Azure-filresurser i lagringskontot när jag försöker konfigurera säkerhetskopiering?

Kontrollera om Azure-filresursen redan skyddas i samma Recovery Services-valv eller om den nyligen har tagits bort.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Kan jag skydda filresurser som är anslutna till en synkroniseringsgrupp i Azure Files Sync?

Ja. Skydd av Azure-filresurser som är anslutna till Sync-grupper har Aktiver ATS.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>När jag försökte säkerhetskopiera filresurser klickade jag på ett lagringskonto för att identifiera filresurserna i kontot, Men jag skyddar dem inte. Hur gör jag för att skyddar du dessa fil resurser med andra valv?

När du försöker säkerhetskopiera och väljer ett lagrings konto för att identifiera fil resurser i det registreras lagrings kontot med valvet som detta görs från. Om du väljer att skydda fil resurserna med ett annat valv [avregistrerar](manage-afs-backup.md#unregister-a-storage-account) du det valda lagrings kontot från det här valvet.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Kan jag ändra valvet till vilket jag säkerhetskopierar mina fil resurser?

Ja. Du måste dock [stoppa skyddet av fil resursen](manage-afs-backup.md#stop-protection-on-a-file-share) från det anslutna valvet, [avregistrera](manage-afs-backup.md#unregister-a-storage-account) lagrings kontot och sedan skydda det från ett annat valv.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Hur många Azure-filresurser kan jag skydda i ett valv?

Du kan skydda Azure-filresurser från upp till 50 lagrings konton per valv. Du kan även skydda upp till 200 Azure-filresurser i ett enda valv.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Kan jag skydda två olika filresurser från samma lagringskonto till olika valv?

Nej. Filresurserna i ett lagringskonto kan endast skyddas med ett och samma valv.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Vad gör jag om mina säkerhets kopieringar inte startar på grund av den maximala gränsen nås?

Du kan ha upp till 200 ögonblicksbilder för en filresurs vid varje tidpunkt. Gränsen gäller ögonblicksbilderna som har tagits av Azure Backup enligt definitionen i din princip. Om säkerhets kopieringen inte startar när gränsen har nåtts tar du bort ögonblicks bilder på begäran för att genomföra framtida säkerhets kopieringar.

## <a name="restore"></a>Återställ

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Kan jag återställa från en borttagen Azure-filresurs?

När en Azure-filresurs tas bort visas en lista över säkerhets kopior som tas bort och en bekräftelse begärs. För närvarande går det inte att återställa en borttagen Azure-filresurs.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Kan jag återställa från säkerhetskopior om jag har stoppat skyddet på en Azure-filresurs?

Ja. Om du valde **Behåll säkerhetskopieringsdata** när du stoppade skyddet kan du återställa från alla befintliga återställningspunkter.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Vad händer om jag avbryter ett pågående återställnings jobb?

Om ett pågående återställnings jobb avbryts stoppas återställnings processen och alla filer som återställs före annulleringen förblir i konfigurerat mål (ursprunglig eller alternativ plats) utan återställningar.

## <a name="manage-backup"></a>Hantera säkerhets kopiering

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Kan jag använda PowerShell för att konfigurera/hantera/återställa säkerhetskopior av Azure-filresurser?

Ja. Se den detaljerade dokumentationen [här](backup-azure-afs-automation.md).

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Kan jag få åtkomst till ögonblicks bilder som tas av Azure backups och montera dem?

Alla ögonblicks bilder som tas av Azure Backup kan nås genom att Visa ögonblicks bilder i portalen, PowerShell eller CLI. Mer information om hur du Azure Files resurs ögonblicks bilder finns i [Översikt över resurs ögonblicks bilder för Azure Files](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Vad är den maximala kvarhållning som jag kan konfigurera för säkerhets kopieringar?

Mer information om maximal kvarhållning finns i [support mat ris](azure-file-share-support-matrix.md) . Azure Backup utför beräkningar i real tid av antalet ögonblicks bilder när du anger Retentions värden när du konfigurerar säkerhets kopierings principen. Så snart antalet ögonblicks bilder som motsvarar dina definierade kvarhållning överskrider 200 visas en varning i portalen där du uppmanas att justera dina kvarhållning-värden. Detta är så att du inte överskrider gränsen för maximalt antal ögonblicks bilder som stöds av Azure Files för alla fil resurser vid en viss tidpunkt.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Vad händer om jag ändrar säkerhetskopieringspolicyn för en Azure-filresurs?

När en ny princip tillämpas på en eller flera filresurser följs schemat och kvarhållningstiden för den nya principen. Om kvarhållningen utökas markeras befintliga återställningspunkter för att behålla dem enligt den nya principen. Om kvarhållningen minskar markeras de för rensning vid nästa rensningsjobb och tas bort.

## <a name="next-steps"></a>Nästa steg

Mer information om andra områden i Azure Backup finns i några av de vanliga frågorna om säkerhets kopiering:

- [Recovery Services-valv – vanliga frågor och svar](backup-azure-backup-faq.md)
- [Säkerhetskopiering av virtuella Azure-datorer – vanliga frågor och svar ](backup-azure-vm-backup-faq.md)
- [Azure Backup-agent – vanliga frågor och svar](backup-azure-file-folder-backup-faq.md)
