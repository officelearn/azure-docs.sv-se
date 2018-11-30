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
ms.openlocfilehash: 0d78ae294cea383fbe59a1f7968d8bf18b1942d1
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422964"
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

    (a) högerklickar du på återställningspunkt i det här bladet (mindre än 30 dagar) och initiera **återställa en virtuell dator**.

    (b) om du vill återställa recovery pekar fler än 30 dagar Klicka här angavs i bladet kan användas.

    c) **återställa en virtuell dator** på menyn huvudet innehåller ett alternativ för att visa och filtrera de virtuella datorerna i anpassade datum som önskade.

    Använd filtret för att ändra tidsintervallet återställningspunkter visas. Som standard visas återställningspunkter för alla konsekvenser. Ändra filtret återställningspunkter för att välja en specifik återställningspunkt konsekvens. Läs mer om varje typ av återställningspunkt [datakonsekvens](backup-azure-vms-introduction.md#data-consistency).

    Återställ alternativ för konsekvenskontroll av återställningspunkter:
    - Kraschkonsekventa återställningspunkter
    - Programkonsekventa återställningspunkter
    - Filsystemet programkonsekventa återställningspunkter
    - Alla återställningspunkter

  ![Återställningspunkter](./media/backup-azure-arm-restore-vms/vm-blade1.png)

    >  [!NOTE]
    > Typ av återställning representerar om den tillhör kundens lagringskonto, i valvet eller båda. Läs mer om [omedelbar återställningspunkt](https://azure.microsoft.com/blog/large-disk-support/).

8. På den **återställa** bladet väljer **återställningspunkt**.

    ![Välj återställningspunkt](./media/backup-azure-arm-restore-vms/select-recovery-point1.png)

    Den **återställa** bladet visar att återställningspunkten anges när du klickar på **OK**.
9. Om du inte redan är det, går till den **återställa** bladet. Se till att en [återställningspunkt väljs](#select-a-restore-point-for-restore), och välj **återställningskonfiguration**. Den **Återställ konfigurationen** blad öppnas.

## <a name="choose-a-vm-restore-configuration"></a>Välj en konfiguration för återställning av virtuell dator
Välj en konfiguration för återställning av virtuell dator när du har valt återställningspunkten. Du kan använda Azure portal eller PowerShell om du vill konfigurera den återställda virtuella datorn.

1. Om du inte redan är det, går till den **återställa** bladet. Se till att en [återställningspunkt väljs](#select-a-restore-point-for-restore), och välj **återställningskonfiguration**. Den **Återställ konfigurationen** blad öppnas.
2. Det här bladet för närvarande har två alternativ för en som **Skapa ny** som är standard och det andra är **ersätta befintliga** som är på plats återställning för att ersätta diskarna bara behålla de befintliga konfigurationerna -tillägg.

> [!NOTE]
> Vi arbetar på att ersätta en hel virtuell dator med diskarna, nätverksinställningar, konfigurationer och tillägg i de kommande månaderna.
>
>

 I den **Skapa ny** vilket återställer data till ny virtuell dator eller nya diskar, har du två alternativ:

 ![Återställa konfigurationsguiden](./media/backup-azure-arm-restore-vms/restore-configuration-create-new.png)

 - **Skapa virtuell dator**
 - **Återställ diskar**

![Återställa konfigurationsguiden](./media/backup-azure-arm-restore-vms/restore-configuration-create-new1.png)

Portalen ger en **Snabbregistrering** alternativet för en återställd virtuell dator. Använd PowerShell eller portalen för att anpassa VM-konfigurationen eller namnen på de resurser som skapades som en del av ett nytt alternativ för virtuell dator, återställa säkerhetskopierade diskar. Använd PowerShell-kommandon för att koppla dem till ditt val av VM-konfiguration. Du kan också använda mallen som medföljer återställda diskar för att anpassa den återställda virtuella datorn. Information om hur du återställer en virtuell dator som har flera nätverkskort eller omfattas av en belastningsutjämnare finns i [återställa en virtuell dator med särskilda nätverkskonfigurationer](#restore-vms-with-special-network-configurations). Om din virtuella Windows-dator använder [HUB licensiering](../virtual-machines/windows/hybrid-use-benefit-licensing.md), Återställ diskar och använda PowerShell/mall som anges i den här artikeln för att skapa den virtuella datorn. Se till att du anger den **licenstyp** som ”Windows_Server” när du skapar den virtuella datorn för att ta del av HUB fördelar med den återställda virtuella datorn. Obs detta kan göras senare när du har skapat för virtuell dator också.

## <a name="create-a-new-vm-from-a-restore-point"></a>Skapa en ny virtuell dator från en återställningspunkt
1. På den **Återställ konfigurationen** bladet som nämns i den innan du avsnittet Ange eller Välj värden för var och en av följande fält:

    a. **Återställ typ**. Skapa en virtuell dator.

    b. **Namn på virtuell dator**. Ange det namn på virtuell dator som inte finns i prenumerationen.

    c. **Resursgrupp**. Använd en befintlig resursgrupp eller skapa en ny. Om du återställa en klassisk virtuell dator, kan du använda det här fältet för att ange namnet på en ny molntjänst. Om du skapar en ny resurs eller Molntjänsten, måste namnet vara globalt unikt. Normalt molntjänstens namn är associerad med en offentlig URL: till exempel [cloudservice]. cloudapp.net. Om du försöker använda ett namn för Molntjänsten resource group/i molnet redan används, tilldelar Azure resource och molntjänster med samma namn som den virtuella datorn. Azure visar resource grupper/cloud services och virtuella datorer som inte är associerade med alla tillhörighetsgrupper. Mer information finns i [hur du migrerar från tillhörighetsgrupper till ett regionalt virtuellt nätverk](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Virtuellt nätverk**. Välj det virtuella nätverket när du skapar virtuell dator. Fältet innehåller alla virtuella nätverk som är associerade med prenumerationen. Resursgruppen för den virtuella datorn visas inom parentes.

    e. **Undernät**. Om det virtuella nätverket har undernät, väljs det första undernätet som standard. Om det finns ytterligare undernät, väljer du det undernät som du vill.

    f. **Lagringsplats**. Storage-konton är mellanlagringsplatsen. Den här menyn visar lagringskonton i samma plats som Recovery Services-valvet. Storage-konton som är zonredundant stöds inte. Om det finns inga lagringskonton med samma plats som Recovery Services-valv, måste du skapa en innan du startar återställningen igen. Lagringskontots replikeringstyp visas inom parentes.

    ![Återställa konfigurationsguiden](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

    > [!NOTE]
    > * Ett virtuellt nätverk är valfritt för en klassisk virtuell dator och obligatorisk för Resource Manager-distribuerade virtuella datorn.

    > * Lagring skriver anges i storage-konto (premium eller standard) i mellanlagringsplatsen bestämmer sig för typ av återställning disklagring. Vi stöder för närvarande inte en blandat läge för diskar när du återställer.
    >
    >

2. På den **återställningskonfiguration** bladet väljer **OK** att slutföra konfigurationen av återställning. På den **återställa** bladet väljer **återställa** som utlöser återställningen.

## <a name="restore-backed-up-disks"></a>Återställa säkerhetskopierade diskar
Återställa TYPVÄRDE **Återställ disk** i **Återställ konfigurationen** bladet gör det möjligt för att skapa en virtuell dator med anpassade konfigurationer. Vid återställning av diskar, ska Storage-konto som ska väljas vara på samma plats som Recovery services-valv. Det är obligatoriskt att skapa ett storage-konto om det finns inga lagringskonton med samma plats som Recovery Services-valvet. ZRS-lagringskonton stöds inte. Typ av replikering för Storage-konto visas inom parentes.

Efter återställningen, Använd nedan:
* [Använda mallen för att anpassa den återställda virtuella datorn](#use-templates-to-customize-restore-vm)
* [Använd de återställda diskarna för att ansluta till en befintlig virtuell dator](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Skapa en ny virtuell dator med hjälp av PowerShell från återställda diskar](./backup-azure-vms-automation.md#restore-an-azure-vm)

På den **återställningskonfiguration** bladet väljer **OK** att slutföra konfigurationen av återställning. På den **återställa** bladet väljer **återställa** som utlöser återställningen.

![Återställningskonfiguration har slutförts](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

I **plats Återställ** görs via fliken **ersätter befintliga**.

## <a name="replace-existing-disks-from-a-restore-point"></a>Byta ut befintliga diskar från en återställningspunkt
**Ersätt befintliga** alternativet hjälper dig att byta ut befintliga diskar i den aktuella virtuella datorn med den valda återställningspunkten. Den här åtgärden kan utföras endast om aktuella virtuella datorn finns. Den här åtgärden kan inte utföras om det togs bort på grund av eventuella orsaker; Du kan också rekommenderar vi att du gör **Skapa nytt** VM eller diskar för att fortsätta med återställningsåtgärder. Under den här åtgärden i förebyggande syfte, säkerhetskopiera vi data innan du påbörjar Ersätt diskar operations. Detta skapar en ögonblicksbild och en återställningspunkt i valvet med kvarhållningsperiod som har angetts i konfigurerade principen för säkerhetskopiering. Om återställningspunkten har diskar som är mer/mindre än den aktuella virtuella datorn och sedan antalet diskar i återställningspunkten kommer endast att användas i den virtuella datorn. **Ersätt befintliga** alternativet är för närvarande inte stöd för ohanterade diskar och krypterade virtuella datorer. Det är också stöds inte för [generaliserad virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) och för virtuella datorer som skapas med hjälp av [anpassade avbildningar](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).  

 På den **återställa konfigurationen** bladet endast indata som måste väljas är **mellanlagringsplatsen**.

   ![Återställa ersätter befintliga konfigurationsguiden](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

 a. **Återställ typ**. Ersätt diskar som representerar den valda återställningspunkten ersätts diskar i befintlig virtuell dator.

 b. **Mellanlagringsplatsen**. Storage-konton är mellanlagringsplatsen för hanterade diskar. Den här menyn visar lagringskonton i samma plats som Recovery Services-valvet. Storage-konton som är zonredundant stöds inte. Om det finns inga lagringskonton med samma plats som Recovery Services-valv, måste du skapa en innan du startar återställningen igen. Lagringskontots replikeringstyp visas inom parentes.

## <a name="track-the-restore-operation"></a>Spåra återställningen
När du utlösa återställningsåtgärden skapar tjänsten backup ett jobb för att spåra återställningen igen. Tjänsten backup skapar även och visar tillfälligt meddelande i den **meddelanden** området i portalen. Om du inte ser meddelandet, väljer du den **meddelanden** symbol att visa dina meddelanden.

![Återställning utlöses](./media/backup-azure-arm-restore-vms/restore-notification1.png)

Klicka på länken för att gå till meddelanden **BackupJobs** lista. All information på åtgärder för ett visst jobb finns i den **BackupJobs** visas. Du kan gå till **BackupJobs** från instrumentpanelen för valvet genom att klicka på den säkerhetskopieringsjobb panelen, väljer **Azure-dator** att visa de jobb som är associerade med valvet.

Den **säkerhetskopieringsjobb** bladet öppnas och visar en lista över jobb.

![Lista över virtuella datorer i ett valv](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

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
