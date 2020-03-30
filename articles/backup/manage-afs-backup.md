---
title: Hantera säkerhetskopior av Azure-filresurser
description: I den här artikeln beskrivs vanliga uppgifter för att hantera och övervaka Azure-filresurser som backas upp av Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247662"
---
# <a name="manage-azure-file-share-backups"></a>Hantera säkerhetskopior av Azure-filresurser

I den här artikeln beskrivs vanliga uppgifter för att hantera och övervaka Azure-filresurser som backas upp av [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Du får lära dig hur du utför hanteringsuppgifter i valvet för Återställningstjänster.

## <a name="monitor-jobs"></a>Övervaka jobb

När du utlöser en säkerhetskopiering eller återställning skapar säkerhetskopieringstjänsten ett jobb för spårning. Du kan övervaka förloppet för alla jobb på sidan **Säkerhetskopieringsjobb**.

Öppna sidan **Säkerhetskopieringsjobb**:

1. Öppna valvet för Återställningstjänster som du använde för att konfigurera säkerhetskopiering för filresurserna. Välj **Säkerhetskopierade jobb** under avsnittet **Övervakning** i fönstret **Översikt.**

   ![Avsnittet Säkerhetskopiering av jobb i övervakningsavsnittet](./media/manage-afs-backup/backup-jobs.png)

1. När du har valt **OK**visas status för alla jobb i fönstret **Säkerhetskopieringsjobb.** Välj det arbetsbelastningsnamn som motsvarar den filresurs som du vill övervaka.

   ![Namn på arbetsbelastning](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Skapa en ny policy

Du kan skapa en ny princip för att säkerhetskopiera Azure-filresurser från avsnittet **Principer för säkerhetskopiering** i valvet för Återställningstjänster. Alla principer som skapas när du konfigurerade säkerhetskopiering för filresurser visas med **principtypen** som **Azure File Share**.

Så här visar du befintliga principer för säkerhetskopiering:

1. Öppna valvet för Återställningstjänster som du använde för att konfigurera säkerhetskopian för filresursen. På Arkivmenyn Återställningstjänster väljer du **Principer för säkerhetskopiering** under avsnittet **Hantera.** Alla principer för säkerhetskopiering som konfigurerats i valvet visas.

   ![Alla principer för säkerhetskopiering](./media/manage-afs-backup/all-backup-policies.png)

1. Om du vill visa principer som är specifika för **Azure File Share**väljer du Azure File **Share** i listrutan längst upp till höger.

   ![Välj Azure-filresurs](./media/manage-afs-backup/azure-file-share.png)

Så här skapar du en ny princip för säkerhetskopiering:

1. Välj **+ Lägg till**i fönstret Principer för **säkerhetskopiering.**

   ![Ny princip för säkerhetskopiering](./media/manage-afs-backup/new-backup-policy.png)

1. Välj Azure File **Share** som **principtyp**i fönstret **Lägg** till . Principfönstret **Säkerhetskopiering** för **Azure File Share** öppnas. Ange principnamn, säkerhetskopieringsfrekvens och kvarhållningsintervall för återställningspunkterna. När du har definierat principen väljer du **OK**.

   ![Definiera principen för säkerhetskopiering](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Ändra princip

Du kan ändra en princip för säkerhetskopiering för att ändra säkerhetskopieringsfrekvensen eller kvarhållningsområdet.

Så här ändrar du en princip:

1. Öppna valvet för Återställningstjänster som du använde för att konfigurera säkerhetskopian för filresursen. På Arkivmenyn Återställningstjänster väljer du **Principer för säkerhetskopiering** under avsnittet **Hantera.** Alla principer för säkerhetskopiering som konfigurerats i valvet visas.

   ![Alla principer för säkerhetskopiering i valvet](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Om du vill visa principer som är specifika för en Azure-filresurs väljer du **Azure File Share** i listrutan längst upp till höger. Välj den principer för säkerhetskopiering som du vill ändra.

   ![Azure-filresurs att ändra](./media/manage-afs-backup/azure-file-share-modify.png)

1. **Fönstret Schema** öppnas. Redigera **intervallet Säkerhetskopiering** och **kvarhållning** efter behov och välj **Spara**. Meddelandet "Uppdatera pågår" visas i fönstret. När uppdateringen av principen har uppdaterats visas meddelandet "Framgångsrikt uppdaterat säkerhetskopieringsprincipen".

   ![Spara den ändrade principen](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Avbryta skyddet av en filresurs

Det finns två sätt att sluta skydda Azure-filresurser:

* Stoppa alla framtida säkerhetskopieringsjobb och *ta bort alla återställningspunkter*.
* Stoppa alla framtida säkerhetskopieringsjobb, men *lämna återställningspunkterna*.

Det kan finnas en kostnad som är associerad med att lämna återställningspunkterna i lagring, eftersom de underliggande ögonblicksbilderna som skapats av Azure Backup behålls. Fördelen med att lämna återställningspunkterna är att du kan återställa filresursen senare. Information om kostnaden för att lämna återställningspunkterna finns i [prisinformationen](https://azure.microsoft.com/pricing/details/backup/). Om du bestämmer dig för att ta bort alla återställningspunkter kan du inte återställa filresursen.

Sluta skydda en Azure-filresurs:

1. Öppna valvet för återställningstjänster som innehåller återställningspunkterna för filresurs. Välj **Säkerhetskopierade objekt** under avsnittet **Skyddade objekt.** Listan över objekttyper för säkerhetskopiering visas.

   ![Säkerhetskopior](./media/manage-afs-backup/backup-items.png)

1. I listan **Typ av säkerhetskopieringshantering** väljer du **Azure Storage (Azure Files)**. Listan **Säkerhetskopieringsobjekt (Azure Storage (Azure Files))** visas.

   ![Välj Azure Storage (Azure-filer)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. I listan **Säkerhetskopior (Azure Storage (Azure Files))** väljer du det säkerhetskopieringsobjekt som du vill stoppa skyddet för.

1. Välj alternativet **Stoppa säkerhetskopiering.**

   ![Välja Avbryt säkerhetskopiering](./media/manage-afs-backup/stop-backup.png)

1. I fönstret **Stoppa säkerhetskopiering** väljer du **Behåll säkerhetskopierade data** eller **Ta bort säkerhetskopierade data**. Välj sedan **Stoppa säkerhetskopiering**.

    ![Välj Behåll säkerhetskopierade data eller Ta bort säkerhetskopierade data](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Återuppta skyddet av en filresurs

Om alternativet **Behåll säkerhetskopieringsdata** valdes när skyddet för filresursen stoppades är det möjligt att återuppta skyddet. Om alternativet **Ta bort säkerhetskopierade data** har valts kan skyddet för filresursen inte återupptas.

Så här återupptar du skyddet för Azure-filresursen:

1. Öppna valvet för återställningstjänster som innehåller återställningspunkterna för filresurs. Välj **Säkerhetskopierade objekt** under avsnittet **Skyddade objekt.** Listan över objekttyper för säkerhetskopiering visas.

   ![Säkerhetskopior för återuppta](./media/manage-afs-backup/backup-items-resume.png)

1. I listan **Typ av säkerhetskopieringshantering** väljer du **Azure Storage (Azure Files)**. Listan **Säkerhetskopieringsobjekt (Azure Storage (Azure Files))** visas.

   ![Lista över Azure Storage (Azure-filer)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. I listan **Säkerhetskopior (Azure Storage (Azure Files))** väljer du det säkerhetskopieringsobjekt som du vill återuppta skyddet för.

1. Välj alternativet **Återuppta säkerhetskopiering.**

   ![Välj återuppta säkerhetskopiering](./media/manage-afs-backup/resume-backup.png)

1. Fönstret **Säkerhetskopieringsprincip** öppnas. Välj en princip som du väljer för att återuppta säkerhetskopieringen.

1. När du har valt en princip för säkerhetskopiering väljer du **Spara**. Meddelandet "Uppdatera pågår" visas i portalen. När säkerhetskopieringen har återupptagits visas meddelandet "Framgångsrikt uppdaterad säkerhetskopieringsprincip för den skyddade Azure-filresursen".

   ![Säkerhetskopieringsprincipen har uppdaterats](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata

Du kan ta bort säkerhetskopian av en filresurs under **jobbet Stoppa säkerhetskopiering,** eller när som helst efter att du har slutat skydda. Det kan vara fördelaktigt att vänta dagar eller veckor innan du tar bort återställningspunkterna. När du tar bort säkerhetskopierade data kan du inte välja specifika återställningspunkter att ta bort. Om du bestämmer dig för att ta bort dina säkerhetskopierade data tar du bort alla återställningspunkter som är associerade med filresursen.

Följande procedur förutsätter att skyddet stoppades för filresursen.

Så här tar du bort säkerhetskopierade data för Azure-filresursen:

1. När säkerhetskopieringsjobbet har stoppats är alternativen **för säkerhetskopiering och** **ta bort säkerhetskopierade data** tillgängliga på instrumentpanelen för **säkerhetskopieringsobjekt.** Välj alternativet **Ta bort säkerhetskopierade data.**

   ![Ta bort säkerhetskopieringsdata](./media/manage-afs-backup/delete-backup-data.png)

1. Fönstret **Ta bort säkerhetskopierade data** öppnas. Ange namnet på filresursen för att bekräfta borttagningen. Du kan också ange mer information i rutorna **Orsak** eller **Kommentarer.** När du är säker på att ta bort säkerhetskopierade data väljer du **Ta bort**.

   ![Bekräfta borttagningsdata](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Avregistrera ett lagringskonto

Om du vill skydda filresurserna i ett visst lagringskonto med hjälp av ett annat valv för återställningstjänster stoppar du först [skyddet för alla filresurser](#stop-protection-on-a-file-share) i det lagringskontot. Avregistrera sedan kontot från det aktuella återställningstjänstvalvet som används för skydd.

Följande procedur förutsätter att skyddet stoppades för alla filresurser i det lagringskonto som du vill avregistrera.

Så här avregistrerar du lagringskontot:

1. Öppna valvet för Återställningstjänster där ditt lagringskonto är registrerat.
1. Välj alternativet **Säkerhetskopieringsinfrastruktur** i fönstret **Översikt** under avsnittet **Hantera.**

   ![Välja infrastruktur för säkerhetskopiering](./media/manage-afs-backup/backup-infrastructure.png)

1. Fönstret **Infrastruktur för säkerhetskopiering** öppnas. Välj **Lagringskonton** under avsnittet **Azure Storage Accounts.**

   ![Välj lagringskonton](./media/manage-afs-backup/storage-accounts.png)

1. När du har valt **Lagringskonton**visas en lista över lagringskonton som registrerats i valvet.
1. Högerklicka på det lagringskonto som du vill avregistrera och välj **Avregistrera**.

   ![Välj Avregistrera dig](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Felsöka säkerhetskopiering av Azure-filresurser](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files).
