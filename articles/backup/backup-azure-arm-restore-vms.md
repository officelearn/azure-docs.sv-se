---
title: 'Azure Backup: Återställa virtuella datorer med hjälp av Azure portal'
description: Återställa en Azure virtuell dator från en återställningspunkt med hjälp av Azure-portalen
services: backup
author: geethalakshmig
manager: vijayts
keywords: Återställ säkerhetskopia. Så här återställer du; återställningspunkt;
ms.service: backup
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: geg
ms.openlocfilehash: b0408aa296dcbff0c73f2c192e24c290d51fec5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647827"
---
# <a name="restore-azure-vms"></a>Återställa virtuella Azure-datorer

Den här artikeln beskrivs hur du återställer Azure VM-data från återställningspunkter som lagras i [Azure Backup](backup-overview.md) Recovery Services-valv.



### <a name="restore-options"></a>Återställningsalternativ

Azure Backup innehåller ett antal sätt att återställa en virtuell dator.

**Återställningsalternativ** | **Detaljer**
--- | ---
**Skapa en ny virtuell dator** | Snabbt skapar och hämtar igång en grundläggande virtuell dator från en återställningspunkt.<br/><br/> Du kan ange ett namn för den virtuella datorn, väljer du resursgruppen och virtuellt nätverk (VNet) som den ska placeras och ange en lagringstyp.
**Återställa disk** | Återställer en virtuell disk som sedan kan användas för att skapa en ny virtuell dator.<br/><br/> Azure Backup är en mall för att hjälpa dig att anpassa och skapa en virtuell dator. <br/><br/> Det här alternativet kopieras VHD: er till det lagringskonto som du anger. Återställningsjobbet genererar en mall som du kan hämta och använda för att ange anpassade inställningar för virtuell dator och skapa en virtuell dator.<br/><br/> Lagringskontot ska vara på samma plats som valvet. Skapa ett lagringskonto om du inte har något.<br/><br/> Replikering för lagringskontotypen visas. Zonredundant lagring (ZRS) stöds inte.<br/><br/> Du kan också koppla disken till en befintlig virtuell dator eller skapa en ny virtuell dator med hjälp av PowerShell.<br/><br/> Det här alternativet är användbart om du vill anpassa den virtuella datorn, lägger du till inställningar som inte var det vid tidpunkten för säkerhetskopiering eller inställningar som måste konfigureras med hjälp av mall eller PowerShell.
**Ersätt befintliga** | Du kan återställa en disk och använda den för att ersätta en disk på den befintliga virtuella datorn.<br/><br/> Den aktuella virtuella datorn måste finnas. Om den har tagits bort det här alternativet kan inte användas.<br/><br/> Azure Backup tar en ögonblicksbild av den befintliga virtuella datorn innan du byter ut disken. Ögonblicksbilden lagras i mellanlagringsplatsen som du anger. Befintliga diskar som är anslutna till den virtuella datorn ersätts sedan med hjälp av den valda återställningspunkten.<br/><br/> Den ögonblicksbild som togs kopieras till valvet och bevaras i enlighet med angivna bevarandeprincipen. <br/><br/> Ersätt befintlig stöds för okrypterade hanterade virtuella datorer. Det finns inte stöd för ohanterade diskar [generaliserad VMs](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), eller för virtuella datorer [skapats med hjälp av anpassade avbildningar](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Om återställningspunkten har mer eller mindre diskar än den aktuella virtuella datorn, avspeglar VM-konfigurationen endast i antalet diskar i återställningspunkten.<br/><br/>

> [!NOTE]
> Du kan också återställa specifika filer och mappar på en Azure-dator. [Läs mer](backup-azure-restore-files-from-vm.md).
>
> Om du kör den [senaste versionen](backup-instant-restore-capability.md) av Azure Backup för virtuella Azure-datorer (även kallat omedelbar återställning), ögonblicksbilder sparas i upp till sju dagar och du kan återställa en virtuell dator från ögonblicksbilder innan säkerhetskopierade data skickas till valvet. Om du vill återställa en virtuell dator från en säkerhetskopia från de senaste sju dagarna, går det snabbare att återställa från ögonblicksbilden och inte från valvet.

## <a name="before-you-start"></a>Innan du börjar

Återställa en virtuell dator (skapa en ny virtuell dator) kontrollera att du har den rätt rollbaserad åtkomstkontrollen (RBAC) [behörigheter](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) för åtgärden återställa en virtuell dator.

Om du inte har behörighet, kan du [återställa en disk](#restore-disks), och när disken har återställts kan du [använda mallen](#use-templates-to-customize-a-restored-vm) som skapades som en del av återställningen ska skapa en ny virtuell dator.



## <a name="select-a-restore-point"></a>Välj en återställningspunkt

1. I valvet som är associerade med den virtuella datorn som du vill återställa, klickar du på **Säkerhetskopiera objekt** > **Azure-dator**.
2. Klicka på en virtuell dator. Som standard på VM-instrumentpanelen visas återställningspunkterna från de senaste 30 dagarna. Du kan visa återställningspunkter som är äldre än 30 dagar eller filter för att hitta återställningspunkter baserat på datum, tidsintervall och olika typer av ögonblicksbild konsekvens.
3. Om du vill återställa den virtuella datorn klickar du på **återställa en virtuell dator**.

    ![Återställningspunkt](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Välj en återställningspunkt som ska användas för återställningen.

## <a name="choose-a-vm-restore-configuration"></a>Välj en konfiguration för återställning av virtuell dator

1. I **återställningskonfiguration**, Välj ett alternativ för återställning:
    - **Skapa ny**: Använd det här alternativet om du vill skapa en ny virtuell dator. Du kan skapa en virtuell dator med enkla inställningar eller återställa en disk och skapa en anpassad virtuell dator.
    - **Ersätt befintliga**: Använd det här alternativet om du vill ersätta diskar på en befintlig virtuell dator.

        ![Återställa konfigurationsguiden](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Ange inställningar för dina valda alternativ.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Som en av de [återställningsalternativ](#restore-options), kan du snabbt skapa en virtuell dator med grundläggande inställningar från en återställningspunkt.

1. I **återställningskonfiguration** > **Skapa nytt** > **återställa typ**väljer **skapa en virtuell dator** .
2. I **virtuellt datornamn**, ange en virtuell dator som inte finns i prenumerationen.
3. I **resursgrupp**, Välj en befintlig resursgrupp för den nya virtuella datorn eller skapa en ny med ett globalt unikt namn. Om du tilldelar ett namn som redan finns, tilldelar Azure gruppen med samma namn som den virtuella datorn.
4. I **virtuellt nätverk**, Välj det virtuella nätverket där den virtuella datorn ska placeras. Alla virtuella nätverk som är associerade med prenumerationen visas. Välj undernätet. Det första undernätet är markerad som standard.
5. I **lagringsplats**, ange lagringstypen för den virtuella datorn.

    ![Återställa konfigurationsguiden](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. I **återställningskonfiguration**väljer **OK**. I **återställa**, klickar du på **återställa** som utlöser återställningen.


## <a name="restore-disks"></a>Återställ diskar

Som en av de [återställningsalternativ](#restore-options), kan du skapa en disk från en återställningspunkt. Sedan med disken, kan du göra något av följande:

- Med mallen som skapas under återställningen ska anpassa inställningar och utlösa distribution av virtuella datorer. Du redigerar mallen standardinställningarna och skicka mallen för distribution av virtuella datorer.
- [Koppla återställda diskar](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) till en befintlig virtuell dator.
- [Skapa en ny virtuell dator](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) från återställda diskar med hjälp av PowerShell.

1. I **återställningskonfiguration** > **Skapa nytt** > **återställa typ**väljer **Återställ diskar**.
2. I **resursgrupp**, Välj en befintlig resursgrupp för de återställda diskarna eller skapa en ny med ett globalt unikt namn.
3. I **lagringskonto**, ange det konto som ska kopieras de virtuella hårddiskarna. Kontrollera att kontot är i samma region som valvet.

    ![Återställningskonfiguration har slutförts](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. I **återställningskonfiguration**väljer **OK**. I **återställa**, klickar du på **återställa** som utlöser återställningen.

Under VM-återställning använder Azure Backup inte storage-konto. Men i följande fall **Återställ diskar** och **omedelbar återställning**, lagringskontot används för att lagra mallen.

### <a name="use-templates-to-customize-a-restored-vm"></a>Använd mallar för att anpassa en återställd virtuell dator

När disken har återställts måste du använda mallen som skapades som en del av återställningen kan anpassa och skapa en ny virtuell dator:

1. Öppna **återställa jobbinformation** för relevanta jobbet.

2. I **återställa jobbinformation**väljer **distribuera mall** initierar malldistribution.

    ![Återställa jobbet nedåt](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Om du vill anpassa VM-inställningen som medföljer mallen, klickar du på **redigera mallen**. Om du vill lägga till fler anpassningar, klickar du på **redigera parametrar**.
    - [Läs mer](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) om hur du distribuerar resurser från en anpassad mall.
    - [Läs mer](../azure-resource-manager/resource-group-authoring-templates.md) om att skapa mallar.

   ![Läsa in malldistribution](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Ange anpassade värden för den virtuella datorn, godkänner den **villkor** och klicka på **köp**.

   ![Skicka malldistribution](./media/backup-azure-arm-restore-vms/submitting-template1.png)


## <a name="replace-existing-disks"></a>Byta ut befintliga diskar

Som en av de [återställningsalternativ](#restore-options), du kan ersätta en befintlig VM-disk med den valda återställningspunkten. [Granska](#restore-options) alla alternativ för återställning.

1. I **återställningskonfiguration**, klickar du på **ersätta befintliga**.
2. I **återställa typ**väljer **ersätta disk/s**. Det här är den återställningspunkt som ska använda Ersätt befintliga VM-diskar.
3. I **mellanlagringsplatsen**, ange där ögonblicksbilder av de aktuella hanterade diskarna ska sparas.

   ![Återställa ersätter befintliga konfigurationsguiden](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="restore-vms-with-special-configurations"></a>Återställa virtuella datorer med särskilda konfigurationer

Det finns ett antal vanliga scenarier där du kan behöva återställa virtuella datorer.

**Scenario** | **Riktlinjer**
--- | ---
**Återställa virtuella datorer med hjälp av Hybrid Use Benefit** | Om en virtuell Windows-dator använder [Hybrid Använd förmånen (HUB) licensiering](../virtual-machines/windows/hybrid-use-benefit-licensing.md), återställa diskarna och skapa en ny virtuell dator med hjälp av den angivna mallen (med **licenstyp** inställd **Windows_Server**) , eller PowerShell.  Den här inställningen kan också användas när du har skapat den virtuella datorn.
**Återställa virtuella datorer under en Azure i datacentret** | Om valvet använder GRS och det primära datacentret för den virtuella datorn stängs av, stöder Azure Backup återställa säkerhetskopierade virtuella datorer till parat datacenter. Du väljer ett lagringskonto i parat datacenter och återställa som vanligt. Azure Backup använder beräkningstjänsten på parad plats för att skapa den återställda virtuella datorn. [Läs mer](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) om datacenter återhämtning.
**Återställa en enda domänkontrollant virtuell dator i en enda domän** | Återställa den virtuella datorn som andra virtuella datorer. Tänk på följande:<br/><br/> Azure VM är som andra virtuella datorer från en Active Directory-perspektiv.<br/><br/> Directory Services återställningsläge (DSRM) är också tillgängligt, så att alla scenarion för återställning av Active Directory är genomförbart. [Läs mer](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v) om säkerhetskopiering och återställning för virtualiserade domänkontrollanter.
**Återställa flera virtuella datorer för domänkontrollanter i en enda domän** | Om andra domänkontrollanter i samma domän kan nås över nätverket, kan domänkontrollanten återställas som en virtuell dator. Om det är den sista återstående domänkontrollanten i domänen eller en återställning i ett isolerat nätverk utförs, använda en [skog recovery](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Återställa flera domäner i en skog** | Vi rekommenderar en [skog recovery](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Bare metal-återställning** | Den största skillnaden mellan virtuella datorer i Azure och lokala hypervisorer är att det finns inga VM-konsolen i Azure. En konsol krävs för vissa scenarier, till exempel återställa med hjälp av en återställning utan operativsystem (BMR)-typ av säkerhetskopiering. Återställning av virtuella datorer från valvet är dock en fullständig ersättning för BMR.
**Återställa virtuella datorer med särskilda nätverkskonfigurationer** | Särskilda nätverkskonfigurationer innefattar virtuella datorer med hjälp av interna eller externa load balancing, med flera nätverkskort eller flera reserverade IP-adresser. Du kan återställa dessa virtuella datorer med hjälp av den [återställa diskalternativ](#restore-disks). Det här alternativet gör en kopia av de virtuella hårddiskarna till det angivna lagringskontot du kan sedan skapa en virtuell dator med en [interna](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) eller [externa](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/) belastningsutjämnare, [flera nätverkskort](../virtual-machines/windows/multiple-nics.md), eller [flera reserverade IP-adresser](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), i enlighet med din konfiguration.
**Nätverkssäkerhetsgrupp (NSG) på NIC/undernät** | Azure VM backup stöder säkerhetskopiering och återställning för NSG information på virtuellt nätverk, undernät och NIC-nivå.
**Zon Fäst virtuella datorer** | Azure Backup stöder säkerhetskopiering och återställning av zonindelat fästa virtuella datorer. [Läs mer](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Spåra återställningen
När du utlösa återställningsåtgärden, skapar ett jobb för spårning av säkerhetskopieringstjänsten. Azure Backup visar meddelanden om jobbet i portalen. Om de inte visas klickar du på den **meddelanden** symbolen så får du se dem.

![Återställning utlöses](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Spåra återställning på följande sätt:

1. Om du vill visa åtgärder för jobbet, klickar du på hyperlänken för meddelanden. Du kan också i valvet, klickar du på **säkerhetskopieringsjobb**, och klicka sedan på den relevanta virtuella datorn.

    ![Lista över virtuella datorer i ett valv](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Du kan övervaka förloppet för återställning, klickar du på återställningsjobb med statusen **pågående**. Detta visar förloppsindikatorn som visar information om förloppet för återställning:

    - **Uppskattad tidsåtgång för återställning**: Först innehåller den tid det tar att slutföra återställningen. När åtgärden allt är klart, den tid det tar minskar och når noll när återställningen är klar.
    - **Procentandel av återställning**. Visar procentuella andelen återställningen som utförts.
    - **Antalet byte som överfördes**: Om du återställning genom att skapa en ny virtuell dator, visas de byte som överfördes mot det totala antalet byte som ska överföras.

## <a name="post-restore-steps"></a>Efter återställning steg

Det finns ett antal saker att tänka på när du återställer en virtuell dator:

- Tillägg som finns under konfigurationen för säkerhetskopiering är installerad men inte aktiverad. Om du ser ett problem kan du installera om tilläggen.
- Om den säkerhetskopierade virtuella datorn en statisk IP-adress måste har den återställda virtuella datorn en dynamisk IP-adress för att undvika konflikter. Du kan [lägga till en statisk IP-adress till den återställda virtuella datorn](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- En återställd virtuell dator inte har en tillgänglighet ange. Om organisationsenheten Använd alternativet Återställ disk till dig kan [ange en tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) när du skapar en virtuell dator från disken med hjälp av den angivna mallen eller PowerShell.
- Om du använder en cloud-init-baserade Linux-distribution, till exempel Ubuntu, blockeras lösenordet av säkerhetsskäl efter återställningen. Använda VMAccess-tillägget på den återställda virtuella datorn till [återställa lösenordet](../virtual-machines/linux/reset-password.md). Vi rekommenderar att du använder SSH-nycklar på dessa distributioner, så du inte behöver återställa lösenordet efter återställningen.


## <a name="backing-up-restored-vms"></a>Säkerhetskopierar återställda virtuella datorer

- Om du har återställt en virtuell dator till samma resursgrupp med samma namn som den ursprungligen säkerhetskopierade virtuella datorn fortsätter säkerhetskopiering på den virtuella datorn efter återställning.
- Om du har återställt den virtuella datorn till en annan resursgrupp eller du har angett ett annat namn för den återställda virtuella datorn kan behöva du konfigurera säkerhetskopiering för den återställda virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Om det uppstår problem under återställningsprocessen, [granska](backup-azure-vms-troubleshoot.md#restore) vanliga problem och fel.
- När den virtuella datorn har återställts, Lär dig mer om [hantering av virtuella datorer](backup-azure-manage-vms.md)
