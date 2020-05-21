---
title: Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services valv
description: Beskriver hur du säkerhetskopierar virtuella Azure-datorer i ett Recovery Services valv med hjälp av Azure Backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: cba042efb08f121d4cd9fa5693edd69c827f1465
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727020"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services valv

Den här artikeln beskriver hur du säkerhetskopierar virtuella Azure-datorer i ett Recovery Services valv med hjälp av tjänsten [Azure Backup](backup-overview.md) .

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Förbered virtuella Azure-datorer.
> * Skapa ett valv.
> * Identifiera virtuella datorer och konfigurera en säkerhets kopierings princip.
> * Aktivera säkerhets kopiering för virtuella Azure-datorer.
> * Kör den första säkerhetskopieringen.

> [!NOTE]
> Den här artikeln beskriver hur du konfigurerar ett valv och väljer virtuella datorer som ska säkerhets kopie ras. Det är användbart om du vill säkerhetskopiera flera virtuella datorer. Alternativt kan du [säkerhetskopiera en enskild virtuell Azure-dator](backup-azure-vms-first-look-arm.md) direkt från VM-inställningarna.

## <a name="before-you-start"></a>Innan du börjar

* [Granska](backup-architecture.md#architecture-built-in-azure-vm-backup) arkitekturen för säkerhets kopiering av virtuella Azure-datorer.
* [Läs mer om](backup-azure-vms-introduction.md) Säkerhets kopiering av virtuella Azure-datorer och tillägget för säkerhets kopiering.
* [Granska support mat ris](backup-support-matrix-iaas.md) innan du konfigurerar säkerhets kopiering.

Dessutom finns det några saker som du kan behöva göra i vissa fall:

* **Installera VM-agenten på den virtuella datorn**: Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om den virtuella datorn skapades från en Azure Marketplace-avbildning installeras och körs agenten. Om du skapar en anpassad virtuell dator, eller om du migrerar en lokal dator, kan du behöva [Installera agenten manuellt](#install-the-vm-agent).

## <a name="create-a-vault"></a>Skapa ett valv

 Ett valv lagrar säkerhets kopior och återställnings punkter som skapats med tiden och lagrar säkerhets kopierings principer som är associerade med säkerhetskopierade datorer. Skapa ett valv på följande sätt:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. I Sök skriver du **Recovery Services**. Under **tjänster**klickar du på **Recovery Services valv**.

     ![Sök efter Recovery Services valv](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. I menyn **Recovery Services valv** klickar du på **+ Lägg till**.

     ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. I **Recovery Services valv**skriver du ett eget namn för att identifiera valvet.
    * Namnet måste vara unikt för Azure-prenumerationen.
    * Det kan innehålla 2 till 50 tecken.
    * Det måste börja med en bokstav och får bara innehålla bokstäver, siffror och bindestreck.
5. Välj den Azure-prenumeration, resurs grupp och geografiska region där valvet ska skapas. Klicka sedan på **skapa**.
    * Det kan ta en stund innan valvet har skapats.
    * Övervaka status meddelanden i det övre högra avsnittet i portalen.

När valvet har skapats visas det i listan Recovery Services valv. Om du inte ser ditt valv väljer du **Uppdatera**.

![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Med Azure Backup kan du nu anpassa resurs gruppens namn som skapats av tjänsten Azure Backup. Mer information finns i [Azure Backup resurs grupp för Virtual Machines](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Ändra Storage Replication

Som standard använder valven [Geo-redundant lagring (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Om valvet är din primära mekanism för säkerhets kopiering rekommenderar vi att du använder GRS.
* Du kan använda [LRS (lokalt redundant lagring)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för ett billigare alternativ.

Ändra typ av lagringsreplik-replikering enligt följande:

1. Klicka på **Egenskaper** i avsnittet **Inställningar** i det nya valvet.
2. I **Egenskaper**, under **säkerhets kopierings konfiguration**, klickar du på **Uppdatera**.
3. Välj typ av lagrings replikering och klicka på **Spara**.

      ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Du kan inte ändra typen av lagringsprovider när valvet har kon figurer ATS och innehåller säkerhets kopierings objekt. Om du vill göra det måste du återskapa valvet.

## <a name="apply-a-backup-policy"></a>Tillämpa en princip för säkerhets kopiering

Konfigurera en säkerhets kopierings policy för valvet.

1. Klicka på **+ säkerhetskopiera** i avsnittet **Översikt** i valvet.

   ![Säkerhets kopierings knapp](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. I **säkerhets kopierings mål**  >  **var din arbets belastning körs? väljer du** **Azure**. I **vad vill du säkerhetskopiera?** Välj **virtuell dator**  >   **OK**. Detta registrerar VM-tillägget i valvet.

   ![Säkerhetskopiera och säkerhetskopiera mål fönster](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. I **säkerhets kopierings princip**väljer du den princip som du vill associera med valvet.
    * Standard principen säkerhetskopierar den virtuella datorn en gång om dagen. De dagliga säkerhets kopiorna behålls i 30 dagar. Ögonblicks bilder av snabb återställning sparas i två dagar.
    * Om du inte vill använda standard principen väljer du **Skapa ny**och skapar en anpassad princip enligt beskrivningen i nästa procedur.

      ![Standard princip för säkerhets kopiering](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. I **Välj virtuella datorer**väljer du de virtuella datorer som du vill säkerhetskopiera med hjälp av principen. Klicka sedan på **OK**.

   * De valda virtuella datorerna verifieras.
   * Du kan bara välja virtuella datorer i samma region som valvet.
   * Virtuella datorer kan bara säkerhets kopie ras i ett enda valv.

     ![Fönstret Välj virtuella datorer](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    >[!NOTE]
    > Endast virtuella datorer i samma region och prenumeration som för valvet kommer att vara tillgängliga för att konfigurera säkerhets kopiering.

5. I **säkerhets kopiering**klickar du på **Aktivera säkerhets kopiering**. Detta distribuerar principen till valvet och till de virtuella datorerna och installerar säkerhets kopierings tillägget på VM-agenten som körs på den virtuella Azure-datorn.

     ![Knappen Aktivera säkerhets kopiering](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

När du har aktiverat säkerhets kopiering:

* Säkerhets kopierings tjänsten installerar säkerhets kopierings tillägget oavsett om den virtuella datorn körs eller inte.
* En första säkerhets kopiering kommer att köras enligt ditt schema för säkerhets kopiering.
* Tänk på följande när säkerhets kopieringarna körs:
  * En virtuell dator som kör har störst chans att fånga en programkonsekvent återställnings punkt.
  * Men även om den virtuella datorn är avstängd, säkerhets kopie ras. En sådan virtuell dator kallas för en virtuell dator som är offline. I det här fallet är återställnings punkten krasch-konsekvent.
* Explicit utgående anslutning krävs inte för att tillåta säkerhets kopiering av virtuella Azure-datorer.

### <a name="create-a-custom-policy"></a>Skapa en anpassad princip

Om du har valt att skapa en ny säkerhets kopierings princip, fyller du i princip inställningarna.

1. I **princip namn**anger du ett beskrivande namn.
2. I **schema för säkerhets kopiering**anger du när säkerhets kopior ska vidtas. Du kan utföra dagliga eller veckovis säkerhets kopieringar för virtuella Azure-datorer.
3. I **omedelbar återställning**anger du hur länge du vill behålla ögonblicks bilder lokalt för omedelbar återställning.
    * När du återställer kopieras de säkerhetskopierade virtuella dator diskarna från Storage, över nätverket till återställnings lagrings platsen. Med omedelbar återställning kan du utnyttja lokalt lagrade ögonblicks bilder som tas under ett säkerhets kopierings jobb utan att vänta på att säkerhets kopierings data överförs till valvet.
    * Du kan behålla ögonblicks bilder för omedelbar återställning mellan en och fem dagar. Standardvärdet är två dagar.
4. I **kvarhållningsintervall**anger du hur länge du vill behålla dina dagliga eller vecko Visa säkerhets kopierings punkter.
5. I **kvarhållning av månatlig säkerhets kopierings punkt**anger du om du vill behålla en månatlig säkerhets kopia av dina dagliga eller vecko Visa säkerhets kopior.
6. Spara principen genom att klicka på **OK** .

    ![Ny säkerhets kopierings princip](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup stöder inte automatisk klock justering för sommar tids ändringar för virtuella Azure-säkerhetskopieringar. När tids ändringarna inträffar ändrar du säkerhets kopierings principerna manuellt vid behov.

## <a name="trigger-the-initial-backup"></a>Utlös den första säkerhets kopieringen

Den första säkerhets kopieringen kommer att köras enligt schemat, men du kan köra den direkt på följande sätt:

1. I menyn valv klickar du på **säkerhets kopierings objekt**.
2. I **säkerhets kopierings objekt**klickar du på **virtuell Azure-dator**.
3. I listan **säkerhets kopierings objekt** klickar du på ellipserna (...).
4. Klicka på **Säkerhetskopiera nu**.
5. I **Säkerhetskopiera nu**använder du kalender kontrollen för att välja den sista dagen som återställnings punkten ska behållas. Klicka sedan på **OK**.
6. Övervaka Portal meddelanden. Du kan övervaka jobb förloppet i valv instrument panelen > **säkerhets kopierings jobb**  >  **pågår**. Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

## <a name="verify-backup-job-status"></a>Verifiera status för säkerhets kopierings jobb

Informationen om säkerhets kopierings jobbet för varje VM-säkerhetskopiering består av två faser, **ögonblicks bilds** fasen följt av **överförings data till valv** fasen.<br/>
Ögonblicks bild fasen garanterar tillgängligheten för en återställnings punkt som lagras tillsammans med diskarna för **omedelbara** återställningar och är tillgängliga i högst fem dagar beroende på den kvarhållna ögonblicks bilder som kon figurer ATS av användaren. Överföring av data till valvet skapar en återställnings punkt i valvet för långsiktig kvarhållning. Överföring av data till valv startar bara när ögonblicks bild fasen har slutförts.

  ![Status för säkerhets kopierings jobb](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Det finns två **under aktiviteter** som körs på Server delen, en för säkerhets kopierings jobb på klient sidan som kan kontrol leras från bladet **säkerhets kopierings jobb** enligt vad som anges nedan:

  ![Status för säkerhets kopierings jobb](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

Det kan ta flera dagar innan **överförings data till valv** fasen slutförs beroende på storleken på diskarna, omsättningen per disk och flera andra faktorer.

Jobbets status kan variera beroende på följande scenarier:

**Ögonblicksbild** | **Överför data till valv** | **Jobb status**
--- | --- | ---
Slutförd | Pågår | Pågår
Slutförd | Överhoppad | Slutförd
Slutförd | Slutförd | Slutförd
Slutförd | Misslyckades | Slutfört med varning
Misslyckades | Misslyckades | Misslyckades

Med den här funktionen kan två säkerhets kopior för samma virtuella dator köras parallellt, men i båda skedet (ögonblicks bilder, överför data till valv) kan endast en under aktivitet köras. I situationer då en pågående säkerhets kopiering resulterade i att säkerhets kopieringen till nästa dag inte kunde köras, undviks den här kopplings funktionen. Efterföljande dagars säkerhets kopieringar kan ha en ögonblicks bild som slutförts medan **överföring av data till valvet** hoppades över om en tidigare säkerhets kopierings jobb pågår.
Den stegvisa återställnings punkten som skapades i valvet fångar upp omsättningen från den senaste återställnings punkten som skapades i valvet. Det kostar inget att påverka användaren.

## <a name="optional-steps"></a>Valfria steg

### <a name="install-the-vm-agent"></a>Installera VM-agenten

Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om den virtuella datorn skapades från en Azure Marketplace-avbildning installeras och körs agenten. Om du skapar en anpassad virtuell dator, eller om du migrerar en lokal dator, kan du behöva installera agenten manuellt, som sammanfattas i tabellen.

**DATORN** | **Information**
--- | ---
**Windows** | 1. [Ladda ned och installera](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) agent-MSI-filen.<br/><br/> 2. Installera med administratörs behörighet på datorn.<br/><br/> 3. kontrol lera installationen. I *C:\WindowsAzure\Packages* på den virtuella datorn högerklickar du på Egenskaper för **WaAppAgent. exe**  >  **Properties**. **Produkt versionen** bör vara 2.6.1198.718 eller högre på fliken **information** .<br/><br/> Om du uppdaterar agenten ser du till att inga säkerhets kopierings åtgärder körs och [installerar om agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installera med hjälp av ett RPM-eller DEB-paket från distributionens paket lagrings plats. Detta är den bästa metoden för att installera och uppgradera Azure Linux-agenten. Alla godkända [distributions leverantörer](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrerar Azure Linux Agent-paketet i sina avbildningar och databaser. Agenten är tillgänglig på [GitHub](https://github.com/Azure/WALinuxAgent), men vi rekommenderar inte att du installerar därifrån.<br/><br/> Om du uppdaterar agenten ska du kontrol lera att inga säkerhets kopierings åtgärder körs och uppdatera binärfilerna.

>[!NOTE]
> **Azure Backup har nu stöd för säkerhets kopiering och återställning av selektiva diskar med den virtuella Azure-datorn säkerhets kopierings lösning.**
>
>Idag har Azure Backup stöd för säkerhets kopiering av alla diskar (operativ system och data) i en virtuell dator tillsammans med säkerhets kopierings lösningen för virtuella datorer. Med funktionen exkludera disk får du ett alternativ för att säkerhetskopiera ett eller flera av de många data diskarna i en virtuell dator. Detta ger en effektiv och kostnads effektiv lösning för dina säkerhets kopierings-och återställnings behov. Varje återställnings punkt innehåller data för de diskar som ingår i säkerhets kopieringen, vilket gör att du kan få en del av diskarna återställd från den aktuella återställnings punkten under återställnings åtgärden. Detta gäller för återställning av båda från ögonblicks bilden och valvet.
>
>**Registrera dig för för hands versionen genom att skriva till oss påAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Nästa steg

* Felsök eventuella problem med [Azure VM-agenter](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) eller [Azure VM-säkerhetskopiering](backup-azure-vms-troubleshoot.md).
* [Återställ](backup-azure-arm-restore-vms.md) Virtuella Azure-datorer.
