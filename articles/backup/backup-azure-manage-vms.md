---
title: Hantera och övervaka Azure VM-säkerhetskopior
description: Lär dig hur du hanterar och övervakar Azure VM-säkerhetskopior med hjälp av Azure Backup-tjänsten.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: dd4e9dc199048b3faf3da0cadfdf60bdcb26c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248156"
---
# <a name="manage-azure-vm-backups-with-azure-backup-service"></a>Hantera Azure VM-säkerhetskopior med Azure Backup-tjänst

I den här artikeln beskrivs hur du hanterar virtuella Azure-datorer (VMs) som säkerhetskopieras med hjälp av [Azure Backup-tjänsten](backup-overview.md). Artikeln sammanfattar också säkerhetskopieringsinformationen som du hittar på instrumentpanelen i valvet.

I Azure-portalen ger instrumentpanelen för Recovery Services-valvet åtkomst till arkivinformation, inklusive:

* Den senaste säkerhetskopian, som också är den senaste återställningspunkten.
* Säkerhetskopieringsprincipen.
* Den totala storleken på alla ögonblicksbilder av säkerhetskopiering.
* Antalet virtuella datorer som är aktiverade för säkerhetskopior.

Du kan hantera säkerhetskopior med hjälp av instrumentpanelen och genom att gå ned till enskilda virtuella datorer. Om du vill starta säkerhetskopiering av datorer öppnar du valvet på instrumentpanelen.

![Fullständig instrumentpanelsvy med skjutreglage](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-on-the-dashboard"></a>Visa virtuella datorer på instrumentpanelen

Så här visar du virtuella datorer på instrumentpanelen i valvet:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Bläddra**på Hub-menyn . I listan över resurser skriver du **Recovery Services**. När du skriver filtreras listan baserat på dina indata. Välj **Recovery Services-valv**.

    ![skapar ett Recovery Services-valv](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. För enkel användning högerklickar du på valvet och väljer **Fäst på instrumentpanelen**.
4. Öppna instrumentpanelen för valvet.

    ![Öppna instrumentpanelen och fönstret Inställningar för valvet](./media/backup-azure-manage-vms/full-view-rs-vault.png)

5. Välj **Virtuella Azure-datorer**på panelen **Säkerhetsobjekt** .

    ![Öppna panelen Säkerhetskopieringsobjekt](./media/backup-azure-manage-vms/contoso-vault-1606.png)

6. I fönstret **Säkerhetsobjekt** kan du visa listan över skyddade virtuella datorer. I det här exemplet skyddar valvet en virtuell dator: demobackup.  

    ![Visa fönstret Säkerhetskopieringsobjekt](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

7. Från instrumentpanelen för valvobjektet ändrar du principer för säkerhetskopiering, kör en säkerhetskopiering på begäran, stoppar eller återupptar skyddet av virtuella datorer, tar bort säkerhetskopierade data, visar återställningspunkter och kör en återställning.

    ![Instrumentpanelen För säkerhetskopieringsobjekt och fönstret Inställningar](./media/backup-azure-manage-vms/item-dashboard-settings.png)

## <a name="manage-backup-policy-for-a-vm"></a>Hantera säkerhetskopieringsprincip för en virtuell dator

Så här hanterar du en princip för säkerhetskopiering:

1. Logga in på [Azure-portalen](https://portal.azure.com/). Öppna instrumentpanelen för valvet.
2. Välj **Virtuella Azure-datorer**på panelen **Säkerhetsobjekt** .

    ![Öppna panelen Säkerhetskopieringsobjekt](./media/backup-azure-manage-vms/contoso-vault-1606.png)

3. I fönstret **Säkerhetskopieringsobjekt** kan du visa listan över skyddade virtuella datorer och senaste säkerhetskopieringsstatus med senaste återställningspoängtid.

    ![Visa fönstret Säkerhetskopieringsobjekt](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

4. På instrumentpanelen för arkivobjektet kan du välja en princip för säkerhetskopiering.

   * Om du vill byta principer väljer du en annan princip och väljer sedan **Spara**. Den nya principen tillämpas omedelbart på valvet.

     ![Välj en säkerhetskopieringsprincip](./media/backup-azure-manage-vms/backup-policy-create-new.png)

## <a name="run-an-on-demand-backup"></a>Kör en säkerhetskopiering på begäran

Du kan köra en säkerhetskopiering på begäran av en virtuell dator när du har konfigurerat dess skydd. Tänk på följande:

* Om den första säkerhetskopieringen väntar skapar säkerhetskopiering på begäran en fullständig kopia av den virtuella datorn i valvet för återställningstjänster.
* Om den första säkerhetskopian är klar skickar en säkerhetskopiering på begäran endast ändringar från den tidigare ögonblicksbilden till Recovery Services-valvet. Det vill än, senare säkerhetskopior är alltid inkrementella.
* Kvarhållningsintervallet för en säkerhetskopiering på begäran är det kvarhållningsvärde som du anger när du utlöser säkerhetskopian.

Så här utlöser du en säkerhetskopiering på begäran:

1. Välj **Säkerhetskopieringsobjekt**under **Skyddat objekt**på [instrumentpanelen](#view-vms-on-the-dashboard)för arkivobjekt .

    ![Alternativet Säkerhetskopiering nu](./media/backup-azure-manage-vms/backup-now-button.png)

2. Välj Azure Virtual **Machine**från **hantering av säkerhetskopieringstyp**. Fönstret **Säkerhetskopieringsobjekt (Azure Virtual Machine)** visas.
3. Välj en virtuell dator och välj **Säkerhetskopiering nu** för att skapa en säkerhetskopiering på begäran. Fönstret **Säkerhetskopiera nu** visas.
4. I fältet **Behåll säkerhetskopiering till** anger du ett datum för säkerhetskopieringen som ska behållas.

    ![Kalendern För säkerhetskopiering nu](./media/backup-azure-manage-vms/backup-now-check.png)

5. Välj **OK** om du vill köra säkerhetskopieringsjobbet.

Om du vill spåra jobbets förlopp väljer du panelen **Säkerhetskopieringsjobb** på instrumentpanelen för valv.

## <a name="stop-protecting-a-vm"></a>Sluta skydda en virtuell dator

Det finns två sätt att sluta skydda en virtuell dator:

* **Stoppa skyddet och behåll säkerhetskopierade data**. Det här alternativet stoppar alla framtida säkerhetskopieringsjobb från att skydda din virtuella dator. Azure Backup-tjänsten behåller dock återställningspunkterna som har säkerhetskopierats.  Du måste betala för att behålla återställningspunkterna i valvet (se [Azure Backup-priser](https://azure.microsoft.com/pricing/details/backup/) för mer information). Du kan återställa den virtuella datorn om det behövs. Om du bestämmer dig för att återuppta vm-skydd kan du använda alternativet *Återuppta säkerhetskopiering.*
* **Stoppa skyddet och ta bort säkerhetskopierade data**. Det här alternativet stoppar alla framtida säkerhetskopieringsjobb från att skydda din virtuella dator och ta bort alla återställningspunkter. Du kan inte återställa den virtuella datorn eller använda alternativet *Återuppta säkerhetskopiering.*

>[!NOTE]
>Om du tar bort en datakälla utan att stoppa säkerhetskopior misslyckas nya säkerhetskopior. Gamla återställningspunkter upphör att gälla enligt principen, men en sista återställningspunkt kommer alltid att behållas tills du stoppar säkerhetskopiorna och tar bort data.
>

### <a name="stop-protection-and-retain-backup-data"></a>Stoppa skyddet och behåll säkerhetskopierade data

Så här stoppar du skyddet och behåller data för en virtuell dator:

1. På [instrumentpanelen för valvobjektet](#view-vms-on-the-dashboard)väljer du **Stoppa säkerhetskopiering**.
2. Välj **Behåll säkerhetskopieringsdata**och bekräfta ditt val efter behov. Lägg till en kommentar om du vill. Om du inte är säker på objektets namn håller du muspekaren över utropstecknet för att visa namnet.

    ![Behåll säkerhetskopierade data](./media/backup-azure-manage-vms/retain-backup-data.png)

Ett meddelande visar att säkerhetskopieringsjobben har stoppats.

### <a name="stop-protection-and-delete-backup-data"></a>Stoppa skyddet och ta bort säkerhetskopierade data

Så här stoppar du skyddet och tar bort data för en virtuell dator:

1. På [instrumentpanelen för valvobjektet](#view-vms-on-the-dashboard)väljer du **Stoppa säkerhetskopiering**.
2. Välj **Ta bort säkerhetskopierade data**och bekräfta ditt val efter behov. Ange namnet på säkerhetskopian och lägg till en kommentar om du vill.

    ![Ta bort säkerhetskopieringsdata](./media/backup-azure-manage-vms/delete-backup-data1.png)

## <a name="resume-protection-of-a-vm"></a>Återuppta skyddet av en virtuell dator

Om du väljer Stoppa skydd och behålla alternativet [för säkerhetskopieringsdata](#stop-protection-and-retain-backup-data) under skydd av virtuella datorer kan du använda **Återuppta säkerhetskopiering**. Det här alternativet är inte tillgängligt om du väljer Stoppa skydd och ta bort alternativet [för säkerhetskopierade data](#stop-protection-and-delete-backup-data) eller [Ta bort säkerhetskopierade data](#delete-backup-data).

Så här återupptar du skyddet för en virtuell dator:

1. På [instrumentpanelen för valvobjektet](#view-vms-on-the-dashboard)väljer du **Återuppta säkerhetskopiering**.

2. Följ stegen i [Hantera principer för säkerhetskopiering](#manage-backup-policy-for-a-vm) för att tilldela principen för den virtuella datorn. Du behöver inte välja den virtuella datorns ursprungliga skyddsprincip.
3. NÃ¤5 ã¤nder du Ã¤nder ã¤nder Ã¤nderã¤ndesprincipen fÃ¶nde vid Ã¤nde den virtuella datorn visas

    ![Meddelande som anger en skyddad virtuell dator](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Ta bort säkerhetskopieringsdata

Det finns två sätt att ta bort en virtuell dators säkerhetskopieringsdata:

* På instrumentpanelen för arkivobjekt väljer du Stoppa säkerhetskopiering och följ instruktionerna för [Stoppa skydd och ta bort säkerhetskopieringsdata.](#stop-protection-and-delete-backup-data)

  ![Välja Avbryt säkerhetskopiering](./media/backup-azure-manage-vms/stop-backup-buttom.png)

* Välj Ta bort säkerhetskopierade data på instrumentpanelen för arkivobjekt. Det här alternativet är aktiverat om du hade valt att stoppa skyddet och behålla alternativet [för säkerhetskopieringsdata](#stop-protection-and-retain-backup-data) under stopp av VM-skydd

  ![Välj Ta bort säkerhetskopia](./media/backup-azure-manage-vms/delete-backup-buttom.png)

  * På [instrumentpanelen för arkivobjekt](#view-vms-on-the-dashboard)väljer du **Ta bort säkerhetskopierade data**.
  * Skriv namnet på säkerhetskopian för att bekräfta att du vill ta bort återställningspunkterna.

    ![Ta bort säkerhetskopieringsdata](./media/backup-azure-manage-vms/delete-backup-data1.png)

  * Om du vill ta bort säkerhetskopierade data för objektet väljer du **Ta bort**. Ett meddelande visar att säkerhetskopieringsdata har tagits bort.

För att skydda dina data innehåller Azure Backup funktionen för mjuk borttagning. Med mjuk borttagning, även efter säkerhetskopiering (alla återställningspunkter) av en virtuell dator tas bort, sparas säkerhetskopieringsdata i ytterligare 14 dagar. Mer information finns [i dokumentationen för mjuk borttagning](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

  > [!NOTE]
  > När du tar bort säkerhetskopierade data tar du bort alla associerade återställningspunkter. Du kan inte välja specifika återställningspunkter att ta bort.

### <a name="backup-item-where-primary-data-source-no-longer-exists"></a>Säkerhetskopior där primär datakälla inte längre finns

* Om Azure-virtuella datorer som konfigurerats för Azure-säkerhetskopiering antingen tas bort eller flyttas utan att skydda, misslyckas både schemalagda säkerhetskopieringsjobb och online-säkerhetskopiera jobb (ad hoc) med felet UserErrorVmNotFoundV2. Förkontrollen av säkerhetskopian visas endast som kritisk för misslyckade säkerhetskopieringsjobb på begäran (misslyckade schemalagda jobb visas inte).
* Dessa säkerhetskopieringsobjekt förblir aktiva i systemet som följer principen för säkerhetskopiering och kvarhållning som anges av användaren. Säkerhetskopierade data för dessa virtuella Azure-datorer kommer att behållas enligt bevarandeprincipen. De utgångna återställningspunkterna (förutom den senaste återställningspunkten) rensas enligt kvarhållningsintervallet som anges i säkerhetskopieringsprincipen.
* Användare rekommenderas att ta bort säkerhetskopieringsobjekten där den primära datakällan inte längre finns för att undvika eventuella extra kostnader, om säkerhetskopieringsobjektet/data för borttagningsresurserna inte längre krävs eftersom den sista återställningspunkten behålls för alltid och användaren debiteras enligt den säkerhetskopieringsprissättning som gäller.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [säkerhetskopierar virtuella Azure-datorer från den virtuella datorns inställningar](backup-azure-vms-first-look-arm.md).
* Läs om hur du [återställer virtuella datorer](backup-azure-arm-restore-vms.md).
* Lär dig hur du [övervakar Azure VM-säkerhetskopior](backup-azure-monitor-vms.md).
