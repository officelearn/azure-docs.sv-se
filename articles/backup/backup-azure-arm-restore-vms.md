---
title: Återställa virtuella datorer med hjälp av Azure Portal
description: Återställa en virtuell Azure-dator från en återställnings punkt med hjälp av Azure Portal
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 05ad6c4eab61ccf5bc0884da63ebda7ba76ac583
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597566"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Så här återställer du Azure VM-data i Azure Portal

Den här artikeln beskriver hur du återställer virtuella Azure-Datadata från återställnings punkterna som lagras i [Azure Backup](backup-overview.md) Recovery Services valv.

## <a name="restore-options"></a>Återställnings alternativ

Azure Backup erbjuder ett antal olika sätt att återställa en virtuell dator.

**Återställnings alternativ** | **Detaljer**
--- | ---
**Skapa en ny virtuell dator** | Skapar och hämtar snabbt en grundläggande virtuell dator som körs från en återställnings punkt.<br/><br/> Du kan ange ett namn på den virtuella datorn, välja resurs gruppen och det virtuella nätverk (VNet) där det ska placeras och ange ett lagrings konto för den återställda virtuella datorn. Den nya virtuella datorn måste skapas i samma region som den virtuella käll datorn.
**Återställ disk** | Återställer en virtuell dator disk, som sedan kan användas för att skapa en ny virtuell dator.<br/><br/> Azure Backup innehåller en mall som hjälper dig att anpassa och skapa en virtuell dator. <br/><br> Återställnings jobbet genererar en mall som du kan hämta och använda för att ange anpassade VM-inställningar och skapa en virtuell dator.<br/><br/> Diskarna kopieras till den resurs grupp som du anger.<br/><br/> Alternativt kan du koppla disken till en befintlig virtuell dator eller skapa en ny virtuell dator med hjälp av PowerShell.<br/><br/> Det här alternativet är användbart om du vill anpassa den virtuella datorn, lägga till konfigurations inställningar som inte fanns vid tidpunkten för säkerhets kopieringen eller lägga till inställningar som måste konfigureras med mallen eller PowerShell.
**Ersätt befintlig** | Du kan återställa en disk och använda den för att ersätta en disk på den befintliga virtuella datorn.<br/><br/> Den aktuella virtuella datorn måste finnas. Det här alternativet kan inte användas om det har tagits bort.<br/><br/> Azure Backup tar en ögonblicks bild av den befintliga virtuella datorn innan disken ersätts och lagrar den på den mellanlagringsplats som du anger. Befintliga diskar som är anslutna till den virtuella datorn ersätts med den valda återställnings punkten.<br/><br/> Ögonblicks bilden kopieras till valvet och bevaras i enlighet med bevarande principen. <br/><br/> Efter åtgärden Ersätt disk behålls den ursprungliga disken i resurs gruppen. Du kan välja att manuellt ta bort de ursprungliga diskarna om de inte behövs. <br/><br/>Ersätt befintlig stöds för okrypterade hanterade virtuella datorer. Den stöds inte för ohanterade diskar, [generaliserade virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)eller för virtuella datorer som [skapats med anpassade avbildningar](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Om återställnings punkten har fler eller färre diskar än den aktuella virtuella datorn kommer antalet diskar i återställnings punkten bara att avspegla konfigurationen för den virtuella datorn.<br><br> Ersätt befintlig stöds inte för virtuella datorer med länkade resurser (t. ex. [användardefinierad hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) eller [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)) eftersom den säkerhetskopierade klient-appen inte har behörighet till resurserna samtidigt som du utför återställningen.
**Mellan region (sekundär region)** | Återställning mellan regioner kan användas för att återställa virtuella Azure-datorer i den sekundära regionen, som är en [Azure-kopplad region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).<br><br> Du kan återställa alla virtuella Azure-datorer för den valda återställnings punkten om säkerhets kopian görs i den sekundära regionen.<br><br> Den här funktionen är tillgänglig för alternativen nedan:<br> * [skapa en virtuell dator](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [återställa diskar](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Vi stöder för närvarande inte alternativet [Ersätt befintliga diskar](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) .<br><br> Behörigheter<br> Återställnings åtgärden i den sekundära regionen kan utföras av säkerhets kopierings administratörer och app-administratörer.

> [!NOTE]
> Du kan också återställa vissa filer och mappar på en virtuell Azure-dator. [Läs mer](backup-azure-restore-files-from-vm.md).
>
> Om du kör den [senaste versionen](backup-instant-restore-capability.md) av Azure Backup för virtuella Azure-datorer (kallas omedelbar återställning), bevaras ögonblicks bilder i upp till sju dagar och du kan återställa en virtuell dator från ögonblicks bilder innan säkerhets kopierings data skickas till valvet. Om du vill återställa en virtuell dator från en säkerhets kopia från de senaste sju dagarna går det snabbare att återställa från ögonblicks bilden och inte från valvet.

## <a name="storage-accounts"></a>Lagringskonton

Information om lagrings konton:

- **Skapa virtuell dator**: när du skapar en ny virtuell dator placeras den virtuella datorn i det lagrings konto som du anger.
- **Återställ disk**: när du återställer en disk kopieras disken till det lagrings konto som du anger. Återställnings jobbet genererar en mall som du kan hämta och använda för att ange anpassade VM-inställningar. Den här mallen placeras på det angivna lagrings kontot.
- **Ersätt disk**: när du ersätter en disk på en befintlig virtuell dator tar Azure Backup en ögonblicks bild av den befintliga virtuella datorn innan disken ersätts. Ögonblicks bilden lagras på den mellanlagringsplats (lagrings konto) som du anger. Det här lagrings kontot används för att tillfälligt lagra ögonblicks bilden under återställnings processen och vi rekommenderar att du skapar ett nytt konto för att göra detta, vilket enkelt kan tas bort efteråt.
- **Lagrings konto plats**: lagrings kontot måste finnas i samma region som valvet. Endast dessa konton visas. Om det inte finns några lagrings konton på platsen måste du skapa ett.
- **Lagrings typ**: blob-lagring stöds inte.
- **Lagrings redundans**: zon redundant lagring (ZRS) stöds inte. Information om replikering och redundans för kontot visas inom parentes efter konto namnet.
- **Premium-lagring**:
  - När du återställer icke-Premium-datorer stöds inte Premium Storage-konton.
  - När du återställer hanterade virtuella datorer stöds inte Premium Storage-konton som kon figurer ATS med nätverks regler.

## <a name="before-you-start"></a>Innan du börjar

Om du vill återställa en virtuell dator (skapa en ny virtuell dator) kontrollerar du att du har rätt [behörighet](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) för rollbaserad åtkomst kontroll (RBAC) för åtgärden Återställ virtuell dator.

Om du inte har behörighet kan du [återställa en disk](#restore-disks)och sedan kan du [använda mallen](#use-templates-to-customize-a-restored-vm) som har genererats som en del av återställnings åtgärden för att skapa en ny virtuell dator när disken har återställts.

## <a name="select-a-restore-point"></a>Välj en återställnings punkt

1. I valvet som är kopplat till den virtuella dator som du vill återställa klickar du på **säkerhets kopierings objekt** > **virtuell Azure-dator**.
2. Klicka på en virtuell dator. Återställnings punkter från de senaste 30 dagarna visas som standard på VM-instrumentpanelen. Du kan visa återställnings punkter som är äldre än 30 dagar eller filtrera för att hitta återställnings punkter baserat på datum, tidsintervall och olika typer av ögonblicks bilds konsekvens.
3. Klicka på **Återställ virtuell dator**om du vill återställa den virtuella datorn.

    ![Återställnings punkt](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Välj en återställnings punkt som ska användas för återställningen.

## <a name="choose-a-vm-restore-configuration"></a>Välj en konfiguration för återställning av virtuell dator

1. I **Återställ konfiguration**väljer du ett återställnings alternativ:
    - **Skapa nytt**: Använd det här alternativet om du vill skapa en ny virtuell dator. Du kan skapa en virtuell dator med enkla inställningar eller återställa en disk och skapa en anpassad virtuell dator.
    - **Ersätt befintlig**: Använd det här alternativet om du vill ersätta diskarna på en befintlig virtuell dator.

        ![Guiden Återställ konfiguration](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Ange inställningar för det valda återställnings alternativet.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Som en av [återställnings alternativen](#restore-options)kan du snabbt skapa en virtuell dator med grundläggande inställningar från en återställnings punkt.

1. I **Återställ konfiguration** > **Skapa ny** > **återställnings typ**väljer du **skapa en virtuell dator**.
2. I **namn på virtuell dator**anger du en virtuell dator som inte finns i prenumerationen.
3. I **resurs grupp**väljer du en befintlig resurs grupp för den nya virtuella datorn eller skapar en ny med ett globalt unikt namn. Om du tilldelar ett namn som redan finns tilldelar Azure gruppen samma namn som den virtuella datorn.
4. I **virtuellt nätverk**väljer du det VNet som den virtuella datorn ska placeras i. Alla virtuella nätverk som är associerade med prenumerationen visas. Välj under nätet. Det första under nätet är valt som standard.
5. I **lagrings plats**anger du lagrings kontot för den virtuella datorn. [Läs mer](#storage-accounts).

    ![Guiden Återställ konfiguration](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. I **Återställ konfiguration**väljer du **OK**. I **återställning**klickar du på **Återställ** för att utlösa återställnings åtgärden.

## <a name="restore-disks"></a>Återställa diskar

Som ett av [återställnings alternativen](#restore-options)kan du skapa en disk från en återställnings punkt. Med disken kan du sedan göra något av följande:

- Använd mallen som genereras under återställnings åtgärden för att anpassa inställningarna och utlösa VM-distribution. Du redigerar standardinställningarna för mallar och skickar mallen för distribution av virtuella datorer.
- [Koppla återställda diskar](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) till en befintlig virtuell dator.
- [Skapa en ny virtuell dator](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) från de återställda diskarna med PowerShell.

1. I **Återställ konfiguration** > **Skapa ny** > **återställnings typ**väljer du **Återställ diskar**.
2. I **resurs grupp**väljer du en befintlig resurs grupp för de återställda diskarna eller skapar en ny med ett globalt unikt namn.
3. I **lagrings konto**anger du det konto som de virtuella hård diskarna ska kopieras till. [Läs mer](#storage-accounts).

    ![Återställnings konfigurationen slutförd](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. I **Återställ konfiguration**väljer du **OK**. I **återställning**klickar du på **Återställ** för att utlösa återställnings åtgärden.

När den virtuella datorn använder hanterade diskar och du väljer alternativet för att **skapa en virtuell dator** så använder Azure Backup inte det angivna lagrings kontot. Om du **återställer diskar** och **omedelbar återställning**, används lagrings kontot bara för att lagra mallen. Hanterade diskar skapas i den angivna resurs gruppen.
När den virtuella datorn använder ohanterade diskar återställs de som blobbar till lagrings kontot.

### <a name="use-templates-to-customize-a-restored-vm"></a>Använda mallar för att anpassa en återställd virtuell dator

När disken har återställts använder du den mall som har genererats som en del av återställnings åtgärden för att anpassa och skapa en ny virtuell dator:

1. Öppna **återställnings jobb information** för det relevanta jobbet.

2. I **information om återställnings jobb**väljer du **distribuera mall** för att initiera mall distributionen.

    ![Återställ jobbs ökning](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Klicka på **Redigera mall**om du vill anpassa den VM-inställning som anges i mallen. Om du vill lägga till fler anpassningar klickar du på **Redigera parametrar**.
    - [Lär dig mer](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) om att distribuera resurser från en anpassad mall.
    - [Lär dig mer](../azure-resource-manager/templates/template-syntax.md) om att redigera mallar.

   ![Läs in mall distribution](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Ange de anpassade värdena för den virtuella datorn, godkänn de **allmänna** villkoren och klicka på **köp**.

   ![Skicka mall distribution](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Ersätt befintliga diskar

Som en av [återställnings alternativen](#restore-options)kan du ersätta en befintlig virtuell dator disk med den valda återställnings punkten. [Granska](#restore-options) alla återställnings alternativ.

1. I **Återställ konfiguration**klickar du på **Ersätt befintlig**.
2. I **återställnings typ**väljer du **Ersätt disk/s**. Detta är den återställnings punkt som ska användas för att ersätta befintliga VM-diskar.
3. På **mellanlagringsplatsen**anger du var ögonblicks bilder av de aktuella hanterade diskarna ska sparas under återställnings processen. [Läs mer](#storage-accounts).

   ![Återställ konfigurations guiden Ersätt befintlig](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Återställning mellan regioner

Som en av [återställnings alternativen](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)kan du med återställningen mellan regioner (CRR) återställa virtuella Azure-datorer i en sekundär region, som är en Azure-kopplad region.

Om du vill publicera till funktionen under för hands versionen läser du [avsnittet innan du börjar](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Om du vill se om CRR har Aktiver ATS följer du anvisningarna i [Konfigurera återställning av kors region](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Visa säkerhets kopierings objekt i sekundär region

Om CRR har Aktiver ATS kan du Visa säkerhets kopierings objekt i den sekundära regionen.

1. Från portalen går du till **Recovery Services valv** > **säkerhets kopierings objekt**
2. Klicka på **sekundär region** om du vill visa objekten i den sekundära regionen.

![Virtuella datorer i sekundär region](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Välj sekundär region](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Återställ i sekundär region

Användar upplevelsen för sekundär regions återställning liknar den primära regionen återställa användar upplevelsen. När du konfigurerar information i bladet Återställ konfiguration för att konfigurera återställningen uppmanas du bara att ange parametrar för sekundär region.

![Välj den virtuella dator som ska återställas](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Välj återställnings punkt](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Återställ konfiguration](./media/backup-azure-arm-restore-vms/rest-config.png)

![Avisering om aktivering av återställning pågår](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Information om hur du återställer och skapar en virtuell dator finns i [skapa en virtuell dator](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm).
- Se [återställa diskar](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks)för att återställa som en disk.

>[!NOTE]
>Återställnings jobbet kan inte avbrytas när återställningen har utlösts och i data överförings fasen.

### <a name="monitoring-secondary-region-restore-jobs"></a>Övervaka återställnings jobb för sekundär region

1. Från portalen går du till **Recovery Services valv** > **säkerhets kopierings jobb**
2. Klicka på **sekundär region** om du vill visa objekten i den sekundära regionen.

![Säkerhets kopierings jobb filtrerade](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Återställa virtuella datorer med särskilda konfigurationer

Det finns ett antal vanliga scenarier där du kan behöva återställa virtuella datorer.

**Scenario** | **Riktlinjer**
--- | ---
**Återställa virtuella datorer med Hybrid Use-förmånen** | Om en virtuell Windows-dator använder [Hybrid Use Benefit (hubb)-licensiering](../virtual-machines/windows/hybrid-use-benefit-licensing.md), återställer du diskarna och skapar en ny virtuell dator med hjälp av den angivna mallen (med **licens typen** inställd på **Windows_Server**) eller PowerShell.  Den här inställningen kan också användas när du har skapat den virtuella datorn.
**Återställa virtuella datorer under en Azure Data Center-katastrof** | Om valvet använder GRS och det primära data centret för den virtuella datorn slutar fungera, kan Azure Backup återställa säkerhetskopierade virtuella datorer till det kopplade data centret. Du väljer ett lagrings konto i det kopplade data centret och återställer det som normalt. Azure Backup använder beräknings tjänsten i den kopplade regionen för att skapa den återställda virtuella datorn. [Läs mer](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) om data Center återhämtning.
**Återställa en virtuell dator med en virtuell domänkontrollant i en enda domän** | Återställ den virtuella datorn som vilken annan virtuell dator som helst. Tänk på följande:<br/><br/> Från ett Active Directory perspektiv är den virtuella Azure-datorn precis som vilken annan virtuell dator som helst.<br/><br/> Återställnings läge för katalog tjänster (DSRM) är också tillgängligt, så alla Active Directory återställnings scenarier är lönsamma. [Läs mer](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) om säkerhets kopierings-och återställnings överväganden för virtualiserade domänkontrollanter.
**Återställa flera virtuella datorer i en enda domän** | Om andra domänkontrollanter i samma domän kan nås över nätverket kan domänkontrollanten återställas på samma sätt som vilken virtuell dator som helst. Om det är den sista återstående domänkontrollanten i domänen, eller om en återställning i ett isolerat nätverk utförs, använder du en [skogs återställning](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Återställa flera domäner i en skog** | Vi rekommenderar en [skogs återställning](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Återställning utan operativ system** | Den största skillnaden mellan virtuella Azure-datorer och lokala hypervisors-paneler är att det inte finns någon VM-konsol tillgänglig i Azure. En-konsol krävs för vissa scenarier, till exempel att återställa med hjälp av en återställning utan operativ system (BMR)-typ. Återställning av virtuella datorer från valvet är dock en fullständig ersättning för BMR.
**Återställa virtuella datorer med särskilda nätverkskonfigurationer** | Särskilda nätverkskonfigurationer är virtuella datorer som använder intern eller extern belastnings utjämning, med flera nätverkskort eller flera reserverade IP-adresser. Du återställer de virtuella datorerna med [alternativet Återställ disk](#restore-disks). Det här alternativet gör en kopia av de virtuella hård diskarna till det angivna lagrings kontot och du kan sedan skapa en virtuell dator med en [intern](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) eller [extern](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) belastningsutjämnare, [flera nätverkskort](../virtual-machines/windows/multiple-nics.md)eller [flera reserverade IP-adresser](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), i enlighet med din konfiguration.
**Nätverks säkerhets grupp (NSG) på NIC/undernät** | Azure VM Backup stöder säkerhets kopiering och återställning av NSG-information på VNet, undernät och NIC-nivå.
**Zon fästa virtuella datorer** | Azure Backup stöder säkerhets kopiering och återställning av zonbaserade fästa virtuella datorer. [Läs mer](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Spåra återställnings åtgärden

När du har utlöst återställnings åtgärden skapar säkerhets kopierings tjänsten ett jobb för spårning. Azure Backup visar meddelanden om jobbet i portalen. Om de inte visas väljer du symbolen **meddelanden** och väljer sedan **Visa alla jobb** för att se status för återställnings processen.

![Återställning har Aktiver ATS](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Spåra återställning enligt följande:

1. Klicka på hyperlänken meddelanden om du vill visa åtgärder för jobbet. Alternativt klickar du på **säkerhets kopierings jobb**i valvet och klickar sedan på den aktuella virtuella datorn.

    ![Lista över virtuella datorer i ett valv](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Om du vill övervaka återställnings förloppet klickar du på återställnings jobb med statusen **pågår**. Förlopps indikatorn visas, som visar information om återställnings förloppet:

    - **Beräknad återställnings tid**: anger från början den tid som krävs för att slutföra återställnings åtgärden. När åtgärden fortskrider, minskar den tid det tar att minska och når noll när återställnings åtgärden slutförts.
    - **Procent andel av återställningen**. Visar procent andelen återställnings åtgärd som är klar.
    - **Antal överförda byte**: om du återställer genom att skapa en ny virtuell dator visas de byte som överfördes mot det totala antalet byte som ska överföras.

## <a name="post-restore-steps"></a>Steg efter återställning

Det finns ett antal saker att notera när du återställer en virtuell dator:

- Tillägg som finns under säkerhets kopierings konfigurationen installeras, men är inte aktiverade. Om du ser ett problem installerar du om tilläggen.
- Om den säkerhetskopierade virtuella datorn har en statisk IP-adress, kommer den återställda virtuella datorn ha en dynamisk IP-adress för att undvika konflikter. Du kan [lägga till en statisk IP-adress till den återställda virtuella datorn](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description).
- Det finns ingen tillgänglighets uppsättning för en återställd virtuell dator. Om du använder alternativet för att återställa diskar kan du [Ange en tillgänglighets uppsättning](../virtual-machines/windows/tutorial-availability-sets.md) när du skapar en virtuell dator från disken med hjälp av den angivna mallen eller PowerShell.
- Om du använder en Cloud-Init-baserad Linux-distribution, till exempel Ubuntu, av säkerhets skäl, blockeras lösen ordet efter återställningen. Använd tillägget VMAccess på den återställda virtuella datorn för att [återställa lösen ordet](../virtual-machines/linux/reset-password.md). Vi rekommenderar att du använder SSH-nycklar på dessa distributioner, så du behöver inte återställa lösen ordet efter återställningen.
- Om du inte kan komma åt den virtuella datorn när den har återställts på grund av att den virtuella datorn har brutit relationer med domänkontrollanten följer du stegen nedan för att öppna den virtuella datorn:
  - Koppla OS-disk som en data disk till en återställd virtuell dator.
  - Installera VM-agenten manuellt om Azure-agenten inte svarar genom att följa den här [länken](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
  - Aktivera åtkomst till seriell konsol på den virtuella datorn för att tillåta kommando rads åtkomst till den virtuella datorn

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - När den virtuella datorn återskapas använder Azure Portal för att återställa det lokala administratörs kontot och lösen ordet
  - Använda Seriell konsol Access och CMD för att koppla från den virtuella datorn från domänen

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- När den virtuella datorn har kopplats från och startats om, kommer du att kunna distribuera RDP till den virtuella datorn med autentiseringsuppgifter för lokal administratör och återansluta den till domänen.

## <a name="backing-up-restored-vms"></a>Säkerhetskopiera återställda virtuella datorer

- Om du har återställt en virtuell dator till samma resurs grupp med samma namn som den ursprungligen säkerhetskopierade virtuella datorn fortsätter säkerhets kopieringen på den virtuella datorn efter återställningen.
- Om du har återställt den virtuella datorn till en annan resurs grupp eller om du angav ett annat namn för den återställda virtuella datorn måste du konfigurera säkerhets kopiering för den återställda virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Om du får problem under återställnings processen kan du [läsa](backup-azure-vms-troubleshoot.md#restore) vanliga problem och fel.
- När den virtuella datorn har återställts kan du läsa mer om att [Hantera virtuella datorer](backup-azure-manage-vms.md)
