---
title: Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services-valv
description: Beskriver hur du säkerhetskopierar virtuella Azure-datorer i ett Recovery Services-valv med hjälp av Azure Backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273519"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Säkerhetskopiera virtuella Azure-datorer i ett Recovery Services-valv

I den här artikeln beskrivs hur du säkerhetskopierar virtuella Azure-datorer i ett Recovery Services-valv med hjälp av [Azure Backup-tjänsten.](backup-overview.md)

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Förbered virtuella Azure-datorer.
> * Skapa ett valv.
> * Identifiera virtuella datorer och konfigurera en princip för säkerhetskopiering.
> * Aktivera säkerhetskopiering för virtuella Azure-datorer.
> * Kör den första säkerhetskopieringen.

> [!NOTE]
> I den här artikeln beskrivs hur du konfigurerar ett valv och väljer virtuella datorer som ska säkerhetskopieras. Det är användbart om du vill säkerhetskopiera flera virtuella datorer. Alternativt kan du [säkerhetskopiera en enda Azure-virtuell dator](backup-azure-vms-first-look-arm.md) direkt från vm-inställningarna.

## <a name="before-you-start"></a>Innan du börjar

* [Granska](backup-architecture.md#architecture-built-in-azure-vm-backup) azure VM-säkerhetskopieringsarkitekturen.
* [Läs mer](backup-azure-vms-introduction.md) Azure VM-säkerhetskopiering och tillägget för säkerhetskopiering.
* [Granska supportmatrisen](backup-support-matrix-iaas.md) innan du konfigurerar säkerhetskopiering.

Dessutom finns det ett par saker som du kan behöva göra under vissa omständigheter:

* **Installera VM-agenten på den virtuella datorn:** Azure Backup säkerhetskopierar Virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om din virtuella dator skapades från en Azure marketplace-avbildning installeras och körs agenten. Om du skapar en anpassad virtuell dator, eller om du migrerar en lokal dator, kan du behöva [installera agenten manuellt](#install-the-vm-agent).

## <a name="create-a-vault"></a>Skapa ett valv

 Ett valv lagrar säkerhetskopior och återställningspunkter som skapats med tiden och lagrar principer för säkerhetskopiering som är associerade med säkerhetskopierade datorer. Skapa ett valv enligt följande:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Skriv **Återställningstjänster**i sökningen . Klicka på **Recovery Services-valv**under **Tjänster**.

     ![Sök efter Recovery Services-valv](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. Klicka på **+Lägg till**på Recovery **Services-valv-menyn.**

     ![Skapa Recovery Services-valv (steg 2)](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. I **Valvet för Återställningstjänster**skriver du in ett eget namn för att identifiera valvet.
    * Namnet måste vara unikt för Azure-prenumerationen.
    * Den kan innehålla 2 till 50 tecken.
    * Den måste börja med en bokstav och den kan bara innehålla bokstäver, siffror och bindestreck.
5. Välj den Azure-prenumeration, resursgrupp och geografiska region där valvet ska skapas. Klicka sedan på **Skapa**.
    * Det kan ta ett tag innan valvet skapas.
    * Övervaka statusmeddelanden i det övre högra området i portalen.

När valvet har skapats visas det i listan Återställningstjänster-valv. Om du inte ser valvet väljer du **Uppdatera**.

![Lista över säkerhetskopieringsvalv](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Azure Backup tillåter nu anpassning av resursgruppnamnet som skapats av Azure Backup-tjänsten. Mer information finns i [Azure Backup-resursgrupp för virtuella datorer](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

### <a name="modify-storage-replication"></a>Ändra lagringsreplikering

Som standard använder valv [geo-redundant lagring (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Om valvet är din primära säkerhetskopieringsmekanism rekommenderar vi att du använder GRS.
* Du kan använda [lokalt redundant lagring (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för ett billigare alternativ.

Ändra lagringsreplikeringstyp enligt följande:

1. Klicka på **Egenskaper** i avsnittet **Inställningar** i det nya valvet.
2. Klicka på **Uppdatera**under **Säkerhetskopieringskonfiguration**i **Egenskaper**.
3. Markera lagringsreplikeringstypen och klicka på **Spara**.

      ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Du kan inte ändra lagringsreplikeringstypen när valvet har konfigurerats och innehåller säkerhetskopior. Om du vill göra detta måste du återskapa valvet.

## <a name="apply-a-backup-policy"></a>Tillämpa en princip för säkerhetskopiering

Konfigurera en principer för säkerhetskopiering för valvet.

1. Klicka på **+Säkerhetskopiering** i avsnittet **Översikt** i valvet.

   ![Knappen Säkerhetskopiering](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. I **Säkerhetskopieringsmål** > Var körs **Azure****din arbetsbelastning?** I **Vad vill du säkerhetskopiera?** **Virtual machine** >  **OK** Detta registrerar vm-tillägget i valvet.

   ![Fönsterrutor för säkerhetskopiering och säkerhetskopieringsmål](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Välj den princip som du vill associera med valvet i **principen Säkerhetskopiering.**
    * Standardprincipen säkerhetskopierar den virtuella datorn en gång om dagen. De dagliga säkerhetskopiorna behålls i 30 dagar. Ögonblicksbilder av omedelbar återställning behålls i två dagar.
    * Om du inte vill använda standardprincipen väljer du **Skapa ny**och skapar en anpassad princip enligt beskrivningen i nästa procedur.

      ![Standardprincip för säkerhetskopiering](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. I **Välj virtuella datorer**väljer du de virtuella datorer som du vill säkerhetskopiera med hjälp av principen. Klicka sedan på **OK**.

   * De valda virtuella datorerna valideras.
   * Du kan bara välja virtuella datorer i samma region som valvet.
   * Virtuella datorer kan bara säkerhetskopieras i ett enda valv.

     ![Fönstret "Välj virtuella datorer"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Klicka på **Aktivera säkerhetskopiering**i **Säkerhetskopiering**. Detta distribuerar principen till valvet och till de virtuella datorerna och installerar säkerhetskopieringstillägget på VM-agenten som körs på den virtuella Azure-datorn.

     ![Knappen "Aktivera säkerhetskopiering"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

När du har aktiverat säkerhetskopiering:

* Tjänsten Säkerhetskopiering installerar säkerhetskopieringstillägget oavsett om den virtuella datorn körs eller inte.
* En första säkerhetskopia körs i enlighet med ditt säkerhetskopieringsschema.
* När säkerhetskopieringar körs bör du tänka på följande:
  * En virtuell dator som körs har störst chans att fånga en programkonsekvent återställningspunkt.
  * Men även om den virtuella datorn är avstängd är den säkerhetskopierad. En sådan virtuell dator kallas en virtuell offline-virtuell dator. I det här fallet kommer återställningspunkten att vara kraschkonsekvent.
* Explicit utgående anslutning krävs inte för att tillåta säkerhetskopiering av virtuella Azure-datorer.

### <a name="create-a-custom-policy"></a>Skapa en anpassad princip

Om du har valt att skapa en ny princip för säkerhetskopiering fyller du i principinställningarna.

1. Ange ett meningsfullt namn i **principnamn.**
2. I **säkerhetskopieringsschema**anger du när säkerhetskopieringar ska göras. Du kan göra dagliga eller veckovisa säkerhetskopior för virtuella Azure-datorer.
3. I **Omedelbar återställning**anger du hur länge du vill behålla ögonblicksbilder lokalt för omedelbar återställning.
    * När du återställer kopieras säkerhetskopierade VM-diskar från lagring, över nätverket till återställningslagringsplatsen. Med omedelbar återställning kan du utnyttja lokalt lagrade ögonblicksbilder som tagits under ett säkerhetskopieringsjobb, utan att vänta på att säkerhetskopierade data ska överföras till valvet.
    * Du kan behålla ögonblicksbilder för omedelbar återställning i mellan en till fem dagar. Två dagar är standardinställningen.
4. I **Kvarhållningsintervall anger**du hur länge du vill behålla dina dagliga eller veckovisa säkerhetskopieringspunkter.
5. I **Kvarhållning av månatliga säkerhetskopieringspunkt**anger du om du vill behålla en månatlig säkerhetskopiering av dina dagliga eller veckovisa säkerhetskopior.
6. Spara principen genom att klicka på **OK.**

    ![Ny princip för säkerhetskopiering](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup stöder inte automatisk klockjustering för sommartidsändringar för Azure VM-säkerhetskopior. När tidsändringar inträffar ändrar du principer för säkerhetskopiering manuellt efter behov.

## <a name="trigger-the-initial-backup"></a>Utlösa den första säkerhetskopian

Den första säkerhetskopian körs i enlighet med schemat, men du kan köra den omedelbart enligt följande:

1. Klicka på **Säkerhetsobjekt**på arkivmenyn.
2. Klicka på **Azure Virtual Machine i** **säkerhetskopieringsobjekt**.
3. Klicka på ellipserna (...) i listan **Säkerhetsobjekt.**
4. Klicka på **Säkerhetskopiering nu**.
5. I **Säkerhetskopiering nu**använder du kalenderkontrollen för att välja den sista dagen då återställningspunkten ska behållas. Klicka sedan på **OK**.
6. Övervaka portalmeddelandena. Du kan övervaka jobbframsteget i instrumentpanelen för valvet >**pågående** **säkerhetskopieringsjobb** > . Beroende på den virtuella datorns storlek kan det ta en stund att skapa den första säkerhetskopian.

## <a name="verify-backup-job-status"></a>Verifiera jobbstatus för säkerhetskopiering

Jobbinformationen för säkerhetskopiering för varje vm-säkerhetskopiering består av två faser, **ögonblicksbildfasen** följt av **överföringsdata till valvfasen.**<br/>
Ögonblicksbildfasen garanterar tillgängligheten för en återställningspunkt som lagras tillsammans med diskarna för **omedelbar återställning** och är tillgängliga i högst fem dagar beroende på den ögonblicksbildkvarhållning som konfigurerats av användaren. Överföringsdata till valvet skapar en återställningspunkt i valvet för långsiktig kvarhållning. Överföringsdata till valvet startar först när ögonblicksbildfasen har slutförts.

  ![Status för säkerhetskopieringsjobb](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Det finns två **underaktiviteter** som körs i serverdelen, en för säkerhetskopieringsjobb för frontend som kan kontrolleras från informationsbladet **Säkerhetskopieringsjobb** enligt nedan:

  ![Status för säkerhetskopieringsjobb](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

**Överföringsdata till valvfasen** kan ta flera dagar att slutföra beroende på diskarnas storlek, omsättning per disk och flera andra faktorer.

Jobbstatus kan variera beroende på följande scenarier:

**Ögonblicksbild** | **Överföra data till valv** | **Jobbstatus**
--- | --- | ---
Slutfört | Pågår | Pågår
Slutfört | Överhoppad | Slutfört
Slutfört | Slutfört | Slutfört
Slutfört | Misslyckades | Kompletterat med varning
Misslyckades | Misslyckades | Misslyckades

Nu med den här funktionen, för samma virtuella dator, två säkerhetskopior kan köras parallellt, men i båda faserna (ögonblicksbild, överföra data till valv) bara en underuppgift kan köras. Så i scenarier var en backup jobb pågående resulterade i nästa dags säkerhetskopiering att misslyckas kommer att undvikas med denna frikoppling funktionalitet. Efterföljande dags säkerhetskopior kan ha ögonblicksbilden slutförd medan **överföringsdata till valv** hoppas över om en tidigare dags säkerhetskopieringsjobb är pågående.
Den inkrementella återställningspunkten som skapas i valvet fångar all omsättning från den senaste återställningspunkten som skapades i valvet. Det finns ingen kostnadspåverkan på användaren.

## <a name="optional-steps"></a>Valfria steg

### <a name="install-the-vm-agent"></a>Installera VM-agenten

Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Om din virtuella dator skapades från en Azure Marketplace-avbildning installeras och körs agenten. Om du skapar en anpassad virtuell dator, eller om du migrerar en lokal dator, kan du behöva installera agenten manuellt, enligt summeringen i tabellen.

**VM** | **Detaljer**
--- | ---
**Windows** | 1. [Ladda ner och installera](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) agentEN MSI-filen.<br/><br/> 2. Installera med administratörsbehörighet på datorn.<br/><br/> 3. Kontrollera installationen. Högerklicka på**Egenskaper**för **WaAppAgent.exe** > i *C:\WindowsAzure\Packages* på den virtuella datorn . På fliken **Detaljer** bör **produktversionen** vara 2.6.1198.718 eller högre.<br/><br/> Om du uppdaterar agenten kontrollerar du att inga säkerhetskopieringsåtgärder körs och [installerar om agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Installera med hjälp av ett RPM- eller DEB-paket från distributionens paketarkiv. Detta är den metod som föredras för att installera och uppgradera Azure Linux-agenten. Alla [godkända distributionsleverantörer](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integrerar Azure Linux-agentpaketet i sina avbildningar och databaser. Agenten är tillgänglig på [GitHub](https://github.com/Azure/WALinuxAgent), men vi rekommenderar inte att du installerar därifrån.<br/><br/> Om du uppdaterar agenten kontrollerar du att inga säkerhetskopieringsåtgärder körs och uppdaterar binärfilerna.

>[!NOTE]
> **Azure Backup stöder nu selektiv säkerhetskopiering och återställning av disk med hjälp av azure virtual machine-säkerhetskopieringslösningen.**
>
>Idag stöder Azure Backup säkerhetskopiering av alla diskar (operativsystem och data) i en virtuell dator tillsammans med hjälp av lösningen för säkerhetskopiering av virtuella datorer. Med exkluderingsdiskfunktioner får du möjlighet att säkerhetskopiera en eller några från de många datadiskarna i en virtuell dator. Detta ger en effektiv och kostnadseffektiv lösning för dina säkerhetskopierings- och återställningsbehov. Varje återställningspunkt innehåller data för diskarna som ingår i säkerhetskopieringen, vilket gör att du ytterligare kan få en delmängd diskar återställda från den angivna återställningspunkten under återställningen. Detta gäller för att återställa både från ögonblicksbilden och valvet.
>
>**För att registrera dig för förhandsgranskningen, skriv till oss påAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>Nästa steg

* Felsöka eventuella problem med [Azure VM-agenter](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) eller [Azure VM-säkerhetskopiering](backup-azure-vms-troubleshoot.md).
* [Återställ](backup-azure-arm-restore-vms.md) Virtuella Azure-datorer.
