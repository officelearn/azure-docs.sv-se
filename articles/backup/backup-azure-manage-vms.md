---
title: Hantera och övervaka Virtuella Azure-säkerhetskopieringar med Azure Backup-tjänsten
description: Lär dig mer om att hantera och övervaka säkerhetskopiering av Azure virtuella datorer med Azure Backup-tjänsten.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: eef1b14f5f25c53877c0d364facfcf9817f508c2
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56885847"
---
# <a name="manage-azure-vm-backups"></a>Hantera säkerhetskopior av virtuella Azure-datorer

Den här artikeln beskriver hur du hanterar virtuella Azure-datorer säkerhetskopieras med de [Azure Backup-tjänsten](backup-overview.md) säkerhetskopior, och sammanfattar säkerhetskopieringsaviseringar informationen på portalens instrumentpanel.


Instrumentpanelen för Recovery Services-valvet ger tillgång till information om valvet, inklusive Azure-portalen:

* Den senaste säkerhetskopieringen, vilket också är den senaste återställningspunkten.
* principen för säkerhetskopiering
* Den totala storleken på alla ögonblicksbilder av säkerhetskopior
* Hur många virtuella datorer som har aktiverats för säkerhetskopiering

Du kan hantera säkerhetskopior med hjälp av instrumentpanelen, och genom att gå vidare till enskilda virtuella datorer. achine säkerhetskopiering börjar med att öppna valvet i instrumentpanelen. 

![Fullständig vy med skjutreglaget](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-in-the-dashboard"></a>Visa virtuella datorer i instrumentpanelen

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På navmenyn klickar du på **Bläddra** och i listan över resurser skriver du **Recovery Services**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Recovery Services-valv**. 

    ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. För enkel användning, högerklickar du på valvet i listan över valv > **fäst på instrumentpanelen**.
4. Öppna instrumentpanelen för valvet. 
    ![Öppna instrumentpanelen för valvet och bladet inställningar](./media/backup-azure-manage-vms/full-view-rs-vault.png)

4. På den **Säkerhetskopieringsobjekt** klickar **Azure Virtual Machines**.

    ![Öppna säkerhetskopieringsobjekt panel](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. I **Säkerhetskopieringsobjekt** bladet kan du se senaste säkerhetskopieringsjobbet för varje objekt. I det här exemplet har en virtuell dator, demovm markgal skyddas av det här valvet.  

    ![Objekt att säkerhetskopiera panel](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

 
6. Från instrumentpanelen för valvet objekt kan du skapa eller ändra principer för säkerhetskopiering, visa återställningspunkter, kör en på begäran-säkerhetskopiering, stoppa och återuppta skyddet av virtuella datorer, ta bort återställningspunkter och köra en återställning.

    ![Objekt att säkerhetskopiera instrumentpanel med bladet inställningar](./media/backup-azure-manage-vms/item-dashboard-settings.png)



## <a name="manage-backup-policies"></a>Hantera säkerhetskopieringsprinciper
1. På den [instrumentpanelen för valvet objekt](#view-vms-in-the-dashboard), klickar du på **alla inställningar** .

    ![Bladet säkerhetskopieringspolicy](./media/backup-azure-manage-vms/all-settings-button.png)
2. I **inställningar**, klickar du på**säkerhetskopieringspolicy**e.
3. Från den **Välj säkerhetskopieringspolicy** menyn:

   * Om du vill ändra principer, Välj en annan princip och klicka på **spara**. Den nya principen tillämpas omedelbart på valvet.
   * Skapa en princip för att välja **Skapa ny**. [Läs mer](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![Säkerhetskopiering av virtuell dator](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran
Du kan säkerhetskopiera en på begäran för en virtuell dator när den är konfigurerad för skydd.
- Om den första säkerhetskopieringen väntar skapar på begäran-säkerhetskopiering en fullständig kopia av den virtuella datorn i Recovery Services-valvet.
- Om den första säkerhetskopieringen har slutförts, skickas en säkerhetskopiering på begäran endast ändringar från tidigare ögonblicksbild till Recovery Services-valvet. Det vill säga är efterföljande säkerhetskopieringar alltid inkrementell.
- Kvarhållningsintervallet för en säkerhetskopiering på begäran är kvarhållningsvärdet som angetts för daglig säkerhetskopieringspunkt i principen. Om ingen daglig säkerhetskopieringspunkt väljs, används veckovis säkerhetskopieringspunkt.


Att utlösa en säkerhetskopiering på begäran:

1. På den [instrumentpanelen för valvet objekt](#view-vms-in-the-dashboard), klickar du på **Säkerhetskopiera nu**.

    ![Säkerhetskopiering nu knappen](./media/backup-azure-manage-vms/backup-now-button.png)

 2. Klicka på **Ja** att starta säkerhetskopieringen.

    ![Säkerhetskopiering nu knappen](./media/backup-azure-manage-vms/backup-now-check.png)

 
 Säkerhetskopieringsjobbet skapar en återställningspunkt. Kvarhållningsintervallet för återställningspunkten är samma som kvarhållningsintervallet som angetts i principen som är associerade med den virtuella datorn. Om du vill spåra förloppet för jobbet i instrumentpanelen för valvet klickar du på den **säkerhetskopieringsjobb** panelen.  

## <a name="stop-protecting-a-vm"></a>Sluta skydda en virtuell dator

Det finns två sätt att sluta skydda virtuella datorer:

- Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter. Du kan inte återställa den virtuella datorn i det här fallet.
- Stoppa alla framtida säkerhetskopieringsjobb men lämna kvar återställningspunkterna. Det finns en kostnad som är associerade med att lämna kvar återställningspunkterna i lagring. Fördelen med att lämna kvar återställningspunkterna är dock att du kan återställa den virtuella datorn om det behövs. [Läs mer](https://azure.microsoft.com/pricing/details/backup/) om priser.

Tänk på följande:

- Om du lämnar återställningspunkterna tas poäng bort i enlighet med principen för säkerhetskopiering. Du debiteras för den skyddade instansen och förbrukad lagring tills alla återställningspunkter rensas. [Läs mer](https://azure.microsoft.com/pricing/details/backup/) om prissättning.
- När du lämnar återställningspunkter intakta, även om de går ut enligt policy för datalagring, håller Azure Backup alltid en senaste återställningspunkt tills du uttryckligen tar bort säkerhetskopierade data.
- Om du tar bort en datakälla utan att stoppa säkerhetskopiering kommer nya säkerhetskopior börjar misslyckas. Igen, gamla återställningspunkterna upphör att gälla enligt principen, men en senaste återställningspunkt sparas alltid tills du stoppar säkerhetskopiering och ta bort data.



Sluta skydda en virtuell dator:

1. På den [instrumentpanelen för valvet objekt](#view-vms-in-the-dashboard), klickar du på **stoppa säkerhetskopiering**.
2. Välj om du vill behålla eller ta bort säkerhetskopierade data och Bekräfta efter behov. Bekräfta efter behov och du kan också ange en kommentar. Om du inte vet namnet på objektet, hovrar du över utropstecken ska visa namnet.

    ![Sluta skydda](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 Ett meddelande får du reda på säkerhetskopieringsjobb har stoppats.


## <a name="resume-protection-of-a-vm"></a>Återuppta skyddet av en virtuell dator

Om du bevaras säkerhetskopierade data när den virtuella datorn har stoppats, kan du återuppta skyddet. Om du tar bort säkerhetskopierade data och du inte kan återuppta.

Te

1. På den [instrumentpanelen för valvet objekt](#view-vms-in-the-dashboard), klickar du på **återuppta säkerhetskopiering**.

2. Följ stegen i [hantera säkerhetskopieringsprinciper](#manage-backup-policies) att tilldela principen för den virtuella datorn. Du kan välja en annan princip än den princip som virtuella datorn ursprungligen skyddades.
3. När principen för säkerhetskopiering används på den virtuella datorn, visas följande meddelande.

    ![Har skyddad virtuell dator](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata

Du kan ta bort säkerhetskopierade data som är associerade med en virtuell dator under den **stoppa säkerhetskopiering** jobb, eller när som helst efter säkerhetskopieringen jobbet har slutförts.

- Det kan vara en fördel att vänta dagar eller veckor innan du tar bort återställningspunkterna.
- Till skillnad från återställning av återställningspunkter när du tar bort säkerhetskopierade data, kan du inte välja specifika återställningspunkter som ska tas bort. Om du väljer att ta bort säkerhetskopierade data, tar du bort alla återställningspunkter som är associerade med objektet.

Den här proceduren förutsätter att säkerhetskopieringen av den virtuella datorn har stoppats eller inaktiverats.


1. På den [instrumentpanelen för valvet objekt](#view-vms-in-the-dashboard), klickar du på **ta bort backup**.

    ![VM-typ](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. Skriv namnet på objektet du vill bekräfta att du vill ta bort återställningspunkterna.

    ![Stoppa verifiering](./media/backup-azure-manage-vms/item-verification-box.png)

4. Ta bort säkerhetskopierade data för det aktuella objektet genom att klicka på ![säkerhetskopiering stoppknappen](./media/backup-azure-manage-vms/delete-button.png)

    Ett meddelande får du reda på dina säkerhetskopierade data har tagits bort.

## <a name="next-steps"></a>Nästa steg
- [Lär dig mer om](backup-azure-vms-first-look-arm.md) säkerhetskopiera virtuella Azure-datorer från inställningarna för virtuella datorer.
- [Lär dig mer om](backup-azure-arm-restore-vms.md) återställning av virtuella datorer. 
- [Lär dig mer om](backup-azure-monitor-vms.md) övervakning av Virtuella Azure-säkerhetskopieringar.
 
