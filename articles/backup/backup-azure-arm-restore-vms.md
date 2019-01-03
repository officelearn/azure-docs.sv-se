---
title: 'Azure Backup: Återställa virtuella datorer med hjälp av Azure portal'
description: Återställa en Azure virtuell dator från en återställningspunkt med hjälp av Azure-portalen
services: backup
author: geethalakshmig
manager: vijayts
keywords: Återställ säkerhetskopia. Så här återställer du; återställningspunkt;
ms.service: backup
ms.topic: conceptual
ms.date: 09/04/2017
ms.author: geg
ms.openlocfilehash: ac0c727e41ad9b361ea9f558a97f16446c12ef0e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793384"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Använd Azure portal för att återställa virtuella datorer
Skydda dina data genom att ta ögonblicksbilder av dina data med definierade intervall. De här ögonblicksbilderna kallas återställningspunkter och de lagras i Recovery Services-valv. Om det är nödvändigt att reparera eller återskapa en virtuell dator (VM), kan du återställa den virtuella datorn från någon av de sparade återställningspunkterna. När du återställer en återställningspunkt kan du:

* Skapa en ny virtuell dator: Från en point-in-time återställningspunkt för den säkerhetskopierade virtuella datorn.
* Återställ diskar: Använda mallen som ingår i processen för att anpassa den återställda virtuella datorn eller göra en enskild fil-återställning.

Den här artikeln förklarar hur du återställer en virtuell dator till en ny virtuell dator eller återställer alla säkerhetskopierade diskar. Enskilda filåterställning finns [återställa filer från en virtuell Azure-säkerhetskopiering](backup-azure-restore-files-from-vm.md).

![Tre sätt att återställa från säkerhetskopiering av virtuella datorer](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)


Återställa en virtuell dator eller alla diskar från VM omfattar backup två steg:

* Välj en återställningspunkt för återställning.
* Välj typ av återställning
    - Alternativ 1: Skapa en ny virtuell dator
    - Alternativ 2: Återställ diskar.

## <a name="select-a-restore-point-for-restore"></a>Välj en återställningspunkt för återställning
1. Logga in på [Azure Portal](http://portal.azure.com/).

2. På Azure-menyn, Välj **alla tjänster**. I listan över tjänster skriver **återställningstjänster** eller gå till **STORAGE** där den **Recovery Service-valv** är visas, markerar du den.

    ![Recovery Services-valv](./media/backup-azure-arm-restore-vms/open-recovery-services-vault1.png)

3. Lista över valv i prenumerationen visas.

    ![Listan över Recovery Services-valv](./media/backup-azure-arm-restore-vms/list-of-rs-vaults1.png)

4. I listan över Recovery Service-valv Välj valvet som är associerade med den virtuella datorn som du vill återställa. När du väljer valvet öppnas valvets instrumentpanel.

    ![Valda Recovery Services-valv](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade1.png)

5. I instrumentpanelen för valvet på den **Säkerhetskopieringsobjekt** panelen, väljer **Azure-dator**.

    ![Instrumentpanelen för valvet](./media/backup-azure-arm-restore-vms/vault-dashboard1.png)

6. Den **Säkerhetskopieringsobjekt** öppnas bladet med listan över virtuella Azure-datorer.

    ![Lista över virtuella datorer i valvet](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault1.png)

7. Välj en virtuell dator för att öppna instrumentpanelen i listan. VM-instrumentpanelen öppnas på området övervakning, som innehåller den **återställningspunkter**. Alla åtgärder för VM-nivå som **Säkerhetskopiera nu**, **filen recovery**, **stoppa säkerhetskopiering** kan utföras från det här bladet.
Återställning kan utföras på många sätt från det här bladet. Observera att det här bladet visar endast de senaste 30 dagarna återställningspunkter.

    - Om du vill återställa med hjälp av en återställningspunkt från de senaste 30 dagarna, högerklickar du på den virtuella datorn > **återställa en virtuell dator**.
    - Om du vill återställa med hjälp av en återställningspunkt som är äldre än 30 dagar, klickar du på länken under **återställningspunkter**.
    - Om du vill visa och filtrera virtuella datorer med anpassade datum, klickar du på **återställa en virtuell dator** på menyn. Med filtret för att ändra tidsintervallet för de visade återställningspunkterna. Du kan också filtrera på olika typer av datakonsekvens.
8. Granska inställningarna för återställning:
    - Data konsekvens visas den [typen av konsekvens](backup-azure-vms-introduction.md#consistency-types) i återställningspunkten.
    - Den **återställningstyp** visar där punkten lagras (i ett lagringskonto i valvet eller båda. [Läs mer](https://azure.microsoft.com/blog/large-disk-support/) om omedelbar återställningspunkter.

  ![Återställningspunkter](./media/backup-azure-arm-restore-vms/vm-blade1.png)
9. Välj en återställningspunkt.

10. Välj **återställningskonfiguration**. Den **Återställ konfigurationen** blad öppnas.

## <a name="choose-a-vm-restore-configuration"></a>Välj en konfiguration för återställning av virtuell dator
Välj en konfiguration för återställning av virtuell dator när du har valt återställningspunkten. Du kan använda Azure portal eller PowerShell om du vill konfigurera den återställda virtuella datorn.

### <a name="restore-options"></a>Återställningsalternativ

**Alternativ** | **Detaljer**
--- | ---
**Skapa ny – Skapa virtuell dator** | Motsvarar Snabbregistrering av en virtuell dator. Hämtar en grundläggande virtuell dator igång från en återställningspunkt.<br/><br/> Inställningarna för virtuella datorer kan ändras som en del av återställningsprocessen.
**Skapa ny återställning disk** | Skapar en virtuell dator som du kan anpassa som en del av återställningsprocessen.<br/><br/> Det här alternativet kopieras VHD: er till det lagringskonto som du anger.<br/><br/> -Lagringskontot ska vara på samma plats som valvet.<br/><br/> Om du inte har något lämpligt konto måste du skapa en.<br/><br/> Replikering lagringskontotypen visas inom parentes. Zonredundant lagring (ZRS) stöds inte.<br/><br/> Från lagringskontot kan antingen koppla återställda diskar till en befintlig virtuell dator eller skapa en ny virtuell dator från de återställda diskar med hjälp av PowerShell.
**Ersätt befintliga** | Med det här alternativet behöver du inte skapa en virtuell dator.<br/><br/> Den aktuella virtuella datorn måste finnas. Om den har tagits bort det här alternativet kan inte användas.<br/><br/> Azure Backup tar en ögonblicksbild av den befintliga virtuella datorn och lagrar den i mellanlagringsplatsen anges. Befintliga diskar som är anslutna till den virtuella datorn sedan ersätts med den valda återställningspunkten. Ögonblicksbilden som skapades tidigare kopieras till valvet och lagras som en återställningspunkt i enlighet med din princip för kvarhållning av säkerhetskopior.<br/><br/> Ersätt befintlig stöds för okrypterade hanterade virtuella datorer. Det finns inte stöd för ohanterade diskar [generaliserad VMs](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), eller för virtuella datorer [skapats med hjälp av anpassade avbildningar](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Om återställningspunkten har mer eller mindre diskar än den aktuella virtuella datorn, visas den virtuella datorn endast i antalet diskar i återställningspunkten.

> [!NOTE]
> Om du vill återställa en virtuell dator med avancerade nätverksinställningar, till exempel om de hanteras av en intern eller extern belastningsutjämnare eller har flera nätverkskort eller flera reserverade IP-adresser kan du återställa med PowerShell. [Läs mer](#restore-vms-with-special-network-configurations).
> Om en virtuell Windows-dator använder [HUB licensiering](../virtual-machines/windows/hybrid-use-benefit-licensing.md), använda den **skapa nya Återställ disk** alternativet och sedan använda PowerShell eller restore-mallen för att skapa den virtuella datorn med **licenstyp** inställd **Windows_Server**. Den här inställningen kan också användas när du har skapat den virtuella datorn.


Ange återställningen inställningen på följande sätt:

1. I **återställa**väljer en [återställningspunkt](#select-a-restore-point-for-restore) > **återställningskonfiguration**.
2. I **Återställ konfigurationen**väljer du hur du vill återställa den virtuella datorn i enlighet med inställningarna som sammanfattas i tabellen ovan.

    ![Återställa konfigurationsguiden](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)


## <a name="create-new-create-a-vm"></a>Skapa ny – skapa en virtuell dator

1. I **återställningskonfiguration** > **Skapa nytt** > **återställa typ**väljer **skapa en virtuell dator** .
2. I **virtuellt datornamn**, ange en virtuell dator som inte finns i prenumerationen.
3. I **resursgrupp**, Välj en befintlig resursgrupp för den nya virtuella datorn eller skapa en ny med ett globalt unikt namn. Om du tilldelar ett namn som redan finns, tilldelar Azure gruppen med samma namn som den virtuella datorn.
4. I **virtuellt nätverk**, Välj det virtuella nätverket där den virtuella datorn ska placeras. Alla virtuella nätverk som är associerade med prenumerationen visas. Välj undernätet. Det första undernätet är markerad som standard.
5. I **lagringsplats**, ange lagringstypen som används för den virtuella datorn.

    ![Återställa konfigurationsguiden](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. I **återställningskonfiguration**väljer **OK**. I **återställa**, klickar du på **återställa** som utlöser återställningen.



## <a name="create-new-restore-disks"></a>Skapa ny återställning av diskar

1. I **återställningskonfiguration** > **Skapa nytt** > **återställa typ**väljer **Återställ diskar**.
2. I **resursgrupp**, Välj en befintlig resursgrupp för de återställda diskarna eller skapa en ny med ett globalt unikt namn.
3. I **lagringskonto**, ange det konto som ska kopieras de virtuella hårddiskarna. Kontrollera att kontot är i samma region som valvet.

    ![Återställningskonfiguration har slutförts](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. I **återställningskonfiguration**väljer **OK**. I **återställa**, klickar du på **återställa** som utlöser återställningen.
5. När disken har återställts kan göra du något av följande för att slutföra återställningen VM.

    - Med mallen som skapades som en del av återställningen för att anpassa inställningar och utlösa distribution av virtuella datorer. Du redigerar mallen standardinställningarna och skicka mallen för distribution av virtuella datorer.
    - Du kan [Anslut återställda diskarna](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps) till en befintlig virtuell dator.
    - Du kan [skapa en ny virtuell dator](backup-azure-vms-automation.md#restore-an-azure-vm) från återställda diskar med hjälp av PowerShell.


## <a name="replace-existing-disks"></a>Byta ut befintliga diskar

Använd det här alternativet för att ersätta befintliga diskar i den aktuella virtuella datorn med den valda återställningspunkten.

1. I **återställningskonfiguration**, klickar du på **ersätta befintliga**.
2. I **återställa typ**väljer **ersätta disk/s** (den återställningspunkt som ska ersätta den befintliga virtuella datorn eller de diskar).
3. I **mellanlagringsplatsen**, ange där ögonblicksbilder av de aktuella hanterade diskarna ska sparas.

   ![Återställa ersätter befintliga konfigurationsguiden](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="track-the-restore-operation"></a>Spåra återställningen
När du utlösa återställningsåtgärden skapar tjänsten backup ett jobb för att spåra återställningen igen. Tjänsten backup skapar även och visar tillfälligt meddelande i den **meddelanden** området i portalen. Om du inte ser meddelandet, väljer du den **meddelanden** symbol att visa dina meddelanden.

![Återställning utlöses](./media/backup-azure-arm-restore-vms/restore-notification1.png)

Klicka på länken för att gå till meddelanden **BackupJobs** lista. All information på åtgärder för ett visst jobb finns i den **BackupJobs** visas. Du kan gå till **BackupJobs** från instrumentpanelen för valvet genom att klicka på den säkerhetskopieringsjobb panelen, väljer **Azure-dator** att visa de jobb som är associerade med valvet.

Den **säkerhetskopieringsjobb** bladet öppnas och visar en lista över jobb.

![Lista över virtuella datorer i ett valv](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

**Förloppsindikatorn** är nu tillgängligt i den **återställa information om** bladet. Den **återställa information om** bladet kan öppnas genom att klicka på återställningsjobb som har statusen **pågående**. Den **förloppsindikatorn** är tillgänglig i alla varianter av återställningar som **Skapa ny**, **Återställ Disk** och **ersätta befintliga**. Informationen som utförs genom att återställa förloppsindikator för installationen är **beräknat återställningstillfället**, **procentandelen återställning** och **antal byte som överfördes**.

Återställa förloppsindikator information anges nedan:

- **Uppskattad tidsåtgång för återställning** först innehåller den tid det tar att slutföra återställningen. När åtgärden allt är klart, den tid det tar minskar och når 0 när återställningen är klar.
- **Procentandel av återställning** tillhandahåller data viss procentandel av återställningen har slutförts.
- **Antalet byte som överfördes** är tillgänglig i underaktivitet när återställning sker via Skapa ny virtuell dator. Detta ger information om hur många antal byte som överfördes mot det totala antalet byte som ska överföras.


## <a name="use-templates-to-customize-a-restored-vm"></a>Använd mallar för att anpassa en återställd virtuell dator
Efter den [åtgärden Återställ diskar är klar](#Track-the-restore-operation), med mallen som skapades som en del av återställningen kan du skapa en ny virtuell dator med en konfiguration skiljer sig från konfigurationen för säkerhetskopiering. Du kan också använda det för att anpassa namnen på de resurser som skapades under processen för att skapa en ny virtuell dator från en återställningspunkt.

![Återställa jobbet nedåt](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

Hämta den mall som skapades som en del av alternativet Återställ diskar:

1. Gå till den **återställa jobbinformation** för jobbet.

2. På den **återställa jobbinformation** väljer **distribuera mall** initierar malldistribution.

3. På den **distribuera mallen** bladet för anpassad distribution, Använd malldistribution för att [redigera och distribuera mallen](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) eller lägga till fler anpassningar av [redigera en mall för](../azure-resource-manager/resource-group-authoring-templates.md) innan du distribuerar.

  ![Läsa in malldistribution](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. När du har angett värdena som krävs, accepterar den **villkor** och välj **köp**.

  ![Skicka malldistribution](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="post-restore-steps"></a>Efter återställning steg
* Om du använder en cloud-init-baserade Linux-distribution, till exempel Ubuntu, av säkerhetsskäl lösenordet blockeras efter återställningen. Använda VMAccess-tillägget på den återställda virtuella datorn till [återställa lösenordet](../virtual-machines/linux/reset-password.md). Vi rekommenderar att du använder SSH-nycklar på dessa distributioner att undvika att återställa lösenordet efter återställningen.
* Tillägg som finns under konfigurationen för säkerhetskopiering har installerats, men de kommer inte aktiveras. Om du ser ett problem kan du installera om tilläggen.
* Om den säkerhetskopierade virtuella datorn har statiska IP-post-återställning, har den återställda virtuella datorn en dynamisk IP-adress att undvika konflikt när du skapar en återställd virtuell dator. Mer information om hur du kan [lägga till en statisk IP-adress till en återställd virtuell dator](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* En återställd virtuell dator har inte en tillgänglighet värdet. Vi rekommenderar att du använder alternativet Återställ diskar till [lägga till en tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) när du skapar en virtuell dator från PowerShell eller mallar med hjälp av återställs diskar.


## <a name="backup-for-restored-vms"></a>Säkerhetskopiering för återställda virtuella datorer
Om du har återställt en virtuell dator till samma resursgrupp med samma namn som den ursprungligen säkerhetskopierade virtuella datorn fortsätter säkerhetskopiering på virtuella datorer efter återställningen. Om du har återställt den virtuella datorn till en annan resursgrupp eller om du har angett ett annat namn för den återställda virtuella datorn, behandlas den virtuella datorn som om det är en ny virtuell dator. Du måste konfigurera säkerhetskopiering för den återställda virtuella datorn.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Återställa en virtuell dator under en Azure i datacentret
Azure Backup kan återställa säkerhetskopierade virtuella datorer till parat datacenter om en katastrof inträffar i det primära datacentret där virtuella datorer körs och du har konfigurerat säkerhetskopieringsvalvet för att vara geo-redundant. Välj ett lagringskonto som finns i ett parat datacenter under sådana scenarier. Resten av återställningsprocessen förblir densamma. Backup använder beräkningstjänst från parad geo för att skapa den återställda virtuella datorn. Mer information finns i [Azure-datacenter återhämtning](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Återställa virtuella datorer för domänkontrollanter
Säkerhetskopiering av en domänkontrollant (DC) virtuella datorer är ett scenario som stöds med Backup. Du måste dock vara försiktig under återställningsprocessen. Rätt återställningsprocessen beror på strukturen för domänen. I det enklaste fallet har du en Domänkontrollant i en enda domän. Oftast för produktion belastning har du en enda domän med flera domänkontrollanter, kanske med vissa domänkontrollanter lokalt. Du kan dessutom ha en skog med flera domäner.

Azure VM är från en Active Directory-perspektiv som andra virtuella datorer på en modern hypervisor som stöds. Med den lokala hypervisorer största skillnaden är att det finns inga VM-konsolen i Azure. En konsol krävs för vissa scenarier, till exempel återställa med hjälp av en återställning utan operativsystem (BMR)-typ av säkerhetskopiering. Återställning av virtuella datorer från säkerhetskopieringsvalvet är dock en fullständig ersättning för BMR. Directory Services återställningsläge (DSRM) är också tillgängligt, så att alla scenarion för återställning av Active Directory är genomförbart. Mer information finns i [överväganden för säkerhetskopiering och återställning för virtualiserade domänkontrollanter](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) och [planera för Active Directory-skog recovery](https://technet.microsoft.com/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Domänkontrollant i en enda domän
Den virtuella datorn kan återställas (t.ex. andra virtuella datorer) från Azure portal eller med hjälp av PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Flera domänkontrollanter i en enda domän
När andra domänkontrollanter i samma domän kan nås över nätverket, kan domänkontrollanten återställas som en virtuell dator. Om det är den sista återstående domänkontrollanten i domänen eller en återställning i ett isolerat nätverk utförs, måste en skog recovery procedur följas.

### <a name="multiple-domains-in-one-forest"></a>Flera domäner i en skog
När andra domänkontrollanter i samma domän kan nås över nätverket, kan domänkontrollanten återställas som en virtuell dator. I alla andra fall rekommenderar vi en för skogsåterställning.

## <a name="restore-vms-with-special-network-configurations"></a>Återställa virtuella datorer med särskilda nätverkskonfigurationer
Det går att säkerhetskopiera och återställa virtuella datorer med följande särskilda nätverkskonfigurationer. Dessa konfigurationer kräver dock vissa särskilt beaktande när du går igenom återställningsprocessen:

* Virtuella datorer under belastningsutjämnare (interna och externa)
* Virtuella datorer med flera reserverade IP-adresser
* Virtuella datorer med flera nätverkskort

> [!IMPORTANT]
> När du skapar särskilda nätverkskonfigurationen för virtuella datorer, måste du använda PowerShell för att skapa virtuella datorer från återställda diskar.
>
>

Följ dessa steg för att fullständigt återskapa de virtuella datorerna när du återställer till disk:

1. Återställ diskar från en Recovery Services-valv med hjälp av [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

1. Skapa VM-konfiguration som krävs för belastningsutjämnaren eller flera nätverkskort/flera reserverad IP med hjälp av PowerShell-cmdlets. Du kan använda den för att skapa den virtuella datorn med den konfiguration du vill ha:

   a. Skapa en virtuell dator i Molntjänsten med en [intern belastningsutjämnare](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Skapa en virtuell dator att ansluta till en [internetuppkopplade belastningsutjämnare](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/).

   c. Skapa en virtuell dator med [flera nätverkskort](../virtual-machines/windows/multiple-nics.md).

   d. Skapa en virtuell dator med [flera reserverade IP-adresser](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md).

## <a name="next-steps"></a>Nästa steg
Nu när du kan återställa dina virtuella datorer, se felsökningsartikeln information på vanliga fel med virtuella datorer. Kontrollera också artikel om hantering av åtgärder med dina virtuella datorer.

* [Felsöka fel](backup-azure-vms-troubleshoot.md#restore)
* [Hantera virtuella datorer](backup-azure-manage-vms.md)
