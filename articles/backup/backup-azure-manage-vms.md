---
title: Hantera säkerhetskopiering för Resource Manager distribuerade virtuella datorer
description: Lär dig att hantera och övervaka säkerhetskopiering för Resource Manager distribuerade virtuella datorer
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 11/28/2016
ms.author: trinadhk
ms.openlocfilehash: 4d45db6ba6354f85c3ed67561751720b6f6f4b77
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606349"
---
# <a name="manage-azure-virtual-machine-backups"></a>Hantera säkerhetskopiering av virtuella Azure-datorer

Den här artikeln innehåller information om hur du hanterar Virtuella säkerhetskopieringar och förklarar aviseringar om säkerhetskopiering informationen i portalens instrumentpanel. Riktlinjerna i den här artikeln gäller för virtuella datorer med Recovery Services-valv. Den här artikeln täcker inte skapandet av virtuella datorer och inte heller har förklarar hur du skyddar virtuella datorer. En introduktion om hur du skyddar Azure Resource Manager distribuerade virtuella datorer i Azure med ett Recovery Services-valv finns [förhandstitt: Säkerhetskopiera virtuella datorer till ett Recovery Services-valv](backup-azure-vms-first-look-arm.md).

## <a name="manage-vaults-and-protected-virtual-machines"></a>Hantera valv och skyddade virtuella datorer
I Azure-portalen ger Recovery Services-valvet instrumentpanelen tillgång till information om valvet, inklusive:

* den senaste ögonblicksbild, som också är den senaste återställningspunkten
* principen för säkerhetskopiering
* Total storlek för alla ögonblicksbilder av säkerhetskopior
* antalet virtuella datorer som skyddas med valvet

Många hanteringsuppgifter med en säkerhetskopiering av virtuella datorer som börjar med öppna valvet i instrumentpanelen. Men eftersom valv kan användas för att skydda flera objekt (eller flera virtuella datorer), om du vill visa information om en viss virtuell dator, öppna instrumentpanelen valvet objektet. Följande procedur visar hur du öppnar den *valvet instrumentpanelen* och fortsätt sedan till den *valvet objektet instrumentpanelen*. Det finns ”tips” i båda procedurerna som pekar reda på hur du lägger till valvet och valvet objektet i Azure instrumentpanel med hjälp av PIN-kod för instrumentpanelen kommando. Fäst på instrumentpanelen är ett sätt att skapa en genväg till valvet eller objekt. Du kan också köra vanliga kommandon från genvägen.

> [!TIP]
> Om du har flera instrumentpaneler och öppnar blad, Använd mörkt blå skjutreglaget längst ned i fönstret till bild Azure instrumentpanelen fram och tillbaka.
>
>

![Fullständig vy med skjutreglaget](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Öppna ett Recovery Services-valv i instrumentpanelen:
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På navmenyn klickar du på **Bläddra** och i listan över resurser skriver du **Recovery Services**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

    Listan över Recovery Services-valv visas.

    ![Lista över Recovery Services-valv ](./media/backup-azure-manage-vms/list-o-vaults.png)

   > [!TIP]
   > Om du fäster ett valv på instrumentpanelen i Azure är som valvet omedelbart tillgängligt när du öppnar den Azure-portalen. Högerklicka på valvet för att fästa ett valv på instrumentpanelen i listan med valvet, och välj **fäst på instrumentpanelen**.
   >
   >
3. Välj i listan över valv valvet för att öppna dess instrumentpanelen. När du väljer valvet, valvet instrumentpanelen och **inställningar** bladet som är öppna. I följande bild, den **Contoso valvet** instrumentpanelen är markerat.

    ![Öppna instrumentpanelen i valvet och inställningsbladet](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>Öppna en valvet objektet instrumentpanel
Du har öppnat valvet instrumentpanelen i föregående procedur. Öppna instrumentpanelen valvet objektet:

1. I instrumentpanelen för valvet på den **säkerhetskopiering objekt** panelen, klickar du på **Azure Virtual Machines**.

    ![Öppna Säkerhetskopiering poster sida vid sida](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    Den **Säkerhetskopieringsobjekt** bladet visar en lista över senaste säkerhetskopieringsjobbet för varje objekt. I det här exemplet har en virtuell dator, demovm markgal skyddas av det här valvet.  

    ![Säkerhetskopiera objekt sida vid sida](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > Du kan fästa ett valv objekt på instrumentpanelen i Azure för enkel åtkomst. Om du vill fästa ett valv objekt i listan över objekt valvet, högerklicka på objektet och välj **fäst på instrumentpanelen**.
   >
   >
2. I den **säkerhetskopiering objekt** bladet, klickar du på objektet för att öppna instrumentpanelen valvet objektet.

    ![Säkerhetskopiera objekt sida vid sida](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    Valvet objektet instrumentpanel och dess **inställningar** bladet som är öppna.

    ![Säkerhetskopiera objekt instrumentpanel med inställningsbladet](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    Från instrumentpanelen valvet objekt, kan du utföra många viktiga hanteringsaktiviteter som:

   * Ändra principer eller skapa en ny princip för säkerhetskopiering
   * Visa återställningspunkter och se deras konsekvent tillstånd
   * Säkerhetskopiering på begäran för en virtuell dator
   * Sluta skydda virtuella datorer
   * Återuppta skyddet av en virtuell dator
   * ta bort säkerhetskopierade data (eller återställningspunkt)
   * [återställa säkerhetskopior](backup-azure-arm-restore-vms.md#restore-backed-up-disks)

Följande procedurer är startpunkten valvet objektet instrumentpanelen.

## <a name="manage-backup-policies"></a>Hantera säkerhetskopieringsprinciper
1. På den [valvet objektet instrumentpanelen](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klickar du på **alla inställningar** att öppna den **inställningar** bladet.

    ![Princip för säkerhetskopiering bladet](./media/backup-azure-manage-vms/all-settings-button.png)
2. På den **inställningar** bladet, klickar du på **säkerhetskopiera princip** att öppna det bladet.

    På bladet visas säkerhetskopiering frekvens och kvarhållning intervallet information.

    ![Princip för säkerhetskopiering bladet](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. Från den **Välj säkerhetskopieringsprincip** menyn:

   * Om du vill ändra principer, Välj en annan princip och klicka på **spara**. Den nya principen tillämpas omedelbart på valvet.
   * Om du vill skapa en princip, Välj **Skapa nytt**.

     ![Säkerhetskopiering av virtuell dator](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     Anvisningar om hur du skapar en princip för säkerhetskopiering finns [definierar en princip för säkerhetskopiering](backup-azure-manage-vms.md#defining-a-backup-policy).

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> När du hanterar principer för säkerhetskopiering, se till att följa den [metodtips](backup-azure-vms-introduction.md#best-practices) för optimal prestanda vid säkerhetskopiering
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>Säkerhetskopiering på begäran för en virtuell dator
Du kan utföra en på-begäran säkerhetskopiering av en virtuell dator när den har konfigurerats för skydd. Om den första säkerhetskopian väntar skapas säkerhetskopiering på begäran en fullständig kopia av den virtuella datorn i Recovery Services-valvet. Om den första säkerhetskopieringen har slutförts, skickas en säkerhetskopiering på begäran endast ändringar från tidigare ögonblicksbild till Recovery Services-valvet. Efterföljande säkerhetskopieringar är som är alltid inkrementellt.

> [!NOTE]
> Kvarhållningsintervall för en säkerhetskopiering på begäran är kvarhållning det angivna värdet för daglig säkerhetskopieringspunkt i principen. Om ingen daglig säkerhetskopieringspunkt är markerad används veckovis säkerhetskopieringspunkt.
>
>

Starta en säkerhetskopiering på begäran för en virtuell dator:

* På den [valvet objektet instrumentpanelen](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klickar du på **Säkerhetskopiera nu**.

    ![Säkerhetskopiering nu knappen](./media/backup-azure-manage-vms/backup-now-button.png)

    Portalen ser till att du vill starta en säkerhetskopiering på begäran. Klicka på **Ja** att starta jobbet.

    ![Säkerhetskopiering nu knappen](./media/backup-azure-manage-vms/backup-now-check.png)

    Säkerhetskopieringsjobbet skapar en återställningspunkt. Kvarhållningsintervall för återställningspunkten är detsamma som Kvarhållningsintervall som anges i principen som är associerad med den virtuella datorn. Om du vill spåra förloppet för jobbet på valvet instrumentpanelen klickar du på den **säkerhetskopieringsjobb** panelen.  

## <a name="stop-protecting-virtual-machines"></a>Sluta skydda virtuella datorer
Om du vill sluta skydda en virtuell dator, tillfrågas du om du vill behålla återställningspunkterna. Det finns två sätt att sluta skydda virtuella datorer:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter, eller
* Stoppa alla framtida säkerhetskopieringsjobb men lämna kvar återställningspunkter

Det finns en kostnad som är associerade med lämnar återställningspunkter i lagringen. Fördelen med att lämna återställningspunkterna är dock kan du återställa den virtuella datorn senare, om så önskas. Information om kostnaden för att lämna återställningspunkterna finns på [prisinformationen](https://azure.microsoft.com/pricing/details/backup/). Om du vill ta bort alla återställningspunkter kan återställa du inte den virtuella datorn.

Ta bort skyddet för en virtuell dator:

1. På den [valvet objektet instrumentpanelen](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klickar du på **stoppa säkerhetskopiering**.

    ![Stoppa säkerhetskopiering](./media/backup-azure-manage-vms/stop-backup-button.png)

    Stoppa säkerhetskopiering blad öppnas.

    ![Stoppa säkerhetskopiering bladet](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. På den **stoppa säkerhetskopiering** bladet Välj om du vill behålla eller ta bort säkerhetskopierade data. Informationsrutan innehåller information om ditt val.

    ![Stoppa skydd](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. Om du valde att behålla säkerhetskopierade data vidare till steg 4. Om du har valt att ta bort säkerhetskopieringsdata, bekräfta att du vill stoppa alla säkerhetskopieringsjobb och ta bort återställningspunkter - skriver du namnet på objektet.

    ![Stoppa verifiering](./media/backup-azure-manage-vms/item-verification-box.png)

    Om du är osäker objektnamnet hovra över utropstecken att visa namnet. Namnet på objektet är också **stoppa säkerhetskopiering** längst upp på bladet.
4. Du kan också ange en **orsak** eller **kommentar**.
5. Säkerhetskopieringsjobbet för det aktuella objektet klickar du på ![säkerhetskopiering stoppknappen](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Ett meddelande kan du vet att säkerhetskopiera jobb har stoppats.

    ![Bekräfta stoppskydd](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>Återuppta skyddet av en virtuell dator
Om den **behålla säkerhetskopierade Data** alternativet valdes när skyddet för den virtuella datorn har stoppats, så är det möjligt att skyddet ska återupptas. Om den **ta bort säkerhetskopierade Data** alternativet har valts och sedan skyddet för den virtuella datorn kan inte återuppta.

Att återuppta skydd för den virtuella datorn

1. På den [valvet objektet instrumentpanelen](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klickar du på **återuppta säkerhetskopiering**.

    ![Återuppta skydd](./media/backup-azure-manage-vms/resume-backup-button.png)

    Princip för säkerhetskopiering blad öppnas.

   > [!NOTE]
   > Du kan välja en annan princip än principen som virtuella datorn ursprungligen skyddades när skydda den virtuella datorn igen.
   >
   >
2. Följ stegen i [hantera principer för säkerhetskopiering](backup-azure-manage-vms.md#manage-backup-policies) tilldela principen för den virtuella datorn.

    När säkerhetskopieringsprincipen som används för den virtuella datorn, visas följande meddelande.

    ![Har skyddat VM](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata
Du kan ta bort den säkerhetskopiera informationen som är kopplad till en virtuell dator under den **stoppa säkerhetskopiering** jobb, eller när som helst efter säkerhetskopieringen jobbet har slutförts. Det kan vara en fördel att vänta dagar eller veckor innan du tar bort återställningspunkterna. Till skillnad från återställning av återställningspunkter när du tar bort säkerhetskopierade data, kan du inte välja specifika återställningspunkter som ska tas bort. Om du väljer att ta bort säkerhetskopierade data, tar du bort alla återställningspunkter som är associerade med objektet.

Följande procedur förutsätter att jobbet för den virtuella datorn har stoppats eller inaktiverats. När jobbet som är inaktiverat på **återuppta säkerhetskopiering** och **ta bort säkerhetskopiering** alternativ är tillgängliga i valvet objektet instrumentpanelen.

![Återuppta och ta bort knappar](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Ta bort säkerhetskopieringsdata på en virtuell dator med den *säkerhetskopiera inaktiverats*:

1. På den [valvet objektet instrumentpanelen](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klickar du på **ta bort säkerhetskopiering**.

    ![VM-typ](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    Den **ta bort säkerhetskopierade Data** blad öppnas.

    ![VM-typ](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. Skriv namnet på objektet du vill bekräfta att du vill ta bort återställningspunkter.

    ![Stoppa verifiering](./media/backup-azure-manage-vms/item-verification-box.png)

    Om du är osäker objektnamnet hovra över utropstecken att visa namnet. Namnet på objektet är också **ta bort säkerhetskopierade Data** längst upp på bladet.
3. Du kan också ange en **orsak** eller **kommentar**.
4. Ta bort säkerhetskopierade data för det aktuella objektet, klicka på ![säkerhetskopiering stoppknappen](./media/backup-azure-manage-vms/delete-button.png)

    Ett meddelande får du reda på den säkerhetskopiera informationen har tagits bort.

## <a name="next-steps"></a>Nästa steg
Information om hur du skapar en virtuell dator från en återställningspunkt igen kolla [återställa virtuella Azure-datorer](backup-azure-arm-restore-vms.md). Om du behöver information om hur du skyddar virtuella datorer, se [förhandstitt: Säkerhetskopiera virtuella datorer till ett Recovery Services-valv](backup-azure-vms-first-look-arm.md). Information om övervakning av händelser finns [övervakar aviseringar för virtuell dator i Azure-säkerhetskopieringar](backup-azure-monitor-vms.md).
