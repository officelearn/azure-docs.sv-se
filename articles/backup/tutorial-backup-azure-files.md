---
title: Självstudiekurs - Säkerhetskopiera Filresurser för Azure Files
description: I den här självstudien kan du läsa om hur du använder Azure-portalen för att konfigurera ett Recovery Services-valv och säkerhetskopiera Azure-filresurser.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: ec9074a39f2ece7878c0c3ef828dc21748d0ab89
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76293937"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Säkerhetskopiera Azure-filresurser i Azure-portalen

Den här självstudien förklarar hur du använder Azure-portalen för att säkerhetskopiera [Azure-filresurser](../storage/files/storage-files-introduction.md).

I den här guiden får du lära du dig att:
> [!div class="checklist"]
>
> * Konfigurera ett Recovery Services-valv till att säkerhetskopiera Azure Files
> * Köra en säkerhetskopiering på begäran för att skapa en återställningspunkt

## <a name="prerequisites"></a>Krav

Innan du kan säkerhetskopiera en Azure-filresurs bör du kontrollera att den finns i någon av de [lagringskontotyper som stöds](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). När du har kontrollerat detta kan du skydda din filresurser.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Begränsningar för säkerhetskopiering av Azure-fildelning under förhandsversionen

Säkerhetskopiering för Azure-filresurser finns i förhandsversion. Azure-filresurser i både general-purpose v1- och general-purpose v2-lagringskonton stöds. Följande säkerhetskopieringsscenarier stöds inte för Azure-filresurser:

* Det finns ingen tillgänglig CLI som skyddar Azure Files med hjälp av Azure Backup.
* Det maximala antalet schemalagda säkerhetskopieringar per dag är en.
* Det maximala antalet säkerhetskopieringar på begäran per dag är fyra.
* Använd [resurslås](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) på lagringskontot för att förhindra oavsiktlig borttagning av säkerhetskopior i ditt Recovery Services-valv.
* Ta inte bort ögonblicksbilder som skapats av Azure Backup. Om du tar bort ögonblicksbilder kan du förlora återställningspunkter och/eller drabbas av återställningsfel.
* Ta inte bort filresurser som skyddas av Azure Backup. Den aktuella lösningen tar bort alla ögonblicksbilder som har tagits av Azure Backup när filresursen har tagits bort och förlorar därför alla återställningspunkter

Säkerhetskopiering för Azure File Shares i lagringskonton med [zon redundant lagring](../storage/common/storage-redundancy-zrs.md) (ZRS) replikering är för närvarande endast tillgängligt i Centrala USA (CUS), Östra USA (EUS), Östra USA 2 (EUS2), North Europe (NE), SouthEast Asia (SEA), West Europe (WE) och West US 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Konfigurera säkerhetskopiering för en Azure-filresurs

Den här självstudien förutsätter att du redan har skapat en filresurs i Azure. Säkerhetskopiera Azure-filresurser:

1. Skapa ett Recovery Services-valv i samma region som din filresurs. Om du redan har ett valv öppnar du valvets översiktssida och klickar på **Backup**.

    ![Klicka på Säkerhetskopiering på översiktssidan för valvet](./media/tutorial-backup-azure-files/overview-backup-page.png)

2. På menyn Mål för säkerhetskopiering väljer du Azure FileShare på menyn **Säkerhetskopieringsmål?** **What do you want to back up?**

    ![Välj Azure-filresursen som mål för säkerhetskopieringen](./media/tutorial-backup-azure-files/choose-azure-fileshare-from-backup-goal.png)

3. Klicka på **Säkerhetskopiering** om du vill konfigurera Azure-filresursen till ditt Recovery Services-valv.

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/tutorial-backup-azure-files/set-backup-goal.png)

    När valvet är associerat med Azure-filresursen öppnas menyn Säkerhetskopiering och uppmanar dig att välja ett lagringskonto. Menyn visar alla lagringskonton som stöds i den region där valvet finns och som inte redan är associerade med ett Recovery Services-valv.

   ![Välj ditt lagringskonto](./media/tutorial-backup-azure-files/list-of-storage-accounts.png)

4. Välj ett konto i listan med lagringskonton och klicka på **OK**. Azure söker i lagringskontot efter filresurser som kan säkerhetskopieras. Om du nyligen har lagt till filresurser och inte ser dem i listan, bör du vänta lite på att filresurserna ska visas.

   ![Filresurser upptäcks](./media/tutorial-backup-azure-files/discover-file-shares.png)

5. Välj en eller flera av de filresurser som du vill säkerhetskopiera i listan **Filresurser** och klicka på **OK**.

6. När du har valt dina filresurser växlar säkerhetskopieringsmenyn över till **Säkerhetskopieringspolicy**. I den här menyn väljer du antingen en befintlig säkerhetskopieringspolicy eller skapar en ny. Klicka sedan på **Aktivera säkerhetskopiering**.

   ![Välj en princip för säkerhetskopiering eller skapa en ny](./media/tutorial-backup-azure-files/apply-backup-policy.png)

    När du har etablerat en säkerhetskopieringspolicy tas en ögonblicksbild av filresurserna vid den schemalagda tiden och en återställningspunkt sparas för den valda perioden.

## <a name="create-an-on-demand-backup"></a>Skapa en säkerhetskopiering på begäran

När du har konfigurerat säkerhetskopieringsprincipen vill du skapa en säkerhetskopiering på begäran för att säkerställa att dina data skyddas tills nästa schemalagda säkerhetskopiering.

### <a name="to-create-an-on-demand-backup"></a>Skapa en säkerhetskopiering på begäran

1. Öppna det Recovery Services-valv som innehåller filresursens återställningspunkter och klicka på **Säkerhetskopieringsobjekt**. En lista med typer av säkerhetskopieringsobjekt visas.

   ![Lista över objekt för säkerhetskopiering](./media/tutorial-backup-azure-files/list-of-backup-items.png)

2. I listan väljer du **Azure Storage (Azure Files)**. Listan med Azure-filresurser visas.

   ![Lista över Azure-filresurser](./media/tutorial-backup-azure-files/list-of-azure-files-backup-items.png)

3. Välj önskad filresurs i listan med Azure-filresurser. Menyn Säkerhetskopieringsobjekt för den valda filresursen öppnas.

   ![Menyn Säkerhetskopieringsobjekt för den valda filresursen](./media/tutorial-backup-azure-files/backup-item-menu.png)

4. I menyn Säkerhetskopieringsobjekt klickar du på **Säkerhetskopiera nu**. Eftersom detta är en säkerhetskopiering på begäran finns det ingen bevarandeprincip som är associerad med återställningspunkten. Dialogrutan **Säkerhetskopiera nu** öppnas. Ange den senaste dag som du vill behålla återställningspunkten för.

   ![Välj datum för kvarhållning av återställningspunkt](./media/tutorial-backup-azure-files/backup-now-menu.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du Azure Portal för att:

> [!div class="checklist"]
>
> * Konfigurera ett Recovery Services-valv till att säkerhetskopiera Azure Files
> * Köra en säkerhetskopiering på begäran för att skapa en återställningspunkt

Fortsätt till nästa artikel för att återställa från en säkerhetskopia av en Azure-filresurs.

> [!div class="nextstepaction"]
> [Återställa från säkerhetskopiering av Azure-filresurser](restore-afs.md)
