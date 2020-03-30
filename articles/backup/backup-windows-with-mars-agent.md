---
title: Säkerhetskopiera Windows-datorer med marsagenten
description: Använd MARS-agenten (Microsoft Azure Recovery Services) för att säkerhetskopiera Windows-datorer.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 12463f33a6fa97b33e70b77fb2fcf6b0a27b5790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408920"
---
# <a name="back-up-windows-machines-by-using-the-azure-backup-mars-agent"></a>Säkerhetskopiera Windows-datorer med hjälp av Azure Backup MARS-agenten

I den här artikeln beskrivs hur du säkerhetskopierar Windows-datorer med hjälp av [Azure Backup-tjänsten](backup-overview.md) och MICROSOFT Azure Recovery Services (MARS)-agenten. MARS kallas även Azure Backup-agenten.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
>
> * Verifiera kraven
> * Skapa en princip för säkerhetskopiering och schema.
> * Utför en säkerhetskopiering på begäran.

## <a name="before-you-start"></a>Innan du börjar

* Lär dig mer om hur [Azure Backup använder MARS-agenten för att säkerhetskopiera Windows-datorer](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Lär dig mer om [säkerhetskopieringsarkitekturen](backup-architecture.md#architecture-back-up-to-dpmmabs) som kör MARS-agenten på en sekundär MABS- eller Data Protection Manager-server.
* Granska [vad som stöds och vad du kan säkerhetskopiera](backup-support-matrix-mars-agent.md) av MARS-agenten.
* [Verifiera internetåtkomsten](install-mars-agent.md#verify-internet-access) på de datorer som du vill säkerhetskopiera.
* Om MARS-agenten inte är installerad kan du läsa om hur du installerar den [här](install-mars-agent.md).

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

Säkerhetskopieringsprincipen anger när ögonblicksbilder av data ska tas för att skapa återställningspunkter. Den anger också hur länge återställningspunkter ska behållas. Du använder MARS-agenten för att konfigurera en principer för säkerhetskopiering.

Azure Backup tar inte automatiskt hänsyn till sommartid (DST). Den här standardinställningen kan orsaka vissa avvikelser mellan den faktiska tiden och den schemalagda säkerhetskopieringstiden.

Så här skapar du en säkerhetskopieringspolicy:

1. När du har hämtat och registrerat MARS-agenten öppnar du agentkonsolen. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.  

1. Under **Åtgärder**väljer du **Schemalägg säkerhetskopiering**.

    ![Schemalägga en Windows Server-säkerhetskopiering](./media/backup-configure-vault/schedule-first-backup.png)
1. Välj Komma igång > **nästa**i guiden Schemalägg **säkerhetskopiering**.
1. Under **Välj objekt som ska säkerhetskopieras**väljer du Lägg till **objekt**.

    ![Lägga till objekt som ska säkerhetskopieras](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. I rutan **Välj objekt** markerar du objekt som ska säkerhetskopieras och väljer sedan **OK**.

    ![Markera objekt som ska säkerhetskopieras](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. På sidan **Välj objekt som ska säkerhetskopieras** väljer du **Nästa**.
1. På sidan **Ange schema för säkerhetskopiering** anger du när du ska göra dagliga eller veckovisa säkerhetskopior. Välj sedan **Nästa**.

    * En återställningspunkt skapas när en säkerhetskopia görs.
    * Antalet återställningspunkter som skapas i din miljö beror på ditt säkerhetskopieringsschema.
    * Du kan schemalägga upp till tre dagliga säkerhetskopior per dag. I följande exempel sker två dagliga säkerhetskopior, en vid midnatt och en klockan 18:00.

        ![Konfigurera ett dagligt schema för säkerhetskopiering](./media/backup-configure-vault/day-schedule.png)

    * Du kan köra veckovisa säkerhetskopior också. I följande exempel görs säkerhetskopior varje alternativ söndag och onsdag klockan 9:30 och 01:00.

        ![Konfigurera ett veckoschema för säkerhetskopiering](./media/backup-configure-vault/week-schedule.png)

1. På sidan **Välj bevarandeprincip** anger du hur historiska kopior av dina data ska lagras. Välj sedan **Nästa**.

    * Bevarandeinställningar anger vilka återställningspunkter som ska lagras och hur länge de ska lagras.
    * För en daglig kvarhållningsinställning anger du att den senaste återställningspunkten kommer att behållas under det angivna antalet dagar vid den angivna tidpunkten för den dagliga kvarhållningen. Du kan också ange en månatlig bevarandeprincip för att ange att återställningspunkten som skapas den 30:e varje månad ska lagras i 12 månader.
    * Kvarhållning för dagliga och veckovisa återställningspunkter sammanfaller vanligtvis med säkerhetskopieringsschemat. Så när schemat utlöser en säkerhetskopia lagras återställningspunkten som säkerhetskopian skapar under den tid som den dagliga eller veckovisa bevarandeprincipen anger.
    * Se följande exempel:

        * Dagliga säkerhetskopior vid midnatt och 18:00 hålls i sju dagar.
        * Säkerhetskopior som tas på en lördag vid midnatt och 18:00 hålls i fyra veckor.
        * Säkerhetskopior som tas den sista lördagen i månaden vid midnatt och 18:00 hålls i 12 månader.
        * Säkerhetskopior som togs den sista lördagen i mars hålls i 10 år.

        ![Exempel på en bevarandeprincip](./media/backup-configure-vault/retention-example.png)

1. På sidan **Välj inledande typ av säkerhetskopiering** bestämmer du om du vill göra den första säkerhetskopieringen över nätverket eller använda offlinesäkerhetskopiering. Om du vill göra den första säkerhetskopian över nätverket väljer du **Automatiskt över nätverket** > **Nästa**.

    Mer information om säkerhetskopiering offline finns i [Använda Azure Data Box för offlinesäkerhetskopiering](offline-backup-azure-data-box.md).

    ![Välj en första typ av säkerhetskopiering](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. Granska informationen på sidan **Bekräftelse** och välj sedan **Slutför**.

    ![Bekräfta typen för säkerhetskopiering](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. När guiden har skapat säkerhetskopieringsschemat väljer du **Stäng**.

    ![Visa förloppet för säkerhetskopieringsschema](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Skapa en princip på varje dator där agenten är installerad.

### <a name="do-the-initial-backup-offline"></a>Gör den första säkerhetskopian offline

Du kan köra en första säkerhetskopia automatiskt över nätverket eller säkerhetskopiera offline. Offline seedning för en första säkerhetskopia är användbart om du har stora mängder data som kräver en hel del nätverksbandbredd för att överföra.

Så här gör du en offlineöverföring:

1. Skriv säkerhetskopieringsdata till en mellanlagringsplats.
1. Använd azureofflineBackupDiskPrep-verktyget för att kopiera data från mellanlagringsplatsen till en eller flera SATA-diskar.

    Verktyget skapar ett Azure Import-jobb. Mer information finns i [Vad är azure import/export-tjänsten](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).
1. Skicka SATA-diskarna till ett Azure-datacenter.

    I datacentret kopieras diskdata till ett Azure-lagringskonto. Azure Backup kopierar data från lagringskontot till valvet och inkrementella säkerhetskopior schemaläggs.

Mer information om offlineutsäd finns i [Använda Azure Data Box för offlinesäkerhetskopiering](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Aktivera nätverksbegränsning

Du kan styra hur MARS-agenten använder nätverksbandbredd genom att aktivera nätverksbegränsning. Begränsning är användbart om du behöver säkerhetskopiera data under arbetstid men du vill styra hur mycket bandbredd säkerhetskopieringen och återställningsaktiviteten använder.

Nätverksbegränsning i Azure Backup använder [QoS (Quality of Service)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) i det lokala operativsystemet.

Nätverksbegränsning för säkerhetskopior är tillgängligt på Windows Server 2012 och senare, och i Windows 8 och senare. Operativsystemen ska köra de senaste Service Pack-versionerna.

Så här aktiverar du nätverksbegränsning:

1. Välj **Ändra egenskaper**i MARS-agenten .
1. På fliken **Begränsning** väljer du Aktivera begränsning av **internetbandbredd för säkerhetskopieringsåtgärder**.

    ![Konfigurera nätverksbegränsning för säkerhetskopieringsåtgärder](./media/backup-configure-vault/throttling-dialog.png)
1. Ange tillåten bandbredd under arbetstid och ledig tid. Bandbreddsvärden börjar vid 512 kbit/s och går upp till 1 023 MBps. Välj sedan **OK**.

## <a name="run-an-on-demand-backup"></a>Kör en säkerhetskopiering på begäran

1. I MARS-agenten väljer du **Säkerhetskopiera nu**.

    ![Säkerhetskopiera nu i Windows Server](./media/backup-configure-vault/backup-now.png)

1. Om MARS-agentversionen är 2.0.9169.0 eller nyare kan du ange ett anpassat kvarhållningsdatum. Välj ett datum i kalendern i avsnittet **Behåll säkerhetskopiering till.**

   ![Använda kalendern för att anpassa ett kvarhållningsdatum](./media/backup-configure-vault/mars-ondemand.png)

1. Granska inställningarna på sidan **Bekräftelse** och välj **Säkerhetskopiera**.
1. Välj **Stäng** om du vill stänga guiden. Om du stänger guiden innan säkerhetskopian är klar fortsätter guiden att köras i bakgrunden.

När den första säkerhetskopian är klar visas statusen **Jobb färdigt** i konsolen Säkerhetskopiering.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>Ställ in lagringsbeteende för säkerhetskopiering av principer för säkerhetskopiering på begäran

> [!NOTE]
> Den här informationen gäller endast MARS-agentversioner som är äldre än 2.0.9169.0.
>

| Alternativ för säkerhetskopieringsschema | Varaktighet för lagring av uppgifter
| -- | --
| Day | **Standardkvarhållning:** Motsvarar "kvarhållning i dagar för dagliga säkerhetskopior". <br/><br/> **Undantag**: Om en daglig schemalagd säkerhetskopiering som är inställd för långsiktig kvarhållning (veckor, månader eller år) misslyckas, en säkerhetskopiering på begäran som utlöses direkt efter felet övervägs för långsiktig kvarhållning. Annars övervägs nästa schemalagda säkerhetskopiering för långsiktig kvarhållning.<br/><br/> **Exempel scenario**: Den schemalagda säkerhetskopian på torsdag vid 8:00 misslyckades. Den här säkerhetskopian skulle beaktas för veckovis, månadsvis eller årlig kvarhållning. Så den första säkerhetskopieringen på begäran som utlöses före nästa schemalagda säkerhetskopiering på fredag klockan 8:00 taggas automatiskt för veckovis, månadsvis eller årlig kvarhållning. Denna backup ersätter torsdagen 08:00 backup.
| Vecka | **Standardkvarhållning**: En dag. Säkerhetskopiering på begäran som tas för en datakälla som har en veckovis säkerhetskopieringsprincip tas bort nästa dag. De tas bort även om de är de senaste säkerhetskopiorna för datakällan. <br/><br/> **Undantag**: Om en schemalagd veckovis säkerhetskopiering som är inställd för långsiktig kvarhållning (veckor, månader eller år) misslyckas, betraktas en säkerhetskopiering på begäran som utlöses direkt efter felet för långsiktig kvarhållning. Annars övervägs nästa schemalagda säkerhetskopiering för långsiktig kvarhållning. <br/><br/> **Exempel scenario**: Den schemalagda säkerhetskopian på torsdag vid 8:00 misslyckades. Den här säkerhetskopian skulle övervägas för månatlig eller årlig kvarhållning. Så den första säkerhetskopieringen på begäran som utlöses före nästa schemalagda säkerhetskopiering på torsdag klockan 8:00 är automatiskt taggad för månatlig eller årlig kvarhållning. Denna backup ersätter torsdagen 08:00 backup.

Mer information finns i [Skapa en princip för säkerhetskopiering](#create-a-backup-policy).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer filer i Azure](backup-azure-restore-windows-server.md).
* Hitta [vanliga frågor om säkerhetskopiering av filer och mappar](backup-azure-file-folder-backup-faq.md)

