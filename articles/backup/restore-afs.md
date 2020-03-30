---
title: Återställa Azure-filresurser
description: Lär dig hur du använder Azure-portalen för att återställa en hel filresurs eller specifika filer från en återställningspunkt som skapats av Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586959"
---
# <a name="restore-azure-file-shares"></a>Återställa Azure-filresurser

I den här artikeln beskrivs hur du använder Azure-portalen för att återställa en hel filresurs eller specifika filer från en återställningspunkt som skapats av [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

I den här artikeln får du lära dig hur du:

* Återställ en fullständig Azure-filresurs.
* Återställ enskilda filer eller mappar.
* Spåra återställningsstatusen.

## <a name="steps-to-perform-a-restore-operation"></a>Steg för att utföra en återställningsåtgärd

Gör så här om du vill utföra en återställningsåtgärd.

### <a name="select-the-file-share-to-restore"></a>Markera den filresurs som ska återställas

1. Öppna valvet för Återställningstjänster som du använde för att konfigurera säkerhetskopia för filresursen i [Azure-portalen.](https://portal.azure.com/)

1. Välj **Säkerhetskopierade objekt** under avsnittet **Skyddade objekt** i översiktsfönstret.

    ![Välj säkerhetskopieringsobjekt](./media/restore-afs/backup-items.png)

1. När du har valt **Säkerhetskopieringsobjekt**öppnas en ny ruta med alla typer av säkerhetskopieringshantering bredvid översiktsfönstret.

    ![Typer av säkerhetskopieringshantering](./media/restore-afs/backup-management.png)

1. Välj **Backup Items** **Azure Storage (Azure Files)** under **Hantering av säkerhetskopieringsobjekt**under hantering av säkerhetskopiering . Du ser en lista över alla filresurser och motsvarande lagringskonton som backas upp med hjälp av det här valvet.

    ![Lista över alla filresurser](./media/restore-afs/file-shares.png)

1. Välj den filresurs som du vill utföra återställningsåtgärden för i listan över Azure-filresurser.

### <a name="full-share-recovery"></a>Full aktieåterhämtning

Du kan använda det här återställningsalternativet för att återställa hela filresursen på den ursprungliga platsen eller en annan plats.

1. Välj alternativet **Återställ resurs** i fönstret **Säkerhetskopieringsobjekt** som visas när du har markerat filresursen för att återställa i steg 5 i avsnittet [Välj filresursen för att återställa.](#select-the-file-share-to-restore)

   ![Välj Återställ resurs](./media/restore-afs/restore-share.png)

1. När du har valt **Återställ resurs**öppnas fönstret **Återställ** med en **återställningspunkt-meny** som visar en lista över återställningspunkter som är tillgängliga för den markerade filresursen.

1. Markera den återställningspunkt som du vill använda för att utföra återställningen och välj **OK**.

    ![Välj återställningspunkt](./media/restore-afs/restore-point.png)

1. När du har valt **OK**växlar menyn i fönstret **Återställ** till **Återställ plats**. Ange var eller hur data ska återställas i **Återställningsplats.** Välj ett av följande två alternativ:

    * **Ursprunglig plats**: Återställ hela filresursen till samma plats som den ursprungliga källan.
    * **Alternativ plats:** Återställ hela filresursen till en alternativ plats och behåll den ursprungliga filresursen som den är.

#### <a name="restore-to-the-original-location"></a>Återställa till den ursprungliga platsen

1. Välj **Ursprunglig plats** som **återställningsmål**och välj om du vill hoppa över eller skriva över om det finns konflikter. När du har gjort rätt val väljer du **OK**.

    ![Välj ursprunglig plats](./media/restore-afs/original-location.png)

1. Välj **Återställ** om du vill starta återställningen.

    ![Välj Återställ för att starta](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

1. Välj **Alternativ plats** som **återställningsmål**.
1. Välj det mållagringskonto där du vill återställa det säkerhetskopierade innehållet i listrutan **Lagringskonto.**
1. I listrutan **Välj fildelning** visas de filresurser som finns i det lagringskonto som du valde i steg 2. Markera den filresurs där du vill återställa det säkerhetskopierade innehållet.
1. I rutan **Mappnamn** anger du ett mappnamn som du vill skapa i målfilresursen med det återställda innehållet.
1. Välj om du vill hoppa över eller skriva över om det finns konflikter.
1. När du har angett lämpliga värden i alla rutor väljer du **OK**.

    ![Välj alternativ plats](./media/restore-afs/alternate-location.png)

1. Välj **Återställ** om du vill starta återställningen.

    ![Välj Återställ för att starta](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Återställning på objektnivå

Du kan använda det här återställningsalternativet för att återställa enskilda filer eller mappar på den ursprungliga platsen eller en annan plats.

1. Välj alternativet **Filåterställning** i fönstret **Säkerhetskopieringsobjekt** som visas när du har markerat filresursen för att återställa i steg 5 i avsnittet [Välj filresursen för att återställa.](#select-the-file-share-to-restore)

    ![Välj återställning av filer](./media/restore-afs/file-recovery.png)

1. När du har valt **Filåterställning**öppnas fönstret **Återställ** med en **återställningspunktsmeny** som visar en lista över återställningspunkter som är tillgängliga för den markerade filresursen.

1. Markera den återställningspunkt som du vill använda för att utföra återställningen och välj **OK**.

    ![Välj återställningspunkt](./media/restore-afs/restore-point.png)

1. När du har valt **OK**växlar menyn för återställningsfönstret till **Återställ plats**. Ange var eller hur data ska återställas i **Återställningsplats.** Välj ett av följande två alternativ:

    * **Ursprunglig plats**: Återställ markerade filer eller mappar till samma filresurs som den ursprungliga källan.
    * **Alternativ plats:** Återställ markerade filer eller mappar till en annan plats och behåll innehållet i den ursprungliga filresursen som det är.

#### <a name="restore-to-the-original-location"></a>Återställa till den ursprungliga platsen

1. Välj **Ursprunglig plats** som **återställningsmål**och välj om du vill hoppa över eller skriva över om det finns konflikter.

    ![Ursprunglig plats för återställning på objektnivå](./media/restore-afs/original-location-item-level.png)

1. Välj **Välj fil** för att markera de filer eller mappar som du vill återställa.

    ![Välj Välj fil](./media/restore-afs/select-file.png)

1. När du har valt **Välj fil**visas innehållet i återställningspunkten för filresurs som du har valt för återställning i ett fildelningsfönster.

1. Markera kryssrutan som motsvarar den fil eller mapp som du vill återställa och välj **Markera**.

    ![Markera fil eller mapp](./media/restore-afs/select-file-folder.png)

1. Upprepa steg 2 till och med 4 för att välja flera filer eller mappar för återställning.
1. När du har markerat alla objekt som du vill återställa väljer du **OK**.

    ![När du har markerat alla objekt som ska återställas väljer du OK](./media/restore-afs/after-selecting-items.png)

1. Välj **Återställ** om du vill starta återställningen.

    ![Välj Återställ för att starta](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

1. Välj **Alternativ plats** som **återställningsmål**.
1. Välj det mållagringskonto där du vill återställa det säkerhetskopierade innehållet i listrutan **Lagringskonto.**
1. I listrutan **Välj fildelning** visas de filresurser som finns i det lagringskonto som du valde i steg 2. Markera den filresurs där du vill återställa det säkerhetskopierade innehållet.
1. I rutan **Mappnamn** anger du ett mappnamn som du vill skapa i målfilresursen med det återställda innehållet.
1. Välj om du vill hoppa över eller skriva över om det finns konflikter.
1. Välj **Välj fil** för att markera de filer eller mappar som du vill återställa.

    ![Markera objekt som ska återställas till annan plats](./media/restore-afs/restore-to-alternate-location.png)

1. När du väljer **Välj fil**visas innehållet i återställningspunkten för filresurs som du har valt för återställning i ett fildelningsfönster.
1. Markera kryssrutan som motsvarar den fil eller mapp som du vill återställa och välj **Markera**.

    ![Välj återställningsmål](./media/restore-afs/recovery-destination.png)

1. Upprepa steg 6 till och med 8 för att välja flera filer eller mappar för återställning.
1. När du har markerat alla objekt som du vill återställa väljer du **OK**.

    ![Välj OK när du har markerat alla filer](./media/restore-afs/after-selecting-all-items.png)

1. Välj **Återställ** om du vill starta återställningen.

## <a name="track-a-restore-operation"></a>Spåra en återställningsåtgärd

När du har utlöst återställningen skapar säkerhetskopieringstjänsten ett jobb för spårning. Azure Backup visar meddelanden om jobbet i portalen. Om du vill visa åtgärder för jobbet väljer du hyperlänken för meddelanden.

![Välj hyperlänk för meddelanden](./media/restore-afs/notifications-link.png)

Du kan också övervaka återställningsstatus från recovery services-valvet:

1. Öppna valvet för återställningstjänster där du utlöste återställningsåtgärden.
1. I översiktsfönstret väljer du **Säkerhetskopieringsjobb** under avsnittet **Övervakning** för att se status för åtgärder som körs mot olika arbetsbelastningar.

    ![Välj säkerhetskopieringsjobb](./media/restore-afs/backup-jobs.png)

1. Välj det arbetsbelastningsnamn som motsvarar filresursen om du vill visa mer information om återställningen, till exempel **Överförda data** och **antal återställda filer**.

    ![Se återställd information](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [hanterar Azure-filresurssäkerhetskopior](manage-afs-backup.md).
