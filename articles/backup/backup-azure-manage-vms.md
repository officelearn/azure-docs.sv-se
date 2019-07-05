---
title: Hantera och övervaka Virtuella Azure-säkerhetskopieringar med hjälp av Azure Backup-tjänsten
description: Lär dig mer om att hantera och övervaka Virtuella Azure-säkerhetskopieringar med hjälp av Azure Backup-tjänsten.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: sogup
ms.openlocfilehash: add2c72535b5be0edcbc00c077dfe20a6deaa3e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434214"
---
# <a name="manage-azure-vm-backups"></a>Hantera säkerhetskopior av virtuella Azure-datorer

Den här artikeln beskriver hur du hanterar Azure-datorer (VM) som säkerhetskopieras med hjälp av den [Azure Backup-tjänsten](backup-overview.md). Artikeln sammanfattar också säkerhetskopierad information du hittar på instrumentpanelen för valvet.


I Azure-portalen instrumentpanelen för Recovery Services-valvet ger möjlighet att valvet information, inklusive:

* Den senaste säkerhetskopieringen, vilket också är den senaste återställningspunkten.
* Principen för säkerhetskopiering.
* Den totala storleken på alla ögonblicksbilder av säkerhetskopior.
* Hur många virtuella datorer som har aktiverats för säkerhetskopior.

Du kan hantera säkerhetskopior med hjälp av instrumentpanelen och genom att gå vidare till enskilda virtuella datorer. Säkerhetskopiering av virtuella datorer, öppna valvet på instrumentpanelen.

![Fullständig instrumentpanelsvy med skjutreglaget](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Visa virtuella datorer på instrumentpanelen

Visa virtuella datorer på instrumentpanelen för valvet:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På navmenyn väljer **Bläddra**. I listan över resurser skriver du **Recovery Services**. När du skriver filtreras listan baserat på dina indata. Välj **Recovery Services-valv**.

    ![skapar ett Recovery Services-valv](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. För enkel användning, högerklickar du på valvet och välj **fäst på instrumentpanelen**.
4. Öppna instrumentpanelen för valvet.

    ![Öppna instrumentpanelen för valvet och bladet inställningar](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. På den **Säkerhetskopieringsobjekt** panelen, väljer **Azure Virtual Machines**.

    ![Öppna panelen Säkerhetskopieringsobjekt](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. På den **Säkerhetskopieringsobjekt** bladet, du kan visa listan över skyddade virtuella datorer. I det här exemplet valvet skyddar en virtuell dator: demobackup.  

    ![Visa bladet Säkerhetskopieringsobjekt](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Från instrumentpanelen för valvet objektets att ändra principer för säkerhetskopiering, kör en på begäran-säkerhetskopiering, stoppa eller återuppta skyddet av virtuella datorer, ta bort säkerhetskopierade data, visa återställningspunkter och köra en återställning.

    ![Instrumentpanelen Säkerhetskopieringsobjekt och bladet inställningar](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Hantera principer för säkerhetskopiering för en virtuell dator

Hantera en princip för säkerhetskopiering:

1. Logga in på [Azure Portal](https://portal.azure.com/). Öppna instrumentpanelen för valvet.
2. På den **Säkerhetskopieringsobjekt** panelen, väljer **Azure Virtual Machines**.

    ![Öppna panelen Säkerhetskopieringsobjekt](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. På den **Säkerhetskopieringsobjekt** bladet som du kan visa listan över skyddade virtuella datorer och status för senaste säkerhetskopiering med senaste återställningstid punkter.

    ![Visa bladet Säkerhetskopieringsobjekt](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. Du kan välja en princip för säkerhetskopiering från instrumentpanelen för valvet-objektet.

   * Att byta principer, Välj en annan princip och välj sedan **spara**. Den nya principen tillämpas omedelbart på valvet.

     ![Välj en princip för säkerhetskopiering](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran
Du kan köra en säkerhetskopiering på begäran för en virtuell dator när du ställer in dess skydd. Tänk på följande information:

- Om den första säkerhetskopieringen väntar skapar på begäran-säkerhetskopiering en fullständig kopia av den virtuella datorn i Recovery Services-valvet.
- Om den första säkerhetskopieringen har slutförts, skickas en säkerhetskopiering på begäran endast ändringar från tidigare ögonblicksbild till Recovery Services-valvet. Det vill säga är senare säkerhetskopior alltid inkrementell.
- Kvarhållningsintervallet för en säkerhetskopiering på begäran är kvarhållningsvärdet som du anger när du utlösa säkerhetskopieringen.

Att utlösa en säkerhetskopiering på begäran:

1. På den [instrumentpanelen för valvet objekt](#view-vms-on-the-dashboard)under **skyddade objektet**väljer **Säkerhetskopieringsobjekt**.

    ![Alternativet Backup nu](./media/backup-azure-manage-vms/backup-now-button.png)

2. Från **typ av Säkerhetskopieringshantering**väljer **Azure-dator**. Den **Säkerhetskopieringsobjekt (Azure virtuell dator)** bladet visas.
3. Välj en virtuell dator och **säkerhetskopiering nu** att skapa en säkerhetskopiering på begäran. Den **Säkerhetskopiera nu** bladet visas.
4. I den **behåller säkerhetskopierade tills** fältet, ange ett datum för säkerhetskopiering ska behållas.

    ![Säkerhetskopiera nu kalender](./media/backup-azure-manage-vms/backup-now-check.png)

5. Välj **OK** att köra jobbet.

Om du vill spåra på jobbets status på instrumentpanelen för valvet, Välj den **säkerhetskopieringsjobb** panelen.

## <a name="stop-protecting-a-vm"></a>Sluta skydda en virtuell dator

Det finns två sätt att sluta skydda en virtuell dator:

* **Stoppa skyddet och Behåll säkerhetskopieringsdata**. Det här alternativet stoppas alla framtida säkerhetskopieringsjobb från att skydda din virtuella dator; Azure Backup-tjänsten kommer dock behålla återställningspunkter som har säkerhetskopierats.  Du måste betala för att behålla återställningspunkter i valvet (se [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/) information). Du kommer att kunna återställa den virtuella datorn om det behövs. Om du vill återuppta skydd av virtuell dator så kan du använda *återuppta säkerhetskopiering* alternativet.
* **Stoppa skyddet och ta bort säkerhetskopieringsdata**. Det här alternativet kommer att stoppa alla framtida säkerhetskopieringsjobb från att skydda den virtuella datorn och ta bort alla återställningspunkter. Du kan inte återställa den virtuella datorn eller använda *återuppta säkerhetskopiering* alternativet.

>[!NOTE]
>Om du tar bort en datakälla utan att stoppa säkerhetskopiering misslyckas nya säkerhetskopior. Gamla återställningspunkterna upphör att gälla enligt principen, men en senaste återställningspunkten är alltid kvar tills du stoppar säkerhetskopieringarna och ta bort data.
>

### <a name="stop-protection-and-retain-backup-data"></a>Stoppa skyddet och behålla säkerhetskopierade data

Stoppa skyddet och behålla data för en virtuell dator:

1. På den [valv objektets instrumentpanelen](#view-vms-on-the-dashboard)väljer **stoppa säkerhetskopiering**.
2. Välj **Behåll säkerhetskopieringsdata**, och bekräftar valet efter behov. Lägg till en kommentar om du vill. Om du inte är säker på objektnamnet hovra över utropstecken ska visa namnet.

    ![Behåll säkerhetskopieringsdata](./media/backup-azure-manage-vms/retain-backup-data.png)

Ett meddelande talar om att säkerhetskopiera jobb har stoppats.

### <a name="stop-protection-and-delete-backup-data"></a>Stoppa skyddet och ta bort säkerhetskopierade data

Stoppa skyddet och ta bort data för en virtuell dator:

1. På den [valv objektets instrumentpanelen](#view-vms-on-the-dashboard)väljer **stoppa säkerhetskopiering**.
2. Välj **ta bort säkerhetskopieringsdata**, och bekräftar valet efter behov. Ange namnet på säkerhetskopieringsobjektet och Lägg till en kommentar om du vill.

    ![Ta bort säkerhetskopieringsdata](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Återuppta skyddet av en virtuell dator

Om du hade valt [sluta skydda och Behåll säkerhetskopieringsdata](#stop-protection-and-retain-backup-data) alternativet under stoppa skydd av virtuell dator, kan du använda **återuppta säkerhetskopiering**. Det här alternativet är inte tillgängligt om du väljer [stoppa skyddet och ta bort säkerhetskopieringsdata](#stop-protection-and-delete-backup-data) alternativet eller [ta bort säkerhetskopieringsdata](#delete-backup-data).

Att återuppta skyddet av en virtuell dator:

1. På den [valv objektets instrumentpanelen](#view-vms-on-the-dashboard)väljer **återuppta säkerhetskopiering**.

2. Följ stegen i [hantera säkerhetskopieringsprinciper](#manage-backup-policy-for-a-vm) att tilldela principen för den virtuella datorn. Du behöver inte välja den Virtuella datorns första protection-principen.
3. När du har tillämpat principen för säkerhetskopiering till den virtuella datorn ser du följande meddelande:

    ![Meddelande om en har skyddad virtuell dator](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata

Det finns två sätt att ta bort säkerhetskopierade data för en virtuell dator:

- Från instrumentpanelen för valvet objekt väljer du stoppa säkerhetskopiering och följer anvisningarna för [stoppa skyddet och ta bort säkerhetskopieringsdata](#stop-protection-and-delete-backup-data) alternativet.

  ![Välja Avbryt säkerhetskopiering](./media/backup-azure-manage-vms/stop-backup-buttom.png)

- Välj Ta bort säkerhetskopierade data från instrumentpanelen för valvet objekt. Det här alternativet är aktiverat om du har valt att [sluta skydda och Behåll säkerhetskopieringsdata](#stop-protection-and-retain-backup-data) under stoppa skydd av virtuell dator

  ![Välj Ta bort säkerhetskopia](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  - På den [instrumentpanelen för valvet objekt](#view-vms-on-the-dashboard)väljer **ta bort säkerhetskopieringsdata**.
  - Skriv namnet på säkerhetskopieringsobjektet att bekräfta att du vill ta bort återställningspunkterna.

    ![Ta bort säkerhetskopieringsdata](./media/backup-azure-manage-vms/delete-backup-data1.png)

  - Om du vill ta bort säkerhetskopierade data för objektet, Välj **ta bort**. Ett meddelande kan du vet att dina säkerhetskopierade data har tagits bort.

  > [!NOTE]
  > När du tar bort säkerhetskopierade data tar du bort alla associerade återställningspunkter. Du kan inte välja specifika återställningspunkter som ska tas bort.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [säkerhetskopiera virtuella Azure-datorer från den Virtuella datorns inställningar](backup-azure-vms-first-look-arm.md).
- Lär dig hur du [återställa virtuella datorer](backup-azure-arm-restore-vms.md).
- Lär dig hur du [övervaka Virtuella Azure-säkerhetskopieringar](backup-azure-monitor-vms.md).
