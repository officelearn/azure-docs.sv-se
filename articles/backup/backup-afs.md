---
title: Säkerhetskopiera Azure-filresurser i Azure Portal
description: Lär dig hur du använder Azure Portal för att säkerhetskopiera Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 58bcd7ecd7c6fac80f5b78fb2c8b568b63e3e1b2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077142"
---
# <a name="back-up-azure-file-shares"></a>Säkerhetskopiera Azure-filresurser

Den här artikeln förklarar hur du använder Azure Portal för att säkerhetskopiera [Azure-filresurser](../storage/files/storage-files-introduction.md).

I den här artikeln får du lära dig att:

* Skapa ett Recovery Services-valv.
* Identifiera fil resurser och konfigurera säkerhets kopior.
* Kör ett säkerhets kopierings jobb på begäran för att skapa en återställnings punkt.

## <a name="prerequisites"></a>Förutsättningar

* Identifiera eller skapa ett [Recovery Services valv](#create-a-recovery-services-vault) i samma region som det lagrings konto som är värd för fil resursen.
* Se till att fil resursen finns i någon av de [typer av lagrings konton som stöds](azure-file-share-support-matrix.md).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-file-shares-and-configure-backup"></a>Identifiera fil resurser och konfigurera säkerhets kopiering

1. I [Azure Portal](https://portal.azure.com/)öppnar du Recovery Services-valvet som du vill använda för att konfigurera säkerhets kopiering för fil resursen.

1. I fönstret **Recovery Services valv** väljer du **+ säkerhetskopiera** på menyn högst upp.

   ![Recovery Services-valv](./media/backup-afs/recovery-services-vault.png)

    1. I fönstret **säkerhets kopierings mål** anger **du var arbets belastningen körs?** till **Azure** genom att välja alternativet **Azure** i list rutan.

          ![Välj Azure som arbets belastning](./media/backup-afs/backup-goal.png)

    2. I **vad vill du säkerhetskopiera? väljer du** **Azure-filresurs** i list rutan.

          ![Välj Azure-FileShare](./media/backup-afs/select-azure-file-share.png)

    3. Välj **säkerhets kopiering** för att registrera tillägget Azure File Share i valvet.

          ![Välj säkerhets kopiering för att associera Azure-filresursen med valvet](./media/backup-afs/register-extension.png)

1. När du har valt **säkerhets kopiering**öppnas fönstret **säkerhets kopiering** . Välj det lagrings konto som är värd för den fil resurs som du vill skydda genom att klicka på länken **Välj** länk under **lagrings kontots** text ruta.

   ![Välj länken Välj](./media/backup-afs/choose-select-link.png)

1. **Rutan Välj lagrings konto** öppnas till höger och listar en uppsättning identifierade lagrings konton som stöds. De är antingen associerade med det här valvet eller finns i samma region som valvet, men har ännu inte kopplats till något Recovery Services-valv.

1. I listan över identifierade lagrings konton väljer du ett konto och väljer **OK**.

   ![Välj bland de identifierade lagrings kontona](./media/backup-afs/select-discovered-storage-account.png)

1. Nästa steg är att välja de fil resurser som du vill säkerhetskopiera. Klicka på knappen **Lägg till** i avsnittet **fil resurser to backup** .

   ![Välj de fil resurser som ska säkerhets kopie ras](./media/backup-afs/select-file-shares-to-back-up.png)

1. Fönstret **Välj fildelnings** kontext öppnas till höger. Azure söker i lagrings kontot efter fil resurser som kan säkerhets kopie ras. Om du nyligen har lagt till dina fil resurser och inte ser dem i listan, kan det vara en tid för fil resurserna att visas.

1. I listan **Välj fil resurser** väljer du en eller flera av de fil resurser som du vill säkerhetskopiera. Välj **OK**.

   ![Välj fil resurser](./media/backup-afs/select-file-shares.png)

1. Du kan välja en säkerhets kopierings princip för fil resursen med tre alternativ:

   * Välj standard principen.<br>
   Med det här alternativet kan du aktivera daglig säkerhets kopiering som ska behållas i 30 dagar. Om du inte har en befintlig säkerhets kopierings princip i valvet öppnas säkerhets kopierings fönstret med standard princip inställningarna. Om du vill välja standardinställningar kan du klicka direkt på **Aktivera säkerhets kopiering**.

   * Skapa en ny policy <br>

      1. Om du vill skapa en ny säkerhets kopierings princip för fil resursen klickar du på länk texten under List rutan i avsnittet **säkerhets kopierings princip** .<br>

         ![Skapa ny princip](./media/backup-afs/create-new-policy.png)

      1. Kontext fönstret för **säkerhets kopierings principen** öppnas till höger. Ange ett princip namn i text rutan och välj kvarhållningsperioden enligt ditt krav. Endast alternativet för daglig kvarhållning är aktiverat som standard. Om du vill ha veckovis, månatlig eller årlig kvarhållning markerar du motsvarande kryss ruta och anger önskat kvarhållningsintervall.

      1. När du har angett värdena för kvarhållning och ett giltigt princip namn klickar du på OK.<br>

         ![Ange princip namn och bevarande värden](./media/backup-afs/policy-name.png)

   * Välj en av de befintliga säkerhets kopierings principerna <br>

   Om du vill välja en av de befintliga säkerhets kopierings principerna för att konfigurera skydd väljer du önskad princip i list rutan **säkerhets kopierings princip** .<br>

   ![Välj en befintlig princip](./media/backup-afs/choose-existing-policy.png)

1. Klicka på **Aktivera säkerhets kopiering** för att börja skydda fil resursen.

   ![Välj Aktivera säkerhets kopiering](./media/backup-afs/enable-backup.png)

När du har angett en säkerhets kopierings princip tas en ögonblicks bild av fil resurserna vid den schemalagda tiden. Återställnings punkten behålls även för den valda perioden.

>[!NOTE]
>Azure Backup stöder nu principer med dagliga/veckovis/månads Visa/årliga kvarhållning för säkerhets kopiering av Azure-filresurser.

## <a name="create-an-on-demand-backup"></a>Skapa en säkerhetskopiering på begäran

Ibland kanske du vill skapa en ögonblicks bild av en säkerhets kopia eller återställnings punkt utanför de tider som schemalagts i säkerhets kopierings principen. En vanlig orsak till att generera en säkerhets kopiering på begäran är direkt efter att du har konfigurerat säkerhets kopierings principen. Baserat på schemat i säkerhets kopierings policyn kan det vara timmar eller dagar tills en ögonblicks bild tas. För att skydda dina data fram tills säkerhetskopieringspolicyn aktiveras, bör du starta en säkerhetskopiering på begäran. Det krävs ofta att du skapar en säkerhets kopiering på begäran innan du gör några planerade ändringar i fil resurserna.

### <a name="create-a-backup-job-on-demand"></a>Skapa ett säkerhets kopierings jobb på begäran

1. Öppna det Recovery Services valv som du använde för att säkerhetskopiera fil resursen. I **översikts** fönstret väljer du **säkerhets kopierings objekt** under avsnittet **skyddade objekt** .

   ![Välj säkerhets kopierings objekt](./media/backup-afs/backup-items.png)

1. När du har valt **säkerhets kopierings objekt**visas ett nytt fönster med en lista med alla **typer av säkerhets kopierings hantering** bredvid **översikts** fönstret.

   ![Lista över säkerhets kopierings hanterings typer](./media/backup-afs/backup-management-types.png)

1. Välj **Azure Storage (Azure Files)** i listan **typ av säkerhets kopierings hantering** . Du ser en lista över alla fil resurser och motsvarande lagrings konton som har säkerhetskopierats med hjälp av det här valvet.

   ![Azure Storage (Azure Files) säkerhetskopiera objekt](./media/backup-afs/azure-files-backup-items.png)

1. I listan över Azure-filresurser väljer du den fil resurs som du vill använda. Information om **säkerhets kopierings objekt** visas. På menyn **säkerhets kopierings objekt** väljer du **Säkerhetskopiera nu**. Eftersom det här säkerhets kopierings jobbet är på begäran finns det ingen bevarande princip som är associerad med återställnings punkten.

   ![Välj Säkerhetskopiera nu](./media/backup-afs/backup-now.png)

1. Fönstret **Säkerhetskopiera nu** öppnas. Ange den senaste dag som du vill behålla återställningspunkten för. Du kan ha en maximal kvarhållning på 10 år för en säkerhets kopiering på begäran.

   ![Välj kvarhållningsdatum](./media/backup-afs/retention-date.png)

1. Välj **OK** för att bekräfta säkerhets kopierings jobbet på begäran som körs.

1. Övervaka Portal meddelanden för att hålla reda på hur du slutför körningen av säkerhets kopierings jobb. Du kan övervaka jobb förloppet i valv-instrumentpanelen. Välj **pågående säkerhets kopierings jobb**  >  **In progress**.

>[!NOTE]
>Azure Backup låser lagrings kontot när du konfigurerar skydd för alla fil resurser i motsvarande konto. Detta ger skydd mot oavsiktlig borttagning av ett lagrings konto med säkerhetskopierade fil resurser.

## <a name="best-practices"></a>Bästa praxis

* Ta inte bort ögonblicks bilder som skapats av Azure Backup. Om du tar bort ögonblicksbilder kan du förlora återställningspunkter och/eller drabbas av återställningsfel.

* Ta inte bort låset som gjorts på lagrings kontot genom att Azure Backup. Om du tar bort låset är ditt lagrings konto känsligt för oavsiktlig borttagning och om det tas bort går Dina ögonblicks bilder eller säkerhets kopior förlorade.

## <a name="next-steps"></a>Nästa steg

Lär dig att:

* [Återställa Azure-filresurser](restore-afs.md)
* [Hantera säkerhets kopior av Azure-filresurser](manage-afs-backup.md)
