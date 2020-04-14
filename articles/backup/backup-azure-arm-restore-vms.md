---
title: Återställa virtuella datorer med hjälp av Azure-portalen
description: Återställa en virtuell Azure-dator från en återställningspunkt med hjälp av Azure-portalen
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: b9cdb187aa3b8750bead8e81ad6d0ee50dcb3d6c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254928"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Återställa Azure VM-data i Azure Portal

I den här artikeln beskrivs hur du återställer Azure VM-data från återställningspunkter som lagras i [Azure Backup](backup-overview.md) Recovery Services-valv.

## <a name="restore-options"></a>Återställningsalternativ

Azure Backup innehåller ett antal sätt att återställa en virtuell dator.

**Alternativet Återställ** | **Detaljer**
--- | ---
**Skapa en ny virtuell dator** | Skapar och får snabbt igång en grundläggande virtuell dator från en återställningspunkt.<br/><br/> Du kan ange ett namn för den virtuella datorn, välja resursgruppen och det virtuella nätverket (VNet) där den ska placeras och ange ett lagringskonto för den återställda virtuella datorn. Den nya virtuella datorn måste skapas i samma region som källdatorn.
**Återställ disk** | Återställer en VM-disk som sedan kan användas för att skapa en ny virtuell dator.<br/><br/> Azure Backup innehåller en mall som hjälper dig att anpassa och skapa en virtuell dator. <br/><br> Återställningsjobbet genererar en mall som du kan hämta och använda för att ange anpassade vm-inställningar och skapa en virtuell dator.<br/><br/> Diskarna kopieras till den resursgrupp som du anger.<br/><br/> Du kan också koppla disken till en befintlig virtuell dator eller skapa en ny virtuell dator med PowerShell.<br/><br/> Det här alternativet är användbart om du vill anpassa den virtuella datorn, lägga till konfigurationsinställningar som inte fanns där vid säkerhetskopieringen eller lägga till inställningar som måste konfigureras med mallen eller PowerShell.
**Ersätt befintlig** | Du kan återställa en disk och använda den för att ersätta en disk på den befintliga virtuella datorn.<br/><br/> Den aktuella virtuella datorn måste finnas. Om det har tagits bort kan det här alternativet inte användas.<br/><br/> Azure Backup tar en ögonblicksbild av den befintliga virtuella datorn innan disken ersätts och lagrar den på den mellanlagringsplats du anger. Befintliga diskar som är anslutna till den virtuella datorn ersätts med den valda återställningspunkten.<br/><br/> Ögonblicksbilden kopieras till valvet och behålls i enlighet med bevarandeprincipen. <br/><br/> Efter åtgärden ersätt disk behålls den ursprungliga disken i resursgruppen. Du kan välja att manuellt ta bort de ursprungliga diskarna om de inte behövs. <br/><br/>Ersätt befintliga stöds för okrypterade hanterade virtuella datorer, inklusive virtuella datorer [som skapats med anpassade avbildningar](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Det stöds inte för klassiska virtuella datorer.<br/><br/> Om återställningspunkten har mer eller mindre diskar än den aktuella virtuella datorn, kommer antalet diskar i återställningspunkten bara att återspegla vm-konfigurationen.<br><br> Ersätta befintliga stöds inte för virtuella datorer med länkade resurser (till [exempel användartilldelade hanterade identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) eller Key [Vault)](https://docs.microsoft.com/azure/key-vault/key-vault-overview)eftersom klientappen för säkerhetskopiering inte har behörighet för dessa resurser när återställningen utförs.
**Korsregion (sekundär region)** | Återställning mellan regioner kan användas för att återställa virtuella Azure-datorer i den sekundära regionen, som är en [Azure-kopplad region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).<br><br> Du kan återställa alla virtuella Azure-datorer för den valda återställningspunkten om säkerhetskopieringen görs i den sekundära regionen.<br><br> Den här funktionen är tillgänglig för alternativen nedan:<br> * [Skapa en virtuell dator](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Återställa diskar](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Vi stöder för närvarande inte alternativet [Ersätt befintliga diskar.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Behörigheter<br> Återställningsåtgärden på sekundär region kan utföras av administratörer för säkerhetskopiering och appadministratörer.

> [!NOTE]
> Du kan också återställa specifika filer och mappar på en Virtuell Azure.You can also recover specific files and folders on an Azure VM. [Läs mer](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Lagringskonton

Några detaljer om lagringskonton:

- **Skapa virtuell dator:** När du skapar en ny virtuell dator placeras den virtuella datorn i det lagringskonto som du anger.
- **Återställningsdisk:** När du återställer en disk kopieras disken till det lagringskonto som du anger. Återställningsjobbet genererar en mall som du kan hämta och använda för att ange anpassade vm-inställningar. Den här mallen placeras i det angivna lagringskontot.
- **Ersätt disk:** När du byter ut en disk på en befintlig virtuell dator tar Azure Backup en ögonblicksbild av den befintliga virtuella datorn innan disken ersätts. Ögonblicksbilden lagras på den mellanlagringsplats (lagringskonto) som du anger. Det här lagringskontot används för att tillfälligt lagra ögonblicksbilden under återställningsprocessen, och vi rekommenderar att du skapar ett nytt konto för att göra detta, som enkelt kan tas bort efteråt.
- **Lagringskontoplats**: Lagringskontot måste finnas i samma region som valvet. Endast dessa konton visas. Om det inte finns några lagringskonton på platsen måste du skapa ett.
- **Lagringstyp:** Blob-lagring stöds inte.
- **Lagringsredundans:** Zon redundant lagring (ZRS) stöds inte. Replikerings- och redundansinformationen för kontot visas inom parentes efter kontonamnet.
- **Premium-lagring:**
  - När du återställer virtuella datorer som inte är premium stöds inte premiumlagringskonton.
  - När du återställer hanterade virtuella datorer stöds inte premiumlagringskonton som konfigurerats med nätverksregler.

## <a name="before-you-start"></a>Innan du börjar

Om du vill återställa en virtuell dator (skapa en ny virtuell dator) kontrollerar du att du har rätt rollbaserade åtkomstkontroll (RBAC) [behörigheter](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) för åtgärden Återställ virtuell dator.

Om du inte har behörighet kan du [återställa en disk](#restore-disks)och sedan när disken har återställts kan du använda [mallen](#use-templates-to-customize-a-restored-vm) som genererades som en del av återställningsåtgärden för att skapa en ny virtuell dator.

## <a name="select-a-restore-point"></a>Välj en återställningspunkt

1. I valvet som är associerat med den virtuella datorn som du vill återställa klickar du på **Säkerhetskopieringsobjekt** > **Azure Virtual Machine**.
2. Klicka på en virtuell dator. Som standard på instrumentpanelen för den virtuella datorn visas återställningspunkter från de senaste 30 dagarna. Du kan visa återställningspunkter som är äldre än 30 dagar eller filtrera för att hitta återställningspunkter baserat på datum, tidsintervall och olika typer av ögonblicksbildkonsekvens.
3. Om du vill återställa den virtuella datorn klickar du på **Återställ virtuell dator**.

    ![Återställningspunkt](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Välj en återställningspunkt som ska användas för återställningen.

## <a name="choose-a-vm-restore-configuration"></a>Välj en vm-återställningskonfiguration

1. Välj ett återställningsalternativ i **Återställningskonfiguration:**
    - **Skapa nya**: Använd det här alternativet om du vill skapa en ny virtuell dator. Du kan skapa en virtuell dator med enkla inställningar eller återställa en disk och skapa en anpassad virtuell dator.
    - **Ersätt befintliga**: Använd det här alternativet om du vill ersätta diskar på en befintlig virtuell dator.

        ![Guiden Återställ konfiguration](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Ange inställningar för det valda återställningsalternativet.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Som ett av [återställningsalternativen](#restore-options)kan du snabbt skapa en virtuell dator med grundläggande inställningar från en återställningspunkt.

1. I **Återställningskonfiguration** > **Skapa ny** > **återställningstyp**väljer du **Skapa en virtuell dator**.
2. I **Namnet på virtuella**datorer anger du en virtuell dator som inte finns i prenumerationen.
3. I **resursgruppen**väljer du en befintlig resursgrupp för den nya virtuella datorn eller skapar en ny med ett globalt unikt namn. Om du tilldelar ett namn som redan finns, tilldelar Azure gruppen samma namn som den virtuella datorn.
4. I **Virtuellt nätverk**väljer du det virtuella nätverk där den virtuella datorn ska placeras. Alla virtuella nätverk som är associerade med prenumerationen visas. Markera undernätet. Det första undernätet är markerat som standard.
5. Ange **Storage Location**lagringskontot för den virtuella datorn i Lagringsplats. [Läs mer](#storage-accounts).

    ![Guiden Återställ konfiguration](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Välj **OK**i **Återställningskonfiguration**. Klicka på **Återställ** i **Återställ**för att utlösa återställningen.

## <a name="restore-disks"></a>Återställa diskar

Som ett av [återställningsalternativen](#restore-options)kan du skapa en disk från en återställningspunkt. Sedan med disken kan du göra något av följande:

- Använd mallen som genereras under återställningen för att anpassa inställningar och utlösa vm-distribution. Du redigerar standardmallinställningarna och skickar mallen för vm-distribution.
- [Koppla återställda diskar](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) till en befintlig virtuell dator.
- [Skapa en ny virtuell dator](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) från de återställda diskarna med PowerShell.

1. Välj Återställ diskar i >  **Återställningskonfiguration** > Skapa**ny****återställningstyp**. **Restore disks**
2. I **resursgruppen**väljer du en befintlig resursgrupp för de återställda diskarna eller skapar en ny med ett globalt unikt namn.
3. I **Lagringskonto**anger du det konto som de virtuella hårddiskarna ska kopieras till. [Läs mer](#storage-accounts).

    ![Återställningskonfigurationen har slutförts](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Välj **OK**i **Återställningskonfiguration**. Klicka på **Återställ** i **Återställ**för att utlösa återställningen.

När den virtuella datorn använder hanterade diskar och du väljer alternativet **Skapa virtuell dator** använder Azure Backup inte det angivna lagringskontot. När det gäller **Återställ diskar** och **Omedelbar återställning**används lagringskontot endast för att lagra mallen. Hanterade diskar skapas i den angivna resursgruppen.
När den virtuella datorn använder ohanterade diskar återställs de som blobbar till lagringskontot.

### <a name="use-templates-to-customize-a-restored-vm"></a>Använda mallar för att anpassa en återställd virtuell dator

När disken har återställts använder du mallen som genererades som en del av återställningsåtgärden för att anpassa och skapa en ny virtuell dator:

1. Öppna **Återställ jobbinformation** för det aktuella jobbet.

2. Välj **Distribuera mall** i **Återställ jobbinformation**för att initiera malldistribution.

    ![Återställ jobbgranskning](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Om du vill anpassa den virtuella datorns inställning i mallen klickar du på **Redigera mall**. Om du vill lägga till fler anpassningar klickar du på **Redigera parametrar**.
    - [Läs mer](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) om hur du distribuerar resurser från en anpassad mall.
    - [Läs mer](../azure-resource-manager/templates/template-syntax.md) om hur du redigerar mallar.

   ![Läs in malldistribution](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Ange anpassade värden för den virtuella datorn, acceptera **villkoren och** klicka på **Köp**.

   ![Skicka malldistribution](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Ersätta befintliga diskar

Som ett av [återställningsalternativen](#restore-options)kan du ersätta en befintlig VM-disk med den valda återställningspunkten. [Granska](#restore-options) alla återställningsalternativ.

1. Klicka på Ersätt befintlig i **Återställningskonfiguration**. **Replace existing**
2. Välj **Ersätt disk/s**i **Återställ typ**. Det här är återställningspunkten som ska användas för att ersätta befintliga VM-diskar.
3. I **Mellanlagringsplats**anger du var ögonblicksbilder av de aktuella hanterade diskarna ska sparas under återställningsprocessen. [Läs mer](#storage-accounts).

   ![Guiden Återställ konfiguration Ersätt befintlig](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Återställning mellan regioner

Som ett av [återställningsalternativen](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)kan du återställa virtuella Azure-virtuella datorer i en sekundär region, som är en Azure-region.

Om du vill gå in på funktionen under förhandsgranskningen läser du [avsnittet Innan du börjar](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Om du vill se om CRR är aktiverat följer du instruktionerna i [Konfigurera återställning av korsregionen](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Visa säkerhetskopieringsobjekt i sekundär region

Om CRR är aktiverat kan du visa säkerhetskopieringsobjekten i den sekundära regionen.

1. Från portalen går du till > **Återställningstjänsters valvsäkerhetskopia** **Recovery Services vault**
2. Klicka på **Sekundär region** om du vill visa objekten i den sekundära regionen.

![Virtuella datorer i sekundär region](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Välj sekundär region](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Återställa i sekundär region

Den sekundära användarupplevelsen för att återställa regioner liknar användarupplevelsen för primär regionåterställning. När du konfigurerar information i bladet Återställ konfiguration för att konfigurera återställningen uppmanas du att bara ange sekundära regionparametrar.

![Välj VM för återställning](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Välj återställningspunkt](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Återställ konfiguration](./media/backup-azure-arm-restore-vms/rest-config.png)

![Meddelande om återställning av utlösare](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Om du vill återställa och skapa en virtuell dator läser du [Skapa en virtuell dator](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm).
- Information om hur du [återställer](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks)som en disk finns i Återställ diskar .

>[!NOTE]
>När återställningen har utlösts och i dataöverföringsfasen kan återställningsjobbet inte avbrytas.

### <a name="monitoring-secondary-region-restore-jobs"></a>Övervaka sekundära regioner återställa jobb

1. Från portalen går du till > **Säkerhetskopieringsjobb** för **Återställningstjänster**
2. Klicka på **Sekundär region** om du vill visa objekten i den sekundära regionen.

![Säkerhetskopieringsjobb filtrerade](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Återställa virtuella datorer med speciella konfigurationer

Det finns ett antal vanliga scenarier där du kan behöva återställa virtuella datorer.

**Scenario** | **Riktlinjer**
--- | ---
**Återställa virtuella datorer med hjälp av förmån för hybridanvändning** | Om en Windows VM använder [HUB-licensiering (Hybrid Use Benefit)](../virtual-machines/windows/hybrid-use-benefit-licensing.md)återställs diskarna och skapar en ny virtuell dator med den medföljande mallen (med **licenstyp** inställd **på Windows_Server**) eller PowerShell.  Den här inställningen kan också användas när du har skapat den virtuella datorn.
**Återställa virtuella datorer under en Azure datacenterkatastrof** | Om valvet använder GRS och det primära datacentret för den virtuella datorn går ned, stöder Azure Backup återställa säkerhetskopierade virtuella datorer till det kopplade datacentret. Du väljer ett lagringskonto i det kopplade datacentret och återställer som vanligt. Azure Backup använder beräkningstjänsten i den parade regionen för att skapa den återställda virtuella datorn. [Läs mer](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) om återhämtning av datacenter.
**Återställa virtuell dator med en domänkontrollant i en domän** | Återställ den virtuella datorn som alla andra virtuella datorer. Tänk på följande:<br/><br/> Ur ett Active Directory-perspektiv är Den virtuella Azure-datorn som alla andra virtuella datorer.<br/><br/> Återställningsläge för katalogtjänster (DSRM) är också tillgängligt, så alla Active Directory-återställningsscenarier är genomförbara. [Läs mer](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) om säkerhetskopiering och återställning av överväganden för virtualiserade domänkontrollanter.
**Återställa virtuella datorer för domänkontrollanter i en domän** | Om andra domänkontrollanter i samma domän kan nås över nätverket kan domänkontrollanten återställas som vilken virtuell dator som helst. Om det är den sista återstående domänkontrollanten i domänen, eller om en återställning i ett isolerat nätverk utförs, använder du en [skogsåterställning](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Återställa flera domäner i en skog** | Vi rekommenderar en [skogsåtervinning](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Bare-metal återställa** | Den största skillnaden mellan virtuella Azure-datorer och lokala hypervisorer är att det inte finns någon VM-konsol tillgänglig i Azure. En konsol krävs för vissa scenarier, till exempel återställa med hjälp av en bare-metal recovery (BMR)-typ backup. Vm-återställning från valvet är dock en fullständig ersättning för BMR.
**Återställa virtuella datorer med särskilda nätverkskonfigurationer** | Särskilda nätverkskonfigurationer inkluderar virtuella datorer med interna eller externa belastningsutjämning, med hjälp av flera NICS eller flera reserverade IP-adresser. Du återställer dessa virtuella datorer med [alternativet återställ disk](#restore-disks). Det här alternativet gör en kopia av hårddiskarna till det angivna lagringskontot och du kan sedan skapa en virtuell dator med en [intern](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) eller [extern](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) belastningsutjämnare, [flera NICS](../virtual-machines/windows/multiple-nics.md)eller [flera reserverade IP-adresser](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)i enlighet med konfigurationen.
**Network Security Group (NSG) på nätverkskort/undernät** | Azure VM-säkerhetskopiering stöder säkerhetskopiering och återställning av NSG-information på vnet-, undernäts- och nätverkskortsnivå.
**Fastsatt virtuella datorer för zon** | Azure Backup stöder säkerhetskopiering och återställning av zoned fästa virtuella datorer. [Läs mer](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Spåra återställningen

När du har utlöst återställningen skapar säkerhetskopieringstjänsten ett jobb för spårning. Azure Backup visar meddelanden om jobbet i portalen. Om de inte visas markerar du symbolen **Meddelanden** och väljer sedan **Visa alla jobb** för att se återställningsprocessstatus.

![Återställning utlöses](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Spåra återställning enligt följande:

1. Om du vill visa åtgärder för jobbet klickar du på hyperlänken för meddelanden. Du kan också klicka på **Säkerhetskopieringsjobb**i valvet och klicka sedan på den aktuella virtuella datorn.

    ![Lista över virtuella datorer i ett valv](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Om du vill övervaka återställningsförloppet klickar du på ett återställningsjobb med **statusen Pågår**. Då visas förloppsindikatorn, som visar information om återställningsstatus:

    - **Beräknad återställningstid**: Inledningsvis ger den tid det tar att slutföra återställningen. När åtgärden fortskrider minskar tiden och når noll när återställningen är klar.
    - **Procent av återställningen**. Visar procentandelen återställningsåtgärd som är klar.
    - **Antal överförda byte:** Om du återställer genom att skapa en ny virtuell dator visas de byte som överfördes mot det totala antalet byte som ska överföras.

## <a name="post-restore-steps"></a>Steg efter återställning

Det finns ett antal saker att notera när du har återställt en virtuell dator:

- Tillägg som finns under säkerhetskopieringskonfigurationen installeras, men inte aktiverade. Om du ser ett problem installerar du om tilläggen.
- Om den säkerhetskopierade virtuella datorn hade en statisk IP-adress har den återställda virtuella datorn en dynamisk IP-adress för att undvika konflikter. Du kan [lägga till en statisk IP-adress i den återställda virtuella datorn](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description).
- En återställd virtuell dator har ingen tillgänglighetsuppsättning. Om du använder alternativet återställ disk kan du [ange en tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) när du skapar en virtuell dator från disken med den medföljande mallen eller PowerShell.
- Om du använder en molnbaserad Linuxdistribution, till exempel Ubuntu, blockeras lösenordet efter återställningen av säkerhetsskäl. Använd VMAccess-tillägget på den återställda virtuella datorn för att [återställa lösenordet](../virtual-machines/linux/reset-password.md). Vi rekommenderar att du använder SSH-nycklar på dessa distributioner, så att du inte behöver återställa lösenordet efter återställningen.
- Om du inte kan komma åt den virtuella datorn när den har återställts på grund av att den virtuella datorn har brutit relation med domänkontrollanten följer du stegen nedan för att ta upp den virtuella datorn:
  - Koppla OS-disken som en datadisk till en återställd virtuell dator.
  - Installera VM-agent manuellt om Azure Agent inte svarar genom att följa den här [länken](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
  - Aktivera serial konsolåtkomst på virtuell dator för att tillåta kommandoradsåtkomst till virtuell dator

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - När den virtuella datorn byggs om använder Azure-portalen för att återställa lokala administratörskonto och lösenord
  - Använd seriell konsolåtkomst och CMD för att skilja virtuella datorer från domänen

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- När den virtuella datorn är osammanhängande och startas om, kommer du att kunna framgångsrikt RDP till VM med lokala administratörsautentiseringsuppgifter och återgå till VM tillbaka till domänen framgångsrikt.

## <a name="backing-up-restored-vms"></a>Säkerhetskopiera återställda virtuella datorer

- Om du återställde en virtuell dator till samma resursgrupp med samma namn som den ursprungligen säkerhetskopierade virtuella datorn fortsätter säkerhetskopieringen på den virtuella datorn efter återställning.
- Om du återställde den virtuella datorn till en annan resursgrupp eller om du har angett ett annat namn för den återställda virtuella datorn måste du konfigurera säkerhetskopian för den återställda virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Om du får problem under återställningsprocessen [kan du granska](backup-azure-vms-troubleshoot.md#restore) vanliga problem och fel.
- När den virtuella datorn har återställts kan du läsa om hur du [hanterar virtuella datorer](backup-azure-manage-vms.md)
