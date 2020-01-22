---
title: Återställa Azure-filresurser
description: Lär dig hur du använder Azure Portal för att återställa en hel fil resurs eller vissa filer från en återställnings punkt som skapats av Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b16eb4120ff2d269135ae8ae6555ef4fdbdbda5d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294401"
---
# <a name="restore-azure-file-shares"></a>Återställa Azure-filresurser

Den här artikeln förklarar hur du använder Azure Portal för att återställa en hel fil resurs eller vissa filer från en återställnings punkt som skapats av tjänsten [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) .

I den här guiden får du lära dig att:

* Återställa en fullständig Azure-filresurs
* Återställa enskilda filer eller mappar
* Spåra status för återställnings åtgärden

## <a name="steps-to-perform-restore"></a>Steg för att utföra återställning

### <a name="select-the-file-share-to-restore"></a>Välj den fil resurs som ska återställas

1. I [Azure Portal](https://portal.azure.com/)öppnar du Recovery Services-valvet som du använde för att konfigurera säkerhets kopieringen för fil resursen.

2. Klicka på **säkerhets kopierings objekt** under avsnittet **skyddade objekt** på **översikts** bladet.

    ![Klicka på säkerhets kopierings objekt](./media/restore-afs/backup-items.png)

3. När du klickar på **säkerhetskopiera objekt**visas ett nytt blad med alla typer av säkerhets kopierings hantering bredvid **översikts** bladet som visas nedan:

    ![Typer av säkerhets kopierings hantering](./media/restore-afs/backup-management.png)

4. I **säkerhets kopierings objekt**, under **säkerhets kopierings hanterings typ**, väljer du **Azure Storage (Azure Files)** . Du ser en lista över alla fil resurser och deras motsvarande lagrings konto som säkerhets kopie ras med det här valvet.

    ![Lista över alla fil resurser](./media/restore-afs/file-shares.png)

5. I listan över Azure-filresurser väljer du önskad fil resurs som du vill utföra återställnings åtgärden för.

### <a name="full-share-recovery"></a>Fullständig delnings återställning

Du kan använda det här alternativet för återställning för att återställa hela fil resursen på den ursprungliga platsen eller en annan plats.

1. Välj alternativet för att **återställa delning** från bladet **säkerhets kopierings objekt** som visas när du har valt den önskade fil resursen som ska återställas i steg 5 i avsnittet [Välj den fil resurs som ska återställas](#select-the-file-share-to-restore) .

   ![Välj återställnings resurs](./media/restore-afs/restore-share.png)

2. När du klickar på **Återställ delning**öppnas bladet **Återställ** med en återställnings **punkt** meny som visar en lista över återställnings punkter som är tillgängliga för den valda fil resursen.

3. Välj den återställnings punkt som du vill använda för att utföra återställnings åtgärden och klicka på **OK**.

    ![Välj återställnings punkt](./media/restore-afs/restore-point.png)

4. När du klickar på OK växlar blad menyn Återställ till **återställnings plats**. I **återställnings plats**anger du var (eller hur) du vill återställa data. Du kan välja något av följande två alternativ:

    * **Ursprunglig plats**: Återställ hela fil resursen till samma plats som den ursprungliga källan.
    * **Alternativ plats**: Återställ hela fil resursen till en annan plats och behåll den ursprungliga fil resursen **som den är**.

#### <a name="restore-to-original-location"></a>Återställ till ursprunglig plats

1. Välj den **ursprungliga platsen** som **återställnings mål** och välj om du vill hoppa över eller skriva över om det finns konflikter. Klicka på **OK** när du har gjort ett lämpligt val.

    ![Välj ursprunglig plats](./media/restore-afs/original-location.png)

2. Starta återställnings åtgärden genom att klicka på **Återställ** .

    ![Klicka på Återställ för att starta](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

1. Välj en **annan plats** som återställnings mål.
2. Välj det mål lagrings konto där du vill återställa det säkerhetskopierade innehållet på den nedrullningsbara menyn i fältet **lagrings konto** .
3. Baserat på det lagrings konto som du valde i steg 2, visas listan över fil resurser som finns i det valda lagrings kontot i list rutan **Välj fil resurs** . Välj den fil resurs där du vill återställa det säkerhetskopierade innehållet.
4. I fältet **mappnamn** anger du namnet på mappen som du vill skapa i mål fil resursen med det återställda innehållet.
5. Välj om du vill hoppa över eller skriva över om det finns konflikter.
6. Klicka på **OK** när du har angett lämpliga värden i alla fält.

    ![Välj alternativ plats](./media/restore-afs/alternate-location.png)

7. Starta återställnings åtgärden genom att klicka på Återställ.

    ![Klicka på Återställ för att starta](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Återställning på objekt nivå

Du kan använda det här alternativet för återställning för att återställa enskilda filer eller mappar på den ursprungliga platsen eller en annan plats.

1. Välj alternativet **fil återställning** på bladet **säkerhets kopierings objekt** som visas när du har valt den önskade fil resursen som ska återställas i steg 5 i avsnittet [Välj den fil resurs som ska återställas](#select-the-file-share-to-restore) .

    ![Välj fil återställning](./media/restore-afs/file-recovery.png)

2. När du klickar på **fil återställning**öppnas bladet **Återställ** med en menyn återställnings **punkt** och en lista över återställnings punkter som är tillgängliga för den valda fil resursen visas.

3. Välj den återställnings punkt som du vill använda för att utföra återställnings åtgärden och klicka på **OK**.

    ![Välj återställnings punkt](./media/restore-afs/restore-point.png)

4. När du klickar på OK växlar blad menyn Återställ till **återställnings plats**. I **återställnings plats**anger du var (eller hur) du vill återställa data. Du kan välja något av följande två alternativ:

    * **Ursprunglig plats**: Återställ valda filer/mappar till samma fil resurs som den ursprungliga källan.
    * **Alternativ plats**: Återställ valda filer/mappar till en annan plats och behåll det ursprungliga fil **resurs innehållet.**

#### <a name="restore-to-original-location"></a>Återställa till ursprunglig plats

1. Välj den **ursprungliga platsen** som **återställnings mål** och välj om du vill hoppa över eller skriva över om det finns konflikter.

    ![Ursprunglig plats för återställning på objekt nivå](./media/restore-afs/original-location-item-level.png)

2. Klicka på **Välj fil** för att välja de filer/mappar som du vill återställa.

    ![Klicka på Välj fil](./media/restore-afs/select-file.png)

3. När du klickar på **Välj fil**visas ett blad för fil resurs som visar innehållet i den återställnings punkt för fil resursen som du har valt för återställning.

4. Markera rutan som motsvarar filen/mappen som du vill återställa och klicka på **Välj**.

    ![Välj fil eller mapp](./media/restore-afs/select-file-folder.png)

5. Upprepa steg 2-4 för att välja flera filer/mappar för återställning.
6. När du har valt alla objekt som du vill återställa klickar du på **OK**.

    ![När du har valt alla objekt som ska återställas klickar du på OK](./media/restore-afs/after-selecting-items.png)

7. Starta återställnings åtgärden genom att klicka på Återställ.

    ![Klicka på Återställ för att starta](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

1. Välj en **annan plats** som återställnings mål.
2. Välj det mål lagrings konto där du vill återställa det säkerhetskopierade innehållet på den nedrullningsbara menyn i fältet **lagrings konto** .
3. Baserat på det lagrings konto som du valde i steg 2, visas en lista över de fil resurser som finns i det valda lagrings kontot i list rutan **Välj fil resurs** . Välj den fil resurs där du vill återställa det säkerhetskopierade innehållet.
4. I fältet **mappnamn** anger du namnet på mappen som du vill skapa i mål fil resursen med det återställda innehållet.
5. Välj om du vill hoppa över eller skriva över om det finns konflikter.
6. Klicka på **Välj fil** för att välja de filer/mappar som du vill återställa.

    ![Välj objekt som ska återställas till en annan plats](./media/restore-afs/restore-to-alternate-location.png)

7. När du klickar på **Välj fil**visas ett blad för fil resurs som visar innehållet i den återställnings punkt för fil resursen som du har valt för återställning.
8. Markera rutan som motsvarar filen/mappen som du vill återställa och klicka på **Välj**.

    ![Välj återställnings mål](./media/restore-afs/recovery-destination.png)

9. Upprepa steg 6-8 för att välja flera filer/mappar för återställning.
10. När du har valt alla objekt som du vill återställa klickar du på **OK**.

    [Klicka på OK när du har valt alla filer](./media/restore-afs/after-selecting-all-items.png)

11. Starta återställnings åtgärden genom att klicka på **Återställ** .

## <a name="track-restore-operation"></a>Spåra återställnings åtgärd

När du har utlöst återställnings åtgärden skapar säkerhets kopierings tjänsten ett jobb för spårning. Azure Backup visar meddelanden om jobbet i portalen. Klicka på hyperlänken meddelanden om du vill visa åtgärder för jobbet.

![Klicka på aviseringar hyperlänk](./media/restore-afs/notifications-link.png)

Du kan också övervaka återställnings förloppet från Recovery Services-valvet. Här följer stegen för att kontrol lera status för återställnings åtgärden:

1. Öppna det Recovery Services valv från vilket du utlöste återställnings åtgärden.
2. Klicka på **säkerhets kopierings jobb** under **avsnittet övervakning** i bladet **Översikt** för att se statusen för åtgärder som körs mot olika arbets belastningar.

    ![Klicka på säkerhets kopierings jobb](./media/restore-afs/backup-jobs.png)

3. Klicka på det arbets belastnings namn som motsvarar din fil resurs för att visa mer information om återställnings åtgärden, t. ex. överförda data, antal återställda filer osv.

    ![Se återställd information](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [hanterar säkerhets kopior av Azure-filresurser](manage-afs-backup.md)
