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
ms.openlocfilehash: eb47b1d8d3f2859b2b5c0b79633b2d37e5a40756
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380173"
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Använd Azure portal för att återställa virtuella datorer
Skydda dina data genom att ta ögonblicksbilder av dina data med definierade intervall. De här ögonblicksbilderna kallas återställningspunkter och de lagras i Recovery Services-valv. Om det är nödvändigt att reparera eller återskapa en virtuell dator (VM), kan du återställa den virtuella datorn från någon av de sparade återställningspunkterna. När du återställer en återställningspunkt kan du:

* Skapa en ny virtuell dator, vilket är en point-in-time-representation av den säkerhetskopierade virtuella datorn.
* Återställ diskar, och använda mallen som ingår i processen för att anpassa den återställda virtuella datorn eller göra en enskild fil-återställning. 

Den här artikeln förklarar hur du återställer en virtuell dator till en ny virtuell dator eller återställer alla säkerhetskopierade diskar. Enskilda filåterställning finns [återställa filer från en virtuell Azure-säkerhetskopiering](backup-azure-restore-files-from-vm.md).

![Tre sätt att återställa från säkerhetskopiering av virtuella datorer](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln innehåller information och procedurer som används för att återställa distribuerade virtuella datorer med hjälp av Resource Manager-modellen.
>
>

Återställa en virtuell dator eller alla diskar från VM omfattar backup två steg:

* Välj en återställningspunkt för återställning.
* Välj typ av återställning, skapa en ny virtuell dator eller Återställ diskar och ange de obligatoriska parametrarna. 

## <a name="select-a-restore-point-for-restore"></a>Välj en återställningspunkt för återställning
1. Logga in på [Azure Portal](http://portal.azure.com/).

1. På Azure-menyn, Välj **Bläddra**. I listan över tjänster skriver **återställningstjänster**. Listan över tjänster som anpassar sig till det du skriver. När du ser **Recovery Services-valv**, markera den.

    ![Recovery Services-valv](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Lista över valv i prenumerationen visas.

    ![Listan över Recovery Services-valv](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
1. I listan väljer du valvet som är associerade med den virtuella datorn som du vill återställa. När du väljer valvet öppnas valvets instrumentpanel.

    ![Valda Recovery Services-valv](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
1. I instrumentpanelen för valvet på den **Säkerhetskopieringsobjekt** panelen, väljer **Azure Virtual Machines**.

    ![Instrumentpanelen för valvet](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    Den **Säkerhetskopieringsobjekt** bladet öppnas och visar en lista med virtuella Azure-datorer.

    ![Lista över virtuella datorer i valvet](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
1. Välj en virtuell dator för att öppna instrumentpanelen i listan. VM-instrumentpanelen öppnas på området övervakning, som innehåller den **återställningspunkter** panelen.

    ![Återställningspunkter](./media/backup-azure-arm-restore-vms/vm-blade.png)
1. På menyn VM-instrumentpanelen väljer **återställa**.

    ![Knappen Återställ](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    Den **återställa** blad öppnas.

    ![Återställ blad](./media/backup-azure-arm-restore-vms/restore-blade.png)
1. På den **återställa** bladet väljer **återställningspunkt**. Den **: Välj återställningspunkt** blad öppnas.

    ![Välj återställningspunkt](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    I dialogrutan visas som standard alla återställningspunkter från de senaste 30 dagarna. Använd den **Filter** att ändra tidsintervallet återställningspunkter visas. Som standard visas återställningspunkter för alla konsekvenser. Ändra den **alla återställningspunkter** filter för att välja en specifik återställningspunkt konsekvens. Läs mer om varje typ av återställningspunkt [datakonsekvens](backup-azure-vms-introduction.md#data-consistency).

    Återställ alternativ för konsekvenskontroll av återställningspunkter:

     * Kraschkonsekventa återställningspunkter
     * Programkonsekventa återställningspunkter
     * Filsystemet programkonsekventa återställningspunkter
     * Alla återställningspunkter

1. Välj en återställningspunkt och **OK**.

    ![Återställningsalternativ för återställningspunkt](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    Den **återställa** bladet visar att återställningspunkten har angetts.

1. Om du inte redan är det, går till den **återställa** bladet. Se till att en [återställningspunkt väljs](#select-a-restore-point-for-restore), och välj **återställningskonfiguration**. Den **Återställ konfigurationen** blad öppnas.

## <a name="choose-a-vm-restore-configuration"></a>Välj en konfiguration för återställning av virtuell dator
Välj en konfiguration för återställning av virtuell dator när du har valt återställningspunkten. Du kan använda Azure portal eller PowerShell om du vill konfigurera den återställda virtuella datorn.

1. Om du inte redan är det, går till den **återställa** bladet. Se till att en [återställningspunkt väljs](#select-a-restore-point-for-restore), och välj **återställningskonfiguration**. Den **Återställ konfigurationen** blad öppnas.

    ![Återställa konfigurationsguiden](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
1. På den **Återställ konfigurationen** bladet har du två alternativ:

   * **Skapa virtuell dator**

   * **Återställ diskar**

Portalen ger en **Snabbregistrering** alternativet för en återställd virtuell dator. Använd PowerShell eller portalen för att anpassa VM-konfigurationen eller namnen på de resurser som skapades som en del av ett nytt alternativ för virtuell dator, återställa säkerhetskopierade diskar. Använd PowerShell-kommandon för att koppla dem till ditt val av VM-konfiguration. Du kan också använda mallen som medföljer återställda diskar för att anpassa den återställda virtuella datorn. Information om hur du återställer en virtuell dator som har flera nätverkskort eller omfattas av en belastningsutjämnare finns i [återställa en virtuell dator med särskilda nätverkskonfigurationer](#restore-vms-with-special-network-configurations). Om din virtuella Windows-dator använder [HUB licensiering](../virtual-machines/windows/hybrid-use-benefit-licensing.md), Återställ diskar och använda PowerShell/mall som anges i den här artikeln för att skapa den virtuella datorn. Se till att du anger den **licenstyp** som ”Windows_Server” när du skapar den virtuella datorn för att ta del av HUB fördelar med den återställda virtuella datorn. 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>Skapa en ny virtuell dator från en återställningspunkt
1. Om du inte redan är där [Välj en återställningspunkt](#restore-a vm-with-special-network-configurations) innan du börjar skapa en ny virtuell dator från en återställningspunkt. När du har valt en återställningspunkt på den **Återställ konfigurationen** bladet ange eller Välj värden för var och en av följande fält:

    a. **Återställ typ**. Skapa en virtuell dator.

    b. **Namn på virtuell dator**. Ange ett namn för den virtuella datorn. Namnet måste vara unikt för den resursgrupp (för en Azure Resource Manager-distribuerade virtuella datorn) eller en molntjänst (för en klassisk virtuell dator). Du kan inte ersätta den virtuella datorn om den redan finns i prenumerationen.

    c. **Resursgrupp**. Använd en befintlig resursgrupp eller skapa en ny. Om du återställa en klassisk virtuell dator, kan du använda det här fältet för att ange namnet på en ny molntjänst. Om du skapar en ny resurs eller Molntjänsten, måste namnet vara globalt unikt. Normalt molntjänstens namn är associerad med en offentlig URL: till exempel [cloudservice]. cloudapp.net. Om du försöker använda ett namn för Molntjänsten resource group/i molnet redan används, tilldelar Azure resource och molntjänster med samma namn som den virtuella datorn. Azure visar resource grupper/cloud services och virtuella datorer som inte är associerade med alla tillhörighetsgrupper. Mer information finns i [hur du migrerar från tillhörighetsgrupper till ett regionalt virtuellt nätverk](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Virtuellt nätverk**. Välj det virtuella nätverket när du skapar den virtuella datorn. Fältet innehåller alla virtuella nätverk som är associerade med prenumerationen. Resursgruppen för den virtuella datorn visas inom parentes.

    e. **Undernät**. Om det virtuella nätverket har undernät, väljs det första undernätet som standard. Om det finns ytterligare undernät, väljer du det undernät som du vill.

    f. **Storage-konto**. Den här menyn visar lagringskonton i samma plats som Recovery Services-valvet. Storage-konton som är zonredundant stöds inte. Om det finns inga lagringskonton med samma plats som Recovery Services-valv, måste du skapa en innan du startar återställningen igen. Lagringskontots replikeringstyp visas inom parentes.

    ![Återställa konfigurationsguiden](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * Om du återställer en Resource Manager-distribuerade virtuella datorn måste du identifiera ett virtuellt nätverk. Ett virtuellt nätverk är valfritt för en klassisk virtuell dator.

    > * Om du återställer virtuella datorer med hanterade diskar kan du kontrollera att det valda lagringskontot inte är aktiverad för Azure Storage Service Encryption i dess livslängd.

    > * Baserat på lagringstyp för det valda lagringskontot (premium eller standard), kommer alla diskar som återställts vara premium eller standard-diskar. Vi stöder för närvarande inte en blandat läge för diskar när du återställer.
    >
    >

1. På den **återställningskonfiguration** bladet väljer **OK** att slutföra konfigurationen av återställning. På den **återställa** bladet väljer **återställa** som utlöser återställningen.

## <a name="restore-backed-up-disks"></a>Återställa säkerhetskopierade diskar
Anpassa den virtuella datorn som du vill skapa från säkerhetskopierade diskar skiljer sig från vad som finns i den **Återställ konfigurationen** bladet väljer **Återställ diskar** som värde för **återställa typ**. Det här alternativet begär ett lagringskonto där diskarna från säkerhetskopior som ska kopieras. När du väljer ett lagringskonto kan du välja ett konto som delar samma plats som Recovery Services-valvet. Storage-konton som är zonredundant stöds inte. Om det finns inga lagringskonton med samma plats som Recovery Services-valv, måste du skapa en innan du startar återställningen igen. Lagringskontots replikeringstyp visas inom parentes.

När återställningen är klar kan du:

* [Använda mallen för att anpassa den återställda virtuella datorn](#use-templates-to-customize-restore-vm)
* [Använd de återställda diskarna för att ansluta till en befintlig virtuell dator](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Skapa en ny virtuell dator med hjälp av PowerShell från återställda diskar](./backup-azure-vms-automation.md#restore-an-azure-vm)

På den **återställningskonfiguration** bladet väljer **OK** att slutföra konfigurationen av återställning. På den **återställa** bladet väljer **återställa** som utlöser återställningen.

![Återställningskonfiguration har slutförts](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Spåra återställningen
När du utlösa återställningsåtgärden skapar tjänsten backup ett jobb för att spåra återställningen igen. Tjänsten backup skapar även och visar tillfälligt meddelande i den **meddelanden** området i portalen. Om du inte ser meddelandet, väljer du den **meddelanden** symbol att visa dina meddelanden.

![Återställning utlöses](./media/backup-azure-arm-restore-vms/restore-notification.png)

Visa igen när den bearbetar eller visa den när den är klar öppnar den **säkerhetskopieringsjobb** lista.

1. På Azure-menyn, Välj **Bläddra**, och i listan över tjänster skriver **återställningstjänster**. Listan över tjänster som anpassar sig till det du skriver. När du ser **Recovery Services-valv**, markera den.

    ![Öppna Recovery Services-valv](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Lista över valv i prenumerationen visas.

    ![Listan över Recovery Services-valv](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
1. Välj valvet som är associerade med den virtuella datorn som du har återställt i listan. När du väljer valvet öppnas valvets instrumentpanel.

1. På instrumentpanelen för valvet på den **säkerhetskopieringsjobb** panelen, väljer **Azure-datorer** att visa de jobb som är associerade med valvet.

    ![Instrumentpanelen för valvet](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    Den **säkerhetskopieringsjobb** bladet öppnas och visar en lista över jobb.

    ![Lista över virtuella datorer i ett valv](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>Använd mallar för att anpassa en återställd virtuell dator
Efter den [åtgärden Återställ diskar är klar](#Track-the-restore-operation), med mallen som skapades som en del av återställningen kan du skapa en ny virtuell dator med en konfiguration skiljer sig från konfigurationen för säkerhetskopiering. Du kan också använda det för att anpassa namnen på de resurser som skapades under processen för att skapa en ny virtuell dator från en återställningspunkt. 

> [!NOTE]
> Mallar har lagts till som en del av återställning av diskar för återställningspunkter efter 1 mars 2017. De är tillämpliga för virtuella datorer med icke-hanterade diskar. Stöd för hanterade virtuella kommer i kommande versioner. 
>
>

Hämta den mall som skapades som en del av alternativet Återställ diskar:

1. Gå till återställning jobbinformation för jobbet.

1. På den **återställa jobbinformation** väljer **distribuera mall** initierar malldistribution. 

     ![Återställa jobbet nedåt](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
1. På den **distribuera mallen** bladet för anpassad distribution, Använd malldistribution för att [redigera och distribuera mallen](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) eller lägga till fler anpassningar av [redigera en mall för](../azure-resource-manager/resource-group-authoring-templates.md) innan du distribuerar. 

   ![Läsa in malldistribution](./media/backup-azure-arm-restore-vms/loading-template.png)
   
1. När du har angett värdena som krävs, accepterar den **villkor** och välj **köp**.

   ![Skicka malldistribution](./media/backup-azure-arm-restore-vms/submitting-template.png)

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

   c. Skapa en virtuell dator med [flera nätverkskort](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/).

   d. Skapa en virtuell dator med [flera reserverade IP-adresser](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/).

## <a name="next-steps"></a>Nästa steg
Nu när du kan återställa dina virtuella datorer, se felsökningsartikeln information på vanliga fel med virtuella datorer. Kontrollera också artikel om hantering av åtgärder med dina virtuella datorer.

* [Felsöka fel](backup-azure-vms-troubleshoot.md#restore)
* [Hantera virtuella datorer](backup-azure-manage-vms.md)
