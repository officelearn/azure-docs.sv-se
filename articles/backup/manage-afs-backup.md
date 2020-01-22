---
title: Hantera säkerhetskopior av Azure-filresurser
description: I den här artikeln beskrivs vanliga uppgifter för att hantera och övervaka Azure-filresurser som säkerhets kopie ras av Azure Backups tjänsten.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294557"
---
# <a name="manage-azure-file-share-backups"></a>Hantera säkerhetskopior av Azure-filresurser

I den här artikeln beskrivs vanliga uppgifter för att hantera och övervaka Azure-filresurser som säkerhets kopie ras av [Azure backups](https://docs.microsoft.com/azure/backup/backup-overview) tjänsten. Du lär dig hur du kör följande hanterings aktiviteter i Recovery Services valvet:

* [Övervaka jobb](#monitor-jobs)
* [Skapa en ny policy](#create-a-new-policy)
* [Ändra princip](#modify-policy)
* [Avbryta skyddet av en filresurs](#stop-protection-on-a-file-share)
* [Återuppta skyddet av en filresurs](#resume-protection-on-a-file-share)
* [Ta bort säkerhetskopierade data](#delete-backup-data)
* [Avregistrera lagrings konto](#unregister-storage-account)

## <a name="monitor-jobs"></a>Övervaka jobb

När du utlöser en säkerhets kopierings-eller återställnings åtgärd skapar säkerhets kopierings tjänsten ett jobb för spårning. Du kan övervaka förloppet för alla jobb på sidan **Säkerhetskopieringsjobb**.

Öppna sidan **Säkerhetskopieringsjobb**:

1. Öppna det Recovery Services-valv som du använde för att konfigurera säkerhets kopiering för dina fil resurser. I **översikts** bladet klickar du på **säkerhets kopierings jobb** under avsnittet **övervakning** .

   ![Säkerhets kopierings jobb i avsnittet övervakning](./media/manage-afs-backup/backup-jobs.png)

2. När du klickar på OK visas bladet **säkerhets kopierings jobb** som visar status för alla jobb. Du kan klicka på det arbets belastnings namn som motsvarar den fil resurs som du vill övervaka.

   ![Arbets belastnings namn](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Skapa en ny policy

Du kan skapa en ny princip för att säkerhetskopiera Azure-filresurser från avsnittet **säkerhets kopierings principer** i Recovery Services valvet. Alla principer som skapas när du konfigurerade säkerhets kopiering för fil resurser visas med princip typen Azure-filresurs.

Visa befintliga principer för säkerhetskopiering:

1. Öppna det Recovery Services valv som du använde för att konfigurera säkerhets kopieringen för fil resursen och klicka på **säkerhets kopierings principer** under avsnittet hantera på menyn Recovery Services valv. Alla säkerhets kopierings principer som kon figurer ATS i valvet visas.

   ![Alla säkerhets kopierings principer](./media/manage-afs-backup/all-backup-policies.png)

2. Om du vill visa principer som är speciella för Azure-filresursen väljer du **Azure-filresurs** i list rutan längst upp till höger.

   ![Välj Azure-filresurs](./media/manage-afs-backup/azure-file-share.png)

Skapa en ny princip för säkerhetskopiering:

1. Klicka på **+ Lägg till** på bladet säkerhets kopierings principer.

   ![Ny säkerhets kopierings princip](./media/manage-afs-backup/new-backup-policy.png)

2. Välj **Azure-filresurs** som **princip typ** i bladet **Lägg till** . Bladet säkerhets kopierings policy för Azure-filresursen öppnas. Ange princip namn, säkerhets kopierings frekvens och kvarhållningsintervall för återställnings punkterna. Klicka på **OK** när du har definierat principen.

   ![Definiera säkerhets kopierings policyn](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Ändra princip

Du kan ändra en säkerhets kopierings princip om du vill ändra säkerhets kopierings frekvens eller kvarhållningsintervall.

Så här ändrar du en princip:

1. Öppna det Recovery Services valv som du använde för att konfigurera säkerhets kopieringen för fil resursen och klicka på **säkerhets kopierings principer** under avsnittet hantera på Recovery Services valv-menyn. Alla säkerhets kopierings principer som kon figurer ATS i valvet visas.

   ![Alla säkerhets kopierings principer i valvet](./media/manage-afs-backup/all-backup-policies-modify.png)

2. Om du vill visa principer som är speciella för en Azure-filresurs väljer du **Azure-filresurs** i list rutan längst upp till höger. Klicka på den säkerhets kopierings princip som du vill ändra.

   ![Azure-filresurs att ändra](./media/manage-afs-backup/azure-file-share-modify.png)

3. Bladet **schema** öppnas. Redigera säkerhets kopierings schema/kvarhållningsintervall efter behov och klicka på **Spara**. Du ser meddelandet "uppdatering pågår" på bladet och när princip ändringarna har uppdaterats visas meddelandet "uppdateringen av säkerhets kopierings principen har uppdaterats".

   ![Spara den ändrade principen](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Avbryta skyddet av en filresurs

Det finns två sätt att sluta skydda Azure-filresurser:

* Stoppa alla framtida säkerhets kopierings jobb och *ta bort alla återställnings punkter*
* Stoppa alla framtida säkerhets kopierings jobb men *lämna återställnings punkterna*

Det kan finnas en kostnad för att lämna återställnings punkterna i lagret, eftersom de underliggande ögonblicks bilderna som skapats av Azure Backup bevaras. Fördelen med att lämna kvar återställningspunkterna är dock att du kan återställa filresursen senare, om så önskas. Information om kostnaden för att lämna återställnings punkterna finns i [pris informationen](https://azure.microsoft.com/pricing/details/backup/). Om du väljer att ta bort alla återställningspunkter kan du inte återställa filresursen.

Sluta skydda en Azure-filresurs:

1. Öppna Recovery Services-valvet som innehåller fil resurs återställnings punkterna och klicka på **säkerhetskopiera objekt** under skyddade objekt. En lista med typer av säkerhetskopieringsobjekt visas.

   ![Säkerhetskopieringsobjekt](./media/manage-afs-backup/backup-items.png)

2. I listan **Typ av säkerhetskopieringshantering** väljer du **Azure Storage (Azure Files)** . Listan över **säkerhets kopierings objekt för (Azure Storage (Azure Files))** visas.

   ![Välj Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. I listan över **säkerhets kopierings objekt (Azure Storage (Azure Files))** väljer du det säkerhets kopierings objekt som du vill stoppa skyddet för.

4. Välj alternativet **stoppa säkerhets kopiering** på bladet **säkerhets kopierings objekt** .

   ![Välja Avbryt säkerhetskopiering](./media/manage-afs-backup/stop-backup.png)

5. På bladet **stoppa säkerhets kopiering** väljer du att **behålla säkerhets kopierings data** eller **ta bort säkerhetskopierade data** och klickar på **stoppa säkerhets kopiering**.

    ![Välj Behåll eller ta bort säkerhetskopierade data](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Återuppta skyddet av en filresurs

Om alternativet **Behåll säkerhets kopierings data** valdes när skyddet för fil resursen stoppades, är det möjligt att återuppta skyddet. Om alternativet **Ta bort säkerhetskopieringsdata** valdes går det inte att återuppta skyddet av filresursen.

Återuppta skyddet för Azure-fil resursen:

1. Öppna Recovery Services-valvet som innehåller fil resurs återställnings punkterna och klicka på **säkerhetskopiera objekt** under skyddade objekt. En lista med typer av säkerhetskopieringsobjekt visas.

   ![Säkerhetskopiera objekt för återuppta](./media/manage-afs-backup/backup-items-resume.png)

2. I listan **Typ av säkerhetskopieringshantering** väljer du **Azure Storage (Azure Files)** . Listan över **säkerhets kopierings objekt för (Azure Storage (Azure Files))** visas.

   ![Lista över Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. I listan över **säkerhets kopierings objekt (Azure Storage (Azure Files))** väljer du det säkerhets kopierings objekt som du vill återuppta skyddet för.

4. Välj alternativet **återuppta säkerhets kopiering** på blad menyn för **säkerhets kopierings objekt** .

   ![Välj återuppta säkerhets kopiering](./media/manage-afs-backup/resume-backup.png)

5. Bladet **säkerhets kopierings policy** öppnas och du kan välja en princip som du väljer för att återuppta säkerhets kopieringen.

6. Klicka på **Spara** när du har valt önskad **säkerhets kopierings princip**. Du ser meddelandet "uppdatering pågår" på portalen och när säkerhets kopieringen har återupptagits visas meddelandet "en uppdaterad säkerhets kopierings princip för skyddad Azure-filresurs".

   ![Säkerhets kopierings principen har uppdaterats](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata

Du kan ta bort säkerhets kopian av en fil resurs under aktiviteten **Avbryt säkerhets kopiering** eller när som helst efter att du har stoppat skyddet. Det kan vara en fördel att vänta dagar eller veckor innan du tar bort återställningspunkterna. När du tar bort säkerhetskopierade data kan du inte välja vissa återställnings punkter att ta bort. Om du väljer att ta bort dina säkerhetskopierade data tar du bort alla återställnings punkter som är associerade med fil resursen.

Följande procedur förutsätter att skyddet har stoppats för fil resursen.

Ta bort säkerhetskopierade data för Azure-fil resurs:

1. När säkerhets kopierings jobbet har stoppats är alternativen **Fortsätt säkerhetskopiera** och **ta bort säkerhets kopierings data** tillgängliga på instrument panelen för säkerhets kopierings **objekt** . Klicka på alternativet för att **ta bort säkerhets kopierings data** på blad menyn **säkerhets kopierings objekt** .

   ![Ta bort säkerhetskopieringsdata](./media/manage-afs-backup/delete-backup-data.png)

2. Bladet **ta bort säkerhets kopierings data** öppnas. Skriv namnet på fil resursen för att bekräfta borttagningen. Du kan också ange en **orsak** till att ta bort eller **kommentera**. Klicka på **ta bort** när du är säker på att du vill ta bort säkerhetskopierade data.

   ![Bekräfta borttagning av data](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>Avregistrera lagrings konto

Om du vill skydda dina fil resurser i ett visst lagrings konto med ett annat Recovery Services-valv måste du först [stoppa skyddet för alla fil resurser](#stop-protection-on-a-file-share) i det lagrings kontot. Avregistrera sedan kontot från det aktuella Recovery Services-valvet som används för skydd.

Följande procedur förutsätter att skyddet har stoppats för alla fil resurser i lagrings kontot som du vill avregistrera.

Så här avregistrerar du lagrings kontot:

1. Öppna Recovery Services-valvet där ditt lagrings konto är registrerat.
2. Klicka på alternativet **infrastruktur för säkerhets kopiering** under avsnittet **Hantera** på **översikts** bladet.

   ![Klicka på infrastruktur för säkerhets kopiering](./media/manage-afs-backup/backup-infrastructure.png)

3. Bladet **infrastruktur för säkerhets kopiering** öppnas. Klicka på **lagrings konton** under avsnittet **Azure Storage-konton** på det här bladet.

   ![Klicka på lagrings konton](./media/manage-afs-backup/storage-accounts.png)

4. När du klickar på **lagrings konton**visas en lista över lagrings konton som registrerats med valvet.
5. Högerklicka på det lagrings konto som du vill avregistrera och välj **avregistrera**.

   ![Välj avregistrera](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Felsöka säkerhets kopierings-/återställnings fel för Azure-filresurser](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)
