---
title: Säkerhetskopiera Azure-filresurser i Azure Portal
description: Lär dig hur du använder Azure Portal för att säkerhetskopiera Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938219"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Säkerhetskopiera Azure-filresurser i ett Recovery Services valv

Den här artikeln förklarar hur du använder Azure Portal för att säkerhetskopiera [Azure-filresurser](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

I den här artikeln får du lära dig att:

* Skapa ett Recovery Services-valv.
* Identifiera fil resurser och konfigurera säkerhets kopior.
* Kör ett säkerhets kopierings jobb på begäran för att skapa en återställnings punkt.

## <a name="prerequisites"></a>Krav

* Identifiera eller skapa ett [Recovery Services valv](#create-a-recovery-services-vault) i samma region som det lagrings konto som är värd för fil resursen.
* Se till att fil resursen finns i någon av de [typer av lagrings konton som stöds](#limitations-for-azure-file-share-backup-during-preview).

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Begränsningar för säkerhets kopiering av Azure-filresurs under för hands versionen

Säkerhetskopiering för Azure-filresurser finns i förhandsversion. Azure-filresurser i både general-purpose v1- och general-purpose v2-lagringskonton stöds. Här följer några begränsningar för att säkerhetskopiera Azure-fil resurser:

* Stöd för säkerhets kopiering av Azure-filresurser i lagrings konton med ZRS-replikering ( [Zone-redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) ) är för närvarande begränsat till [dessa regioner](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* Azure Backup stöder för närvarande att konfigurera schemalagda en gång – daglig säkerhets kopiering av Azure-filresurser.
* Det maximala antalet schemalagda säkerhetskopieringar per dag är en.
* Det maximala antalet säkerhetskopieringar på begäran per dag är fyra.
* Förhindra att säkerhetskopior i Recovery Services-valvet oavsiktligt tas bort genom att använda [resurslås](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) på lagringskontot.
* Ta inte bort ögonblicks bilder som skapats av Azure Backup. Om du tar bort ögonblicks bilder kan det leda till förlust av återställnings punkter eller återställnings problem.
* Ta inte bort fil resurser som skyddas av Azure Backup. Den aktuella lösningen tar bort alla ögonblicks bilder som tas av Azure Backup när fil resursen har tagits bort, så alla återställnings punkter går förlorade.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Ändra Storage Replication

Som standard använder valven [Geo-redundant lagring (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Om valvet är din primära mekanism för säkerhets kopiering rekommenderar vi att du använder GRS.
* Du kan använda [Lokalt Redundant lagring (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) som ett alternativ med låg kostnad.

Så här ändrar du typen av lagrings replikering:

1. I det nya valvet väljer du **Egenskaper** under avsnittet **Inställningar** .

1. På sidan **Egenskaper** under **säkerhets kopierings konfiguration**väljer du **Uppdatera**.

1. Välj typ av lagrings replikering och välj **Spara**.

    ![Uppdatera säkerhets kopierings konfiguration](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Du kan inte ändra typen av lagringsprovider när valvet har kon figurer ATS och innehåller säkerhets kopierings objekt. Om du vill göra det måste du återskapa valvet.
>

## <a name="discover-file-shares-and-configure-backup"></a>Identifiera fil resurser och konfigurera säkerhets kopiering

1. I [Azure Portal](https://portal.azure.com/)öppnar du Recovery Services-valvet som du vill använda för att säkerhetskopiera fil resursen.

1. I instrument panelen för **Recovery Services valv** väljer du **+ säkerhetskopiera**.

   ![Recovery Services-valv](./media/backup-afs/recovery-services-vault.png)

    a. I **säkerhets kopierings mål**anger **du var din arbets belastning körs?** till **Azure**.

    ![Välj Azure-filresurs som säkerhets kopierings mål](./media/backup-afs/backup-goal.png)

    b.  I **vad vill du säkerhetskopiera? väljer du** **Azure-filresurs** i list rutan.

    c.  Välj **säkerhets kopiering** för att registrera tillägget Azure File Share i valvet.

    ![Välj säkerhets kopiering för att associera Azure-filresursen med valvet](./media/backup-afs/register-extension.png)

1. När du har valt **säkerhets kopiering**öppnas fönstret **säkerhets kopiering** där du kan välja ett lagrings konto från en lista över identifierade lagrings konton som stöds. De är antingen associerade med det här valvet eller finns i samma region som valvet, men har ännu inte kopplats till något Recovery Services-valv.

   ![Välj lagringskonto](./media/backup-afs/select-storage-account.png)

1. I listan över identifierade lagrings konton väljer du ett konto och väljer **OK**. Azure söker i lagrings kontot efter fil resurser som kan säkerhets kopie ras. Om du nyligen har lagt till dina fil resurser och inte ser dem i listan, kan det vara en tid för fil resurserna att visas.

    ![Identifiera fil resurser](./media/backup-afs/discovering-file-shares.png)

1. I listan **fil resurser** väljer du en eller flera av de fil resurser som du vill säkerhetskopiera. Välj **OK**.

1. När du har valt fil resurserna växlar **säkerhets kopierings** menyn till **säkerhets kopierings principen**. Från den här menyn väljer du antingen en befintlig säkerhets kopierings princip eller skapar en ny. Välj sedan **Aktivera säkerhets kopiering**.

    ![Välja säkerhetskopieringspolicy](./media/backup-afs/select-backup-policy.png)

När du har angett en säkerhets kopierings princip tas en ögonblicks bild av fil resurserna vid den schemalagda tiden. Återställnings punkten behålls även för den valda perioden.

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

1. Övervaka Portal meddelanden för att hålla reda på hur du slutför körningen av säkerhets kopierings jobb. Du kan övervaka jobb förloppet i valv-instrumentpanelen. Välj **säkerhets kopierings jobb** **som > pågår**.

## <a name="next-steps"></a>Nästa steg

Lär dig att:
* [Återställa Azure-filresurser](restore-afs.md)
* [Hantera säkerhets kopior av Azure-filresurser](manage-afs-backup.md)
