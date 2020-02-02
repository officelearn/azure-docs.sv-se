---
title: Återställa Azure-filresurser
description: Lär dig hur du använder Azure Portal för att återställa en hel fil resurs eller vissa filer från en återställnings punkt som skapats av Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: 8ecb5850c90e5a92e59d17f7949bd5b6cd6205f5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930743"
---
# <a name="restore-azure-file-shares"></a>Återställa Azure-filresurser

Den här artikeln förklarar hur du använder Azure Portal för att återställa en hel fil resurs eller vissa filer från en återställnings punkt som skapats av [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

I den här artikeln får du lära dig att:

* Återställa en fullständig Azure-filresurs.
* Återställa enskilda filer eller mappar.
* Spåra återställnings åtgärdens status.

## <a name="steps-to-perform-a-restore-operation"></a>Steg för att utföra en återställnings åtgärd
Följ dessa steg om du vill utföra en återställnings åtgärd.

### <a name="select-the-file-share-to-restore"></a>Välj den fil resurs som ska återställas

1. I [Azure Portal](https://portal.azure.com/)öppnar du Recovery Services-valvet som du använde för att konfigurera säkerhets kopieringen för fil resursen.

1. I fönstret Översikt väljer du **säkerhets kopierings objekt** under avsnittet **skyddade objekt** .

    ![Välj säkerhets kopierings objekt](./media/restore-afs/backup-items.png)

1. När du har valt **säkerhets kopierings objekt**öppnas ett nytt fönster som visar alla typer av säkerhets kopierings hantering bredvid översikts fönstret.

    ![Typer av säkerhets kopierings hantering](./media/restore-afs/backup-management.png)

1. I rutan **säkerhets kopierings objekt** under **säkerhets kopierings hanterings typ**väljer du **Azure Storage (Azure Files)** . Du ser en lista över alla fil resurser och deras motsvarande lagrings konton som har säkerhetskopierats med hjälp av det här valvet.

    ![Lista över alla fil resurser](./media/restore-afs/file-shares.png)

1. I listan över Azure-filresurser väljer du den fil resurs som du vill utföra återställnings åtgärden för.

### <a name="full-share-recovery"></a>Fullständig delnings återställning

Du kan använda det här alternativet för återställning för att återställa hela fil resursen på den ursprungliga platsen eller en annan plats.

1. Välj alternativet för att **återställa delning** i rutan **säkerhets kopierings objekt** som visas när du har valt fil resursen att återställa i steg 5 i avsnittet [Välj den fil resurs som ska återställas](#select-the-file-share-to-restore) .

   ![Välj återställnings resurs](./media/restore-afs/restore-share.png)

1. När du har valt **Återställ delning**öppnas fönstret **Återställ** med en menyn återställnings **punkt** som visar en lista över återställnings punkter som är tillgängliga för den valda fil resursen.

1. Välj den återställnings punkt som du vill använda för att utföra återställnings åtgärden och välj **OK**.

    ![Välj återställnings punkt](./media/restore-afs/restore-point.png)

1. När du har valt **OK**växlar menyn **Återställ** panel till **återställnings plats**. I **återställnings plats**anger du var du vill återställa data. Välj något av följande två alternativ:

    * **Ursprunglig plats**: Återställ hela fil resursen till samma plats som den ursprungliga källan.
    * **Alternativ plats**: Återställ hela fil resursen till en annan plats och behåll den ursprungliga fil resursen som den är.

#### <a name="restore-to-the-original-location"></a>Återställ till den ursprungliga platsen

1. Välj den **ursprungliga platsen** som **återställnings mål**och välj om du vill hoppa över eller skriva över om det finns konflikter. När du har gjort ett lämpligt val väljer du **OK**.

    ![Välj ursprunglig plats](./media/restore-afs/original-location.png)

1. Starta återställnings åtgärden genom att välja **Återställ** .

    ![Välj Återställ för att starta](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

1. Välj en **annan plats** som **återställnings mål**.
1. Välj det mål lagrings konto där du vill återställa det säkerhetskopierade innehållet i list rutan **lagrings konto** .
1. List rutan **Välj fil resurs** visar de fil resurser som finns i det lagrings konto som du valde i steg 2. Välj den fil resurs där du vill återställa det säkerhetskopierade innehållet.
1. I rutan **mappnamn** anger du namnet på mappen som du vill skapa i mål fil resursen med det återställda innehållet.
1. Välj om du vill hoppa över eller skriva över om det finns konflikter.
1. När du har angett lämpliga värden i alla rutor väljer du **OK**.

    ![Välj alternativ plats](./media/restore-afs/alternate-location.png)

1. Starta återställnings åtgärden genom att välja **Återställ** .

    ![Välj Återställ för att starta](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Återställning på objekt nivå

Du kan använda det här alternativet för återställning om du vill återställa enskilda filer eller mappar på den ursprungliga platsen eller på en annan plats.

1. Välj alternativet **fil återställning** i rutan **säkerhets kopierings objekt** som visas när du har valt fil resursen att återställa i steg 5 i avsnittet [Välj den fil resurs som ska återställas](#select-the-file-share-to-restore) .

    ![Välj fil återställning](./media/restore-afs/file-recovery.png)

1. När du har valt **fil återställning**öppnas fönstret **Återställ** med en menyn återställnings **punkt** som visar en lista över återställnings punkter som är tillgängliga för den valda fil resursen.

1. Välj den återställnings punkt som du vill använda för att utföra återställnings åtgärden och välj **OK**.

    ![Välj återställnings punkt](./media/restore-afs/restore-point.png)

1. När du har valt **OK**växlar menyn Återställ panel till **återställnings plats**. I **återställnings plats**anger du var du vill återställa data. Välj något av följande två alternativ:

    * **Ursprunglig plats**: Återställ valda filer eller mappar till samma fil resurs som den ursprungliga källan.
    * **Alternativ plats**: Återställ valda filer eller mappar till en annan plats och behåll det ursprungliga fil resurs innehållet.

#### <a name="restore-to-the-original-location"></a>Återställ till den ursprungliga platsen

1. Välj den **ursprungliga platsen** som **återställnings mål**och välj om du vill hoppa över eller skriva över om det finns konflikter.

    ![Ursprunglig plats för återställning på objekt nivå](./media/restore-afs/original-location-item-level.png)

2. Välj **Välj fil** för att välja de filer eller mappar som du vill återställa.

    ![Välj Välj fil](./media/restore-afs/select-file.png)

1. När du har valt **Välj fil**visas innehållet i den fil resurs återställnings punkt som du har valt för återställning i ett fil resurs fönster.

1. Markera kryss rutan som motsvarar den fil eller mapp som du vill återställa och välj **Välj**.

    ![Välj fil eller mapp](./media/restore-afs/select-file-folder.png)

1. Upprepa steg 2 till 4 för att välja flera filer eller mappar som ska återställas.
1. När du har valt alla objekt som du vill återställa väljer du **OK**.

    ![När du har valt alla objekt som ska återställas väljer du OK](./media/restore-afs/after-selecting-items.png)

1. Starta återställnings åtgärden genom att välja **Återställ** .

    ![Välj Återställ för att starta](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

1. Välj en **annan plats** som **återställnings mål**.
1. Välj det mål lagrings konto där du vill återställa det säkerhetskopierade innehållet i list rutan **lagrings konto** .
1. List rutan **Välj fil resurs** visar de fil resurser som finns i det lagrings konto som du valde i steg 2. Välj den fil resurs där du vill återställa det säkerhetskopierade innehållet.
1. I rutan **mappnamn** anger du namnet på mappen som du vill skapa i mål fil resursen med det återställda innehållet.
1. Välj om du vill hoppa över eller skriva över om det finns konflikter.
1. Välj **Välj fil** för att välja de filer eller mappar som du vill återställa.

    ![Välj objekt som ska återställas till en annan plats](./media/restore-afs/restore-to-alternate-location.png)

1. När du väljer **Välj fil**visas innehållet i den fil resurs återställnings punkt som du har valt för återställning i ett fil resurs fönster.
1. Markera kryss rutan som motsvarar den fil eller mapp som du vill återställa och välj **Välj**.

    ![Välj återställnings mål](./media/restore-afs/recovery-destination.png)

1. Upprepa steg 6 till 8 för att välja flera filer eller mappar som ska återställas.
1. När du har valt alla objekt som du vill återställa väljer du **OK**.

    ![Välj OK när du har valt alla filer](./media/restore-afs/after-selecting-all-items.png)

1. Starta återställnings åtgärden genom att välja **Återställ** .

## <a name="track-a-restore-operation"></a>Spåra en återställnings åtgärd

När du har utlöst återställnings åtgärden skapar säkerhets kopierings tjänsten ett jobb för spårning. Azure Backup visar meddelanden om jobbet i portalen. Om du vill visa åtgärder för jobbet väljer du hyperlänken meddelanden.

![Välj hyperlänk för meddelanden](./media/restore-afs/notifications-link.png)

Du kan också övervaka återställnings förloppet från Recovery Services-valvet:

1. Öppna det Recovery Services valv från vilket du utlöste återställnings åtgärden.
1. I fönstret Översikt väljer du **säkerhets kopierings jobb** under avsnittet **övervakning** för att se statusen för åtgärder som körs mot olika arbets belastningar.

    ![Välj säkerhets kopierings jobb](./media/restore-afs/backup-jobs.png)

1. Välj det arbets belastnings namn som motsvarar din fil resurs om du vill visa mer information om återställnings åtgärden, t. ex. **överförda data** och **antalet återställda filer**.

    ![Se återställd information](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [hanterar säkerhets kopior av Azure-filresurser](manage-afs-backup.md).
