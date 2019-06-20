---
title: Säkerhetskopiera Azure Files-filresurser med Azure Backup-tjänsten
description: Den här självstudien beskrivs hur du säkerhetskopierar Azure-filresurser.
services: backup
author: dcurwin
ms.author: dacurwin
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 474d5454e30c35d3f3ccf4ea994093ef47bd6ceb
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275994"
---
# <a name="back-up-azure-file-shares"></a>Säkerhetskopiera Azure-filresurser
Den här artikeln förklarar hur du använder Azure Portal för att säkerhetskopiera och återställa [Azure-filresurser](../storage/files/storage-files-introduction.md).

I den här guiden får du lära du dig att:
> [!div class="checklist"]
> * Konfigurera ett Recovery Services-valv till att säkerhetskopiera Azure Files
> * Köra en säkerhetskopiering på begäran för att skapa en återställningspunkt


## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du kan säkerhetskopiera en Azure-filresurs bör du kontrollera att den finns i någon av de [lagringskontotyper som stöds](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). När du har kontrollerat detta kan du skydda din filresurser.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Begränsningar för säkerhetskopiering av Azure-filresurser i förhandsversionen
Säkerhetskopiering för Azure-filresurser finns i förhandsversion. Azure-filresurser i både general-purpose v1- och general-purpose v2-lagringskonton stöds. Följande säkerhetskopieringsscenarier stöds inte för Azure-filresurser:
- Du kan inte skydda av Azure-filresurser i lagringskonton som har virtuella nätverk eller brandvägg aktiverade.
- Det finns ingen tillgänglig CLI som skyddar Azure Files med hjälp av Azure Backup.
- Det maximala antalet schemalagda säkerhetskopieringar per dag är en.
- Det maximala antalet säkerhetskopieringar på begäran per dag är fyra.
- Förhindra att säkerhetskopior i Recovery Services-valvet oavsiktligt tas bort genom att använda [resurslås](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) på lagringskontot.
- Ta inte bort ögonblicksbilder som skapats av Azure Backup. Om du tar bort ögonblicksbilder kan du förlora återställningspunkter och/eller drabbas av återställningsfel.
- Ta inte bort filresurser som skyddas av Azure Backup. Den aktuella lösningen tar bort alla ögonblicksbilder som har tagits av Azure Backup när filresursen har tagits bort och förlorar därför alla återställningspunkter

Tillbaka för Azure-filresurser i Lagringskonton med [zonredundant lagring](../storage/common/storage-redundancy-zrs.md) replikering (ZRS) är för närvarande endast tillgängliga i centrala USA (CUS), östra USA (EUS), östra USA 2 (EUS2), Norra Europa (NE), sydöstra Asien (SEA), västra Europa (WE) och USA, västra 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Konfigurera säkerhetskopiering för en Azure-filresurs
Den här självstudien förutsätter att du redan har skapat en filresurs i Azure. Säkerhetskopiera Azure-filresurser:

1. Skapa ett Recovery Services-valv i samma region som din filresurs. Om du redan har ett valv öppnar du valvets översiktssida och klickar på **Backup**.

    ![Klicka på säkerhetskopian i din valvets översiktssida](./media/backup-file-shares/overview-backup-page.png)

2. I den **säkerhetskopieringsmål** menyn från **vad vill du säkerhetskopiera?** , Välj Azure-filresursen.

    ![Välj Azure-filresursen som mål för säkerhetskopieringen](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Klicka på **Säkerhetskopiering** för att konfigurera Azure-filresursen till Recovery Services-valvet.

   ![klicka på säkerhetskopian för att associera Azure-filresursen med valvet](./media/backup-file-shares/set-backup-goal.png)

    När valvet är associerat med Azure-filresursen öppnas säkerhetskopieringsmenyn och du uppmanas att välja ett lagringskonto. Menyn visar alla Lagringskonton som stöds i den region där valvet finns och som inte redan är kopplade till ett Recovery Services-valv.

   ![Välj ditt lagringskonto](./media/backup-file-shares/list-of-storage-accounts.png)

4. Välj ett konto i listan med lagringskonton och klicka på **OK**. Azure söker i lagringskontot efter filresurser som kan säkerhetskopieras. Om du nyligen har lagt till filresurser och inte ser dem i listan, bör du vänta lite på att filresurserna ska visas.

   ![Filresurser identifieras](./media/backup-file-shares/discover-file-shares.png)

5. Från den **filresurser** väljer du en eller flera av de filresurser som du vill säkerhetskopiera klicka sedan på **OK**.

6. När du har valt dina filresurser växlar säkerhetskopieringsmenyn över till **Säkerhetskopieringspolicy**. I den här menyn väljer du antingen en befintlig säkerhetskopieringspolicy eller skapar en ny. Klicka sedan på **Aktivera säkerhetskopiering**.

   ![Välj en princip för säkerhetskopiering eller skapa ett nytt lösenord](./media/backup-file-shares/apply-backup-policy.png)

    När du har etablerat en säkerhetskopieringspolicy tas en ögonblicksbild av filresurserna vid den schemalagda tiden och en återställningspunkt sparas för den valda perioden.

## <a name="create-an-on-demand-backup"></a>Skapa en säkerhetskopiering på begäran
När du har konfigurerat principen för säkerhetskopiering, bör du skapa en säkerhetskopiering på begäran för att säkerställa att dina data skyddas förrän nästa schemalagda säkerhetskopiering.


### <a name="to-create-an-on-demand-backup"></a>Skapa en säkerhetskopiering på begäran

1. Öppna det Recovery Services-valv som innehåller filresursens återställningspunkter och klicka på **Säkerhetskopieringsobjekt**. En lista med typer av säkerhetskopieringsobjekt visas.

   ![Listan med Säkerhetskopieringsobjekt](./media/backup-file-shares/list-of-backup-items.png)

2. I listan väljer du **Azure Storage (Azure Files)** . Listan med Azure-filresurser visas.

   ![Lista över Azure-filresurser](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Välj önskad filresurs i listan med Azure-filresurser. Menyn Säkerhetskopieringsobjekt för den valda filresursen öppnas.

   ![Dela säkerhetskopiering objektmenyn för den valda filen](./media/backup-file-shares/backup-item-menu.png)

4. I menyn Säkerhetskopieringsobjekt klickar du på **Säkerhetskopiera nu**. Eftersom detta är en säkerhetskopiering på begäran finns det ingen bevarandeprincip som är associerad med återställningspunkten. Dialogrutan **Säkerhetskopiera nu** öppnas. Ange den senaste dag som du vill behålla återställningspunkten för.

   ![Välj datum för kvarhållning av återställningspunkt](./media/backup-file-shares/backup-now-menu.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du Azure Portal för att:

> [!div class="checklist"]
> * Konfigurera ett Recovery Services-valv till att säkerhetskopiera Azure Files
> * Köra en säkerhetskopiering på begäran för att skapa en återställningspunkt

Fortsätt till nästa artikel om att återställa från en säkerhetskopia av en Azure-filresurs.

> [!div class="nextstepaction"]
> [Återställa från en säkerhetskopia av Azure-filresurser](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
 
