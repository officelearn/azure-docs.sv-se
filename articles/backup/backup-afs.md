---
title: Säkerhetskopiera Azure-filresurser i Azure Portal
description: Lär dig hur du använder Azure Portal för att säkerhetskopiera Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b2e2053857147513a95b3ae72b82d55450ebcffa
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294518"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Säkerhetskopiera Azure-filresurser i ett Recovery Services valv

Den här artikeln förklarar hur du använder Azure Portal för att säkerhetskopiera [Azure-filresurser](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

I den här guiden får du lära dig att:

* skapar ett Recovery Services-valv
* Identifiera fil resurser och konfigurera säkerhets kopior
* Köra en säkerhetskopiering på begäran för att skapa en återställningspunkt

## <a name="prerequisites"></a>Krav

* Identifiera eller skapa ett [Recovery Services valv](#create-a-recovery-services-vault) i samma region som det lagrings konto som är värd för fil resursen.

* Se till att fil resursen finns i någon av de [typer av lagrings konton som stöds](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Begränsningar för säkerhetskopiering av Azure-filresurser i förhandsversionen

Säkerhetskopiering för Azure-filresurser är i förhandsversion. Azure-filresurser i både general-purpose v1- och general-purpose v2-lagringskonton stöds. Detta är begränsningar för säkerhets kopiering av Azure-filresurser:

* Stöd för säkerhets kopiering av Azure-filresurser i lagrings konton med ZRS-replikering ( [Zone redundant Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) ) är för närvarande begränsat till [dessa regioner](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Azure Backup stöder för närvarande att konfigurera schemalagda en gång – daglig säkerhets kopiering av Azure-filresurser.
* Det maximala antalet schemalagda säkerhetskopieringar per dag är en.
* Det maximala antalet säkerhetskopieringar på begäran per dag är fyra.
* Förhindra att säkerhetskopior i Recovery Services-valvet oavsiktligt tas bort genom att använda [resurslås](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) på lagringskontot.
* Ta inte bort ögonblicksbilder som skapats av Azure Backup. Om du tar bort ögonblicksbilder kan du förlora återställningspunkter och/eller drabbas av återställningsfel.
* Ta inte bort filresurser som skyddas av Azure Backup. Den aktuella lösningen tar bort alla ögonblicks bilder som tas av Azure Backup när fil resursen har tagits bort och därför kommer alla återställnings punkter att gå förlorade.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Ändra Storage Replication

Som standard använder valven [Geo-redundant lagring (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Om valvet är din primära mekanism för säkerhets kopiering rekommenderar vi att du använder GRS.

* Du kan använda [Lokalt Redundant lagring (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) som ett alternativ med låg kostnad.

Ändra lagrings replik typen enligt följande:

1. Klicka på **Egenskaper** i avsnittet **Inställningar** i det nya valvet.

2. I **Egenskaper**, under **säkerhets kopierings konfiguration**, klickar du på **Uppdatera**.

3. Välj typ av lagrings replikering och klicka på **Spara**.

    ![Uppdatera säkerhets kopierings konfiguration](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Du kan inte ändra typen av lagringsprovider när valvet har kon figurer ATS och innehåller säkerhets kopierings objekt. Om du vill göra det måste du återskapa valvet.
>

## <a name="discover-file-shares-and-configure-backup"></a>Identifiera fil resurser och konfigurera säkerhets kopiering

1. I [Azure Portal](https://portal.azure.com/)öppnar du Recovery Services-valvet som du vill använda för att säkerhetskopiera fil resursen.

2. I instrument panelen för **Recovery Services valv** väljer du **+ säkerhetskopiera**.

   ![Recovery Services-valv](./media/backup-afs/recovery-services-vault.png)

   a. I **säkerhets kopierings mål**anger **du var din arbets belastning körs?** till **Azure**.

    ![Välj Azure-filresurs som säkerhets kopierings mål](./media/backup-afs/backup-goal.png)

    b.    I **vad vill du säkerhetskopiera väljer du**Azure- **filresurs** på den nedrullningsbara menyn.

    c.    Klicka på **säkerhets kopiering** för att registrera tillägget Azure-filresurs i valvet.

      ![Klicka på Säkerhetskopiera för att associera Azure-filresursen med valvet](./media/backup-afs/register-extension.png)

3. När du klickar på **säkerhets kopiering**öppnas bladet säkerhets kopiering och du blir ombedd att välja ett lagrings konto från en lista över identifierade lagrings konton som stöds. De är antingen associerade med valvet eller förekommer i samma region som valvet, men har ännu inte kopplats till något Recovery Services-valv.

   ![Välj lagringskonto](./media/backup-afs/select-storage-account.png)

4. I listan över identifierade lagrings konton väljer du ett konto och klickar på **OK**. Azure söker i lagringskontot efter filresurser som kan säkerhetskopieras. Om du nyligen har lagt till dina fil resurser och inte ser dem i listan, kan du ange en tid för fil resurserna som ska visas.

    ![Identifiera fil resurser](./media/backup-afs/discovering-file-shares.png)

5. I listan **fil resurser** väljer du en eller flera av de fil resurser som du vill säkerhetskopiera och klickar på **OK**.

6. När du har valt fil resurserna växlar **säkerhets kopierings** menyn till **säkerhets kopierings principen**. Från den här menyn väljer du antingen en befintlig säkerhets kopierings policy eller skapar en ny och klickar sedan på **Aktivera säkerhets kopiering**.

    ![Välja säkerhetskopieringspolicy](./media/backup-afs/select-backup-policy.png)

När du har etablerat en säkerhetskopieringspolicy tas en ögonblicksbild av filresurserna vid den schemalagda tiden och en återställningspunkt sparas för den valda perioden.

## <a name="create-an-on-demand-backup"></a>Skapa en säkerhetskopiering på begäran

Ibland kanske du vill skapa en ögonblicks bild av en säkerhets kopia eller återställnings punkt utanför de tider som schemalagts i säkerhets kopierings principen. En vanlig orsak till att generera en säkerhets kopiering på begäran är direkt efter att du har konfigurerat säkerhets kopierings principen. Baserat på schemat i säkerhetskopieringspolicyn kan det ta timmar eller dagar tills en ögonblicksbild tas. För att skydda dina data fram tills säkerhetskopieringspolicyn aktiveras, bör du starta en säkerhetskopiering på begäran. Det krävs ofta att du skapar en säkerhets kopiering på begäran innan du gör några planerade ändringar i fil resurserna.

### <a name="to-create-an-on-demand-backup"></a>Skapa en säkerhetskopiering på begäran

1. Öppna det Recovery Services valv som du använde för att säkerhetskopiera fil resursen och klicka på **säkerhets kopierings objekt** under avsnittet **skyddade objekt** på **översikts** bladet.

   ![Klicka på säkerhets kopierings objekt](./media/backup-afs/backup-items.png)

2. När du klickar på **säkerhetskopiera objekt**visas ett nytt blad med alla **säkerhets kopierings hanterings typer** bredvid **översikts** bladet på följande sätt:

   ![Lista över säkerhets kopierings hanterings typer](./media/backup-afs/backup-management-types.png)

3. I listan över **typer av säkerhets kopierings hantering**väljer du **Azure Storage (Azure Files)** . Du kommer att se en lista över alla fil resurser och motsvarande lagrings konton som har säkerhetskopierats med det här valvet.

   ![Azure Storage (Azure Files) säkerhetskopiera objekt](./media/backup-afs/azure-files-backup-items.png)

4. Välj önskad filresurs i listan med Azure-filresurser. Information om **säkerhets kopierings objekt** visas. Från menyn **säkerhets kopierings objekt** klickar du på **Säkerhetskopiera nu**. Eftersom detta är en säkerhetskopiering på begäran finns det ingen bevarandeprincip som är associerad med återställningspunkten.

   ![Klicka på Säkerhetskopiera nu](./media/backup-afs/backup-now.png)

5. Bladet **Säkerhetskopiera nu** öppnas. Ange den senaste dag som du vill behålla återställningspunkten för. Du kan ha en maximal kvarhållning på 10 år för en säkerhets kopiering på begäran.

   ![Välj kvarhållningsdatum](./media/backup-afs/retention-date.png)

6. Klicka på **OK** för att bekräfta att säkerhets kopierings jobbet körs på begäran.

7. Övervaka Portal meddelanden för att hålla reda på hur du slutför körningen av säkerhets kopierings jobb. Du kan övervaka jobb förloppet i valv instrument panelen > **säkerhets kopierings jobb** > **pågår**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer Azure-filresurser](restore-afs.md)

* Lär dig hur du [hanterar säkerhets kopior av Azure-filresurser](manage-afs-backup.md)
