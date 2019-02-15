---
title: Hantera säkerhetskopiering för Resource Manager-distribuerade virtuella datorer
description: Lär dig att hantera och övervaka säkerhetskopiering för Resource Manager-distribuerade virtuella datorer
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 11/28/2016
ms.author: sogup
ms.openlocfilehash: 118e32994ed6471c52726e826ecfd42620bd3a91
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269594"
---
# <a name="manage-azure-virtual-machine-backups"></a>Hantera säkerhetskopiering av virtuella Azure-datorer

Den här artikeln innehåller råd om hur du hanterar VM-säkerhetskopieringar och förklarar med aviseringar om säkerhetskopiering informationen på portalens instrumentpanel. Riktlinjerna i den här artikeln gäller för virtuella datorer med Recovery Services-valv. Den här artikeln beskriver inte skapandet av virtuella datorer och inte heller förklarar den hur du skyddar virtuella datorer. En genomgång om hur du skyddar Azure Resource Manager-distribuerade virtuella datorer i Azure med ett Recovery Services-valv finns [första titt: Säkerhetskopiera virtuella datorer till Recovery Services-valvet](backup-azure-vms-first-look-arm.md).

## <a name="manage-vaults-and-protected-virtual-machines"></a>Hantera valv och skyddade virtuella datorer
Instrumentpanelen för Recovery Services-valvet ger tillgång till information om valvet, inklusive Azure-portalen:

* den senaste ögonblicksbilden av säkerhetskopian, som också är den senaste återställningspunkten
* principen för säkerhetskopiering
* Total storlek för alla ögonblicksbilder av säkerhetskopior
* antalet virtuella datorer som skyddas med valvet

Många administrativa uppgifter med en säkerhetskopiering av virtuella datorer som börjar med att öppna valvet i instrumentpanelen. Men eftersom valv kan användas för att skydda flera objekt (eller flera virtuella datorer), om du vill visa information om en viss virtuell dator, öppna instrumentpanelen för valvet objekt. Följande procedur visar hur du öppnar den *instrumentpanelen för valvet* och fortsätt sedan till den *instrumentpanelen för valvet objektet*. Det finns ”tips” i båda procedurerna som påpekar att lägga till valvet och valv objekt till Azure-instrumentpanelen med hjälp av fäst på instrumentpanelen för kommandot. Fäst på instrumentpanelen är ett sätt att skapa en genväg till valvet eller objekt. Du kan också köra vanliga kommandon från genvägen.

> [!TIP]
> Om du har flera instrumentpaneler och blad öppna, använda mörkblå skjutreglaget längst ned i fönstret för att visa instrumentpanelen för Azure fram och tillbaka.
>
>

![Fullständig vy med skjutreglaget](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Öppna Recovery Services-valvet i instrumentpanelen:
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På navmenyn klickar du på **Bläddra** och i listan över resurser skriver du **Recovery Services**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Recovery Services-valv**.

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

    Listan över Recovery Services-valv visas.

    ![Listan över Recovery Services-valv ](./media/backup-azure-manage-vms/list-o-vaults.png)

   > [!TIP]
   > Om du fäster ett valv på Azure-instrumentpanelen, är den vault omedelbart tillgängliga när du öppnar Azure-portalen. Om du vill fästa ett valv på instrumentpanelen i listan över valv, högerklickar du på valvet och väljer **fäst på instrumentpanelen**.
   >
   >
3. I listan över valv Välj valvet för att öppna valvets instrumentpanel. När du väljer valvet instrumentpanelen för valvet och **inställningar** öppnas. I följande bild, den **Contoso-valvet** instrumentpanelen är markerad.

    ![Öppna instrumentpanelen för valvet och bladet inställningar](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>Öppna en instrumentpanel för valvet objekt
I föregående procedur öppnas instrumentpanelen för valvet. Öppna instrumentpanelen för valvet objekt:

1. I instrumentpanelen för valvet på den **Säkerhetskopieringsobjekt** klickar **Azure Virtual Machines**.

    ![Öppna säkerhetskopieringsobjekt panel](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    Den **Säkerhetskopieringsobjekt** bladet visar en lista över senaste säkerhetskopieringsjobbet för varje objekt. I det här exemplet har en virtuell dator, demovm markgal skyddas av det här valvet.  

    ![Objekt att säkerhetskopiera panel](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > Du kan fästa ett valv-objekt på Azure-instrumentpanelen för enkel åtkomst. Om du vill fästa ett valv-objekt i listan över objekt valv, högerklicka på objektet och välj **fäst på instrumentpanelen**.
   >
   >
2. I den **Säkerhetskopieringsobjekt** bladet klickar du på objektet för att öppna instrumentpanelen för valvet objekt.

    ![Objekt att säkerhetskopiera panel](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    Instrumentpanelen för valvet objektet och dess **inställningar** öppnas.

    ![Objekt att säkerhetskopiera instrumentpanel med bladet inställningar](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    Från instrumentpanelen för valvet objekt kan du utföra många viktiga uppgifter, till exempel:

   * Ändra principer eller skapa en ny säkerhetskopieringsprincip
   * Visa återställningspunkter och se deras konsekvens-tillstånd
   * Säkerhetskopiering på begäran för en virtuell dator
   * Sluta skydda virtuella datorer
   * Återuppta skyddet av en virtuell dator
   * ta bort säkerhetskopierade data (eller återställningspunkt)
   * [återställa säkerhetskopior](backup-azure-arm-restore-vms.md#create-new-restore-disks)

Med följande metoder för är startpunkten instrumentpanelen för valvet objekt.

## <a name="manage-backup-policies"></a>Hantera säkerhetskopieringsprinciper
1. På den [instrumentpanelen för valvet objekt](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klickar du på **alla inställningar** att öppna den **inställningar** bladet.

    ![Bladet säkerhetskopieringspolicy](./media/backup-azure-manage-vms/all-settings-button.png)
2. På den **inställningar** bladet klickar du på **säkerhetskopieringspolicy** att öppna bladet.

    På bladet visas säkerhetskopiering frekvens och kvarhållning intervallet information.

    ![Bladet säkerhetskopieringspolicy](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. Från den **Välj säkerhetskopieringspolicy** menyn:

   * Om du vill ändra principer, Välj en annan princip och klicka på **spara**. Den nya principen tillämpas omedelbart på valvet.
   * Skapa en princip för att välja **Skapa ny**.

     ![Säkerhetskopiering av virtuell dator](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     Anvisningar om hur du skapar en princip för säkerhetskopiering finns i [definierar en säkerhetskopieringspolicy](backup-azure-manage-vms.md#defining-a-backup-policy).

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> Medan du hanterar principer för säkerhetskopiering, se till att följa den [bästa praxis](backup-azure-vms-introduction.md#best-practices) för optimala prestanda vid säkerhetskopiering
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>Säkerhetskopiering på begäran för en virtuell dator
Du kan säkerhetskopiera en på begäran för en virtuell dator när den är konfigurerad för skydd. Om den första säkerhetskopieringen väntar skapar på begäran-säkerhetskopiering en fullständig kopia av den virtuella datorn i Recovery Services-valvet. Om den första säkerhetskopieringen har slutförts, skickas en säkerhetskopiering på begäran endast ändringar från tidigare ögonblicksbild till Recovery Services-valvet. Det vill säga är efterföljande säkerhetskopieringar alltid inkrementell.

> [!NOTE]
> Kvarhållningsintervallet för en säkerhetskopiering på begäran är kvarhållningsvärdet som angetts för daglig säkerhetskopieringspunkt i principen. Om ingen daglig säkerhetskopieringspunkt väljs, används veckovis säkerhetskopieringspunkt.
>
>

Att utlösa en säkerhetskopiering på begäran för en virtuell dator:

* På den [instrumentpanelen för valvet objekt](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klickar du på **Säkerhetskopiera nu**.

    ![Säkerhetskopiering nu knappen](./media/backup-azure-manage-vms/backup-now-button.png)

    Portalen ser till att du vill starta en säkerhetskopiering på begäran. Klicka på **Ja** att starta säkerhetskopieringen.

    ![Säkerhetskopiering nu knappen](./media/backup-azure-manage-vms/backup-now-check.png)

    Säkerhetskopieringsjobbet skapar en återställningspunkt. Kvarhållningsintervallet för återställningspunkten är samma som kvarhållningsintervallet som angetts i principen som är associerade med den virtuella datorn. Om du vill spåra förloppet för jobbet i instrumentpanelen för valvet klickar du på den **säkerhetskopieringsjobb** panelen.  

## <a name="stop-protecting-virtual-machines"></a>Sluta skydda virtuella datorer
Om du vill sluta skydda en virtuell dator, tillfrågas du om du vill behålla återställningspunkterna. Det finns två sätt att sluta skydda virtuella datorer:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter, eller
* Stoppa alla framtida säkerhetskopieringsjobb, men lämna kvar återställningspunkterna

Det finns en kostnad som är associerade med att lämna kvar återställningspunkterna i lagring. Fördelen med att lämna kvar återställningspunkterna är dock kan du återställa den virtuella datorn senare, om så önskas. Information om kostnaden för att lämna kvar återställningspunkterna finns i den [prisinformation](https://azure.microsoft.com/pricing/details/backup/). Om du väljer att ta bort alla återställningspunkter kan återställa du inte den virtuella datorn.

Återställningspunkten kommer att bevaras alltid tills Säkerhetskopieringsobjekt är återaktivera skyddet med en bevarandeprincip eller StopProtection med ta borttagningsdata. Vid återaktiveringen av skyddet avgör den nya principen som är komma associerad kvarhållning av återställningspunkter. På samma sätt du om du tar bort en datakälla utan att stoppa säkerhetskopieringen börjar nya säkerhetskopior att misslyckas och gamla återställningspunkter upphör att gälla enligt bevarandeprincipen, men den senaste återställningspunkten behålls alltid tills du stoppar säkerhetskopieringen och tar bort data.

Sluta skydda en virtuell dator:

1. På den [instrumentpanelen för valvet objekt](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klickar du på **stoppa säkerhetskopiering**.

    ![Stoppa säkerhetskopiering](./media/backup-azure-manage-vms/stop-backup-button.png)

    Bladet stoppa säkerhetskopiering öppnas.

    ![Stoppa säkerhetskopiering bladet](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. På den **stoppa säkerhetskopiering** bladet Välj om du vill behålla eller ta bort säkerhetskopierade data. Informationsrutan innehåller information om ditt val.

    ![Sluta skydda](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. Om du valde att behålla säkerhetskopierade data kan du gå vidare till steg 4. Om du har valt att ta bort säkerhetskopierade data, bekräfta att du vill stoppa säkerhetskopieringsjobb och ta bort återställningspunkterna - skriver du namnet på objektet.

    ![Stoppa verifiering](./media/backup-azure-manage-vms/item-verification-box.png)

    Om du inte vet namnet på objektet, hovrar du över utropstecken ska visa namnet. Namnet på objektet är också **stoppa säkerhetskopiering** överst på bladet.
4. Du kan också ange en **orsak** eller **kommentar**.
5. Om du vill stoppa säkerhetskopieringsjobbet för det aktuella objektet, klickar du på ![säkerhetskopiering stoppknappen](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Ett meddelande får du reda på säkerhetskopieringsjobb har stoppats.

    ![Bekräfta att sluta skydda](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>Återuppta skyddet av en virtuell dator
Om den **Behåll säkerhetskopieringsdata** valdes när skyddet för den virtuella datorn har stoppats, så är det möjligt att återuppta skyddet. Om den **ta bort säkerhetskopieringsdata** alternativet har valts och sedan skyddet för den virtuella datorn kan inte återuppta.

Att återuppta skyddet av den virtuella datorn

1. På den [instrumentpanelen för valvet objekt](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klickar du på **återuppta säkerhetskopiering**.

    ![Återuppta skyddet](./media/backup-azure-manage-vms/resume-backup-button.png)

    Bladet säkerhetskopieringspolicy öppnas.

   > [!NOTE]
   > När du skyddar den virtuella datorn igen, kan du välja en annan princip än den princip som virtuella datorn ursprungligen skyddades.
   >
   >
2. Följ stegen i [hantera säkerhetskopieringsprinciper](backup-azure-manage-vms.md#manage-backup-policies) att tilldela principen för den virtuella datorn.

    När principen för säkerhetskopiering används på den virtuella datorn, visas följande meddelande.

    ![Har skyddad virtuell dator](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata
Du kan ta bort säkerhetskopierade data som är associerade med en virtuell dator under den **stoppa säkerhetskopiering** jobb, eller när som helst efter säkerhetskopieringen jobbet har slutförts. Det kan vara en fördel att vänta dagar eller veckor innan du tar bort återställningspunkterna. Till skillnad från återställning av återställningspunkter när du tar bort säkerhetskopierade data, kan du inte välja specifika återställningspunkter som ska tas bort. Om du väljer att ta bort säkerhetskopierade data, tar du bort alla återställningspunkter som är associerade med objektet.

Följande procedur förutsätter att säkerhetskopieringen av den virtuella datorn har stoppats eller inaktiverats. När säkerhetskopieringen är inaktiverat i **återuppta säkerhetskopiering** och **ta bort backup** alternativ är tillgängliga i instrumentpanelen för valvet objekt.

![Återuppta och ta bort knappar](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Att ta bort säkerhetskopierade data på en virtuell dator med den *säkerhetskopiering inaktiverad*:

1. På den [instrumentpanelen för valvet objekt](backup-azure-manage-vms.md#open-a-vault-item-dashboard), klickar du på **ta bort backup**.

    ![VM-typ](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    Den **ta bort säkerhetskopieringsdata** blad öppnas.

    ![VM-typ](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. Skriv namnet på objektet du vill bekräfta att du vill ta bort återställningspunkterna.

    ![Stoppa verifiering](./media/backup-azure-manage-vms/item-verification-box.png)

    Om du inte vet namnet på objektet, hovrar du över utropstecken ska visa namnet. Namnet på objektet är också **ta bort säkerhetskopieringsdata** överst på bladet.
3. Du kan också ange en **orsak** eller **kommentar**.
4. Ta bort säkerhetskopierade data för det aktuella objektet genom att klicka på ![säkerhetskopiering stoppknappen](./media/backup-azure-manage-vms/delete-button.png)

    Ett meddelande får du reda på dina säkerhetskopierade data har tagits bort.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du skapar en virtuell dator från en återställningspunkt igen [återställa virtuella Azure-datorer](backup-azure-arm-restore-vms.md).
* Om du behöver information om hur du skyddar dina virtuella datorer, se [första titt: Säkerhetskopiera virtuella datorer till Recovery Services-valvet](backup-azure-vms-first-look-arm.md).
* Information om övervakning av händelser finns i [övervaka varningar vid säkerhetskopiering av Azure virtuella datorer](backup-azure-monitor-vms.md).
