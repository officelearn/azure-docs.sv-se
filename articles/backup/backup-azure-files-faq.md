---
title: Vanliga frågor och svar om säkerhetskopiering av Azure Files
description: I den här artikeln hittar du svar på vanliga frågor om hur du skyddar dina Azure-filresurser med Azure Backup-tjänsten.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: 1be509f3b82cece3afb1e728a19da4c4d9526195
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836115"
---
# <a name="questions-about-backing-up-azure-files"></a>Frågor om hur du säkerhetskopierar Azure Files

Den här artikeln innehåller vanliga frågor och svar om hur du säkerhetskopierar Azure Files. I vissa svar finns det länkar till artiklar som har omfattande information. Du kan också skicka frågor om tjänsten Azure Backup på [sidan Microsoft Q&en fråga för diskussion](https://docs.microsoft.com/answers/topics/azure-backup.html).

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

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Vad är påverkan på befintliga återställnings punkter och ögonblicks bilder när jag ändrar säkerhets kopierings principen för en Azure-filresurs för att växla från "daglig princip" till "GFS policy"?

När du ändrar en daglig säkerhets kopierings princip till GFS-principen (lägger till vecko Visa per månad/årlig kvarhållning) är beteendet följande:

- **Kvarhållning**: om du lägger till veckovis/månads Visa/årligen kvarhållning som en del av att ändra principen, kommer alla framtida återställnings punkter som skapats som en del av den schemalagda säkerhets kopian att märkas enligt den nya principen. Alla befintliga återställnings punkter betraktas fortfarande som dagliga återställnings punkter och kommer därför inte att märkas som varje vecka/månad/år.

- **Rensningar av ögonblicks bilder och återställnings punkter**:

  - Om den dagliga kvarhållning är utökad uppdateras utgångs datumet för de befintliga återställnings punkterna enligt det dagliga kvarhållningsintervallet som kon figurer ATS i den nya principen.
  - Om daglig kvarhållning minskas markeras befintliga återställnings punkter och ögonblicks bilder för borttagning i nästa rensnings körnings jobb enligt det dagliga kvarhållningsintervallet som kon figurer ATS i den nya principen och tas sedan bort.

Här är ett exempel på hur det fungerar:

#### <a name="existing-policy-p1"></a>Befintlig princip [P1]

|Bevarande typ |Schema |Kvarhållning  |
|---------|---------|---------|
|Varje dag    |    Varje dag med 8 PM    |  100 dagar       |

#### <a name="new-policy-modified-p1"></a>Ny princip [ändrad P1]

| Bevarande typ | Schema                       | Kvarhållning |
| -------------- | ------------------------------ | --------- |
| Varje dag          | Varje dag kl. 9 PM              | 50 dagar   |
| Varje vecka         | På söndag till 9 PM              | 3 veckor   |
| Månadsvis        | Den senaste måndagen vid 9 PM         | 1 månad   |
| Varje år         | I Jan den tredje söndagen med 9 PM | 4 år   |

#### <a name="impact"></a>Påverkan

1. Utgångs datumet för befintliga återställnings punkter justeras enligt det dagliga kvarhållningsintervallet för den nya principen: det vill säga 50 dagar. Därför kommer alla återställnings punkter som är äldre än 50 dagar att markeras för borttagning.

2. De befintliga återställnings punkterna märks inte som varje vecka/månad/år baserat på ny princip.

3. Alla framtida säkerhets kopieringar kommer att utlösas enligt det nya schemat: det vill säga kl. 9 PM.

4. Utgångs datumet för alla framtida återställnings punkter kommer att justeras med den nya principen.

>[!NOTE]
>Princip ändringarna påverkar bara de återställnings punkter som skapats som en del av det schemalagda säkerhets kopierings jobbet. För säkerhets kopiering på begäran avgörs kvarhållning av värdet för **kvarhållning till** som anges vid tidpunkten för säkerhets kopieringen.

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>Vad påverkar befintliga återställnings punkter när jag ändrar en befintlig GFS-princip?

När en ny princip tillämpas på fil resurser görs alla framtida schemalagda säkerhets kopieringar enligt schemat som kon figurer ATS i den ändrade principen.  Kvarhållning av alla befintliga återställnings punkter justeras enligt de nya kvarhållningsintervallet som kon figurer ATS. Så om kvarhållning har utökats markeras befintliga återställnings punkter som behålls enligt den nya principen. Om kvarhållning minskas markeras de för rensning i nästa rensnings jobb och tas sedan bort.

Här är ett exempel på hur det fungerar:

#### <a name="existing-policy-p2"></a>Befintlig princip [P2]

| Bevarande typ | Schema           | Kvarhållning |
| -------------- | ------------------ | --------- |
| Varje dag          | Varje dag med 8 PM | 50 dagar   |
| Varje vecka         | På måndag till 8 PM  | 3 veckor   |

#### <a name="new-policy-modified-p2"></a>Ny princip [ändrad P2]

| Bevarande typ | Schema               | Kvarhållning |
| -------------- | ---------------------- | --------- |
| Varje dag          | Varje dag kl. 9 PM     | 10 dagar   |
| Varje vecka         | På måndag till 9 PM      | 2 veckor   |
| Månadsvis        | Den senaste måndagen vid 9 PM | 2 månader  |

#### <a name="impact-of-change"></a>Effekt av ändring

1. Utgångs datumet för befintliga dagliga återställnings punkter justeras enligt det nya värdet för daglig kvarhållning, vilket är 10 dagar. Det innebär att alla dagliga återställnings punkter som är äldre än 10 dagar tas bort.

2. Förfallo datumet för befintliga vecko återställnings punkter justeras enligt det nya vecko kvarhållning svärdet, som är två veckor. En veckovis återställnings punkt som är äldre än två veckor kommer att tas bort.

3. De månatliga återställnings punkterna skapas bara som en del av framtida säkerhets kopieringar baserat på den nya princip konfigurationen.

4. Utgångs datumet för alla framtida återställnings punkter kommer att justeras med den nya principen.

>[!NOTE]
>Princip ändringarna påverkar bara de återställnings punkter som skapats som en del av den schemalagda säkerhets kopieringen. För säkerhets kopiering på begäran bestäms kvarhållning av värdet för **kvarhållning till** som anges vid tidpunkten för säkerhets kopieringen.

## <a name="next-steps"></a>Nästa steg

Mer information om andra områden i Azure Backup finns i några av de vanliga frågorna om säkerhets kopiering:

- [Recovery Services-valv – vanliga frågor och svar](backup-azure-backup-faq.md)
- [Säkerhetskopiering av virtuella Azure-datorer – vanliga frågor och svar ](backup-azure-vm-backup-faq.md)
- [Azure Backup-agent – vanliga frågor och svar](backup-azure-file-folder-backup-faq.md)
