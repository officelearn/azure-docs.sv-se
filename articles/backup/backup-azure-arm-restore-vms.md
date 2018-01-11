---
title: "Azure-säkerhetskopiering: Återställa virtuella datorer med hjälp av Azure portal | Microsoft Docs"
description: "Återställa en virtuell Azure-dator från en återställningspunkt med hjälp av Azure portal"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "återställa en säkerhetskopia. Så här återställer du; återställningspunkten;"
ms.assetid: 372b87c6-3544-4dc5-bbc9-c742ca502159
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: a3b8bb53c467ad6f595a52e2a2e8f805a8f062f6
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="use-the-azure-portal-to-restore-virtual-machines"></a>Använda Azure portal för att återställa virtuella datorer
Skydda dina data genom att ta ögonblicksbilder av data vid angivna intervall. Dessa kallas återställningspunkter och de lagras i Recovery Services-valv. Om det är nödvändigt att reparera eller återskapa en virtuell dator (VM), kan du återställa den virtuella datorn från någon av de sparade återställningspunkterna. När du återställer en återställningspunkt kan du:

* Skapa en ny virtuell dator, som är en tidpunkt i representation av säkerhetskopierade-VM.
* Återställ diskar, och använda en mall som ingår i processen för att anpassa den återställda virtuella datorn eller göra en återställning av enskilda filer. 

Den här artikeln beskriver hur du återställer en virtuell dator till en ny virtuell dator eller återställa alla säkerhetskopierade diskar. Enskilda filåterställning finns [återställa filer från en virtuell dator i Azure-säkerhetskopiering](backup-azure-restore-files-from-vm.md).

![Tre sätt att återställa från en säkerhetskopiering](./media/backup-azure-arm-restore-vms/azure-vm-backup-restore.png)

> [!NOTE]
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln innehåller information och procedurer som används för att återställa distribuerade virtuella datorer med hjälp av Resource Manager-modellen.
>
>

Återställa en virtuell dator eller alla diskar från VM består säkerhetskopiering av två steg:

* Välj en återställningspunkt för återställning.
* Välj typ av återställning, skapa en ny virtuell dator eller återställa diskar och ange de obligatoriska parametrarna. 

## <a name="select-a-restore-point-for-restore"></a>Välj en återställningspunkt för återställning
1. Logga in på [Azure-portalen](http://portal.azure.com/).

2. Välj på menyn Azure **Bläddra**. I listan över tjänster, skriver **återställningstjänster**. Listan över tjänster justerar du anger. När du ser **Recovery Services-valv**, markerar du den.

    ![Recovery Services-valv](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Listan över valv i prenumerationen visas.

    ![Lista över Recovery Services-valv](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
3. Välj valvet som är kopplade till den virtuella datorn som du vill återställa i listan. När du väljer valvet öppnas dess instrumentpanel.

    ![Valda Recovery Services-valvet](./media/backup-azure-arm-restore-vms/select-vault-open-vault-blade.png)
4. I instrumentpanelen för valvet på den **säkerhetskopiering objekt** panelen, väljer **Azure Virtual Machines**.

    ![instrumentpanel för valvet](./media/backup-azure-arm-restore-vms/vault-dashboard.png)

    Den **säkerhetskopiering objekt** blad öppnas och visar en lista med virtuella Azure-datorer.

    ![lista över virtuella datorer i valvet](./media/backup-azure-arm-restore-vms/list-of-vms-in-vault.png)
5. Välj en virtuell dator för att öppna instrumentpanelen i listan. VM-instrumentpanelen öppnas området övervakning, som innehåller den **återställningspunkter** panelen.

    ![Återställningspunkter](./media/backup-azure-arm-restore-vms/vm-blade.png)
6. Välj på menyn VM instrumentpanelen **återställa**.

    ![Knappen Återställ](./media/backup-azure-arm-restore-vms/vm-blade-menu-restore.png)

    Den **återställa** blad öppnas.

    ![återställa bladet](./media/backup-azure-arm-restore-vms/restore-blade.png)
7. På den **återställa** bladet väljer **återställningspunkt**. Den **Välj återställningspunkt** blad öppnas.

    ![Välj återställningspunkt](./media/backup-azure-arm-restore-vms/recovery-point-selector.png)

    I dialogrutan visas som standard alla återställningspunkter från de senaste 30 dagarna. Använd den **Filter** att ändra tidsintervallet återställningspunkter visas. Återställningspunkter för alla consistencies visas som standard. Ändra den **alla återställningspunkter** filter för att välja en specifik återställning punkt konsekvenskontroll. Mer information om varje typ av återställning punkt finns [datakonsekvens](backup-azure-vms-introduction.md#data-consistency).

    Återställ konsekvenskontroll alternativ:

     * Kraschkonsekventa återställningspunkter
     * Programkonsekventa återställningspunkter
     * Filsystemet programkonsekventa återställningspunkter
     * Alla återställningspunkter

8. Välj en återställningspunkt och **OK**.

    ![Återställa alternativ](./media/backup-azure-arm-restore-vms/select-recovery-point.png)

    Den **återställa** bladet visar att återställningspunkten har angetts.

9. Om du inte redan har det, går till den **återställa** bladet. Se till att en [återställningspunkt väljs](#select-restore-point-for-restore), och välj **Återställ konfiguration**. Den **Återställ konfiguration** blad öppnas.

## <a name="choose-a-vm-restore-configuration"></a>Välj en återställning VM-konfiguration
När du har valt återställningspunkten du Välj en konfiguration för återställning av VM. Du kan använda Azure-portalen eller PowerShell om du vill konfigurera den återställda virtuella datorn.

1. Om du inte redan har det, går till den **återställa** bladet. Se till att en [återställningspunkt väljs](#select-restore-point-for-restore), och välj **Återställ konfiguration**. Den **Återställ konfiguration** blad öppnas.

    ![Återställa konfigurationsguiden](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard-recovery-type.png)
2. På den **Återställ konfiguration** bladet har du två alternativ:

   * **Skapa virtuell dator**

   * **Återställa diskar**

Portalen innehåller en **Snabbregistrering** alternativ för en återställd VM. Använd PowerShell eller portalen för att återställa säkerhetskopierade diskar för att anpassa VM-konfiguration eller namnen på de resurser som skapades som en del av att skapa ett nytt VM-alternativ. Använd PowerShell-kommandon för att ansluta dem till ditt val av VM-konfiguration. Eller så kan du använda mallen som medföljer återställda diskar för att anpassa den återställda virtuella datorn. Information om hur du återställer en virtuell dator som har flera nätverkskort eller under en belastningsutjämnare finns [återställa en virtuell dator med särskilda nätverkskonfigurationer](#restore-a vm-with-special-network-configurations). Om din Windows virtuell dator använder [hubb licensiering](../virtual-machines/windows/hybrid-use-benefit-licensing.md)återställer diskar och använda PowerShell mallen som anges i den här artikeln för att skapa den virtuella datorn. Se till att du anger den **licenstypen** som ”Windows_Server” när du skapar den virtuella datorn att använda hubb fördelar på den återställda virtuella datorn. 
 
## <a name="create-a-new-vm-from-a-restore-point"></a>Skapa en ny virtuell dator från en återställningspunkt
1. Om du inte redan, [Välj en återställningspunkt](#restore-a vm-with-special-network-configurations) innan du börjar skapa en ny virtuell dator från en återställningspunkt. När du har valt en återställningspunkt på den **Återställ konfiguration** bladet ange eller Välj värden för var och en av följande fält:

    a. **Återställa**. Skapa en virtuell dator.

    b. **Namn på virtuell dator**. Ange ett namn för den virtuella datorn. Namnet måste vara unikt för resursgrupp (för en Azure Resource Manager-distribuerad virtuell dator) eller tjänst i molnet (för en klassisk virtuell dator). Du kan ersätta den virtuella datorn om det finns redan i prenumerationen.

    c. **Resursgruppen**. Använd en befintlig resursgrupp eller skapa en ny. Om du återställa en klassisk virtuell dator, kan du använda det här fältet för att ange namnet på en ny molntjänst. Om du skapar en ny grupp/molntjänst för resursen, måste namnet vara globalt unika. Molntjänstnamnet är vanligtvis kopplad till en offentlig URL-adress: till exempel [cloudservice]. cloudapp.net. Om du försöker använda ett namn för Molntjänsten resurs grupp eller ett moln som redan används, tilldelar Azure resource/molntjänst med samma namn som den virtuella datorn. Azure visar resurs grupper/molntjänster och virtuella datorer som inte är associerad med någon tillhörighetsgrupper. Mer information finns i [hur du migrerar från tillhörighetsgrupper till ett regionalt virtuellt nätverk](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

    d. **Virtuellt nätverk**. Välj det virtuella nätverket när du skapar den virtuella datorn. Fältet innehåller alla virtuella nätverk som är associerade med prenumerationen. Resursgruppen för den virtuella datorn visas inom parentes.

    e. **Undernät**. Om det virtuella nätverket har undernät, är det första undernätet markerad som standard. Om det finns ytterligare undernät, kan du välja det undernät som du vill.

    f. **Lagringskontot**. Den här menyn innehåller storage-konton på samma plats som Recovery Services-valvet. Storage-konton som är zonredundant stöds inte. Om det finns inga lagringskonton med samma plats som Recovery Services-valvet, måste du skapa en innan du startar återställningen. Storage-konto replikeringstyp visas inom parentes.

    ![Återställa konfigurationsguiden](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard.png)

    > [!NOTE]
    > * Om du återställer en Resource Manager-distribuerad virtuell dator måste du identifiera ett virtuellt nätverk. Ett virtuellt nätverk är valfri för en klassisk virtuell dator.

    > * Om du återställer virtuella datorer med hanterade diskar, se till att lagringskonto som valts inte är aktiverat för Azure Storage Service-kryptering i dess livslängd.

    > * Baserat på lagringstyp för det lagringskonto som valts (premium eller standard), är alla diskar som återställts premium eller standarddiskar. För närvarande stöds inte blandat läge av diskar när du återställer.
    >
    >

2. På den **Återställ konfiguration** bladet väljer **OK** att slutföra konfigurationen av återställningen. På den **återställa** bladet väljer **återställa** som utlöser återställningen.

## <a name="restore-backed-up-disks"></a>Återställa säkerhetskopierade diskar
Anpassa den virtuella datorn som du vill skapa från säkerhetskopierade diskar än vad som finns i den **Återställ konfiguration** bladet väljer **hårddiskar** som värde för **återställa typen**. Det här alternativet begär ett lagringskonto där diskar från säkerhetskopior som ska kopieras. När du väljer ett lagringskonto kan du välja ett konto som delar samma plats som Recovery Services-valvet. Storage-konton som är zonredundant stöds inte. Om det finns inga lagringskonton med samma plats som Recovery Services-valvet, måste du skapa en innan du startar återställningen. Storage-konto replikeringstyp visas inom parentes.

När återställningen är klar, kan du:

* [Med mallen kan du anpassa den återställda virtuella datorn](#use-templates-to-customize-restore-vm)
* [Använda återställda diskar för att ansluta till en befintlig virtuell dator](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Skapa en ny virtuell dator med hjälp av PowerShell från återställda diskar](./backup-azure-vms-automation.md#restore-an-azure-vm)

På den **Återställ konfiguration** bladet väljer **OK** att slutföra konfigurationen av återställningen. På den **återställa** bladet väljer **återställa** som utlöser återställningen.

![Återställningskonfiguration slutfördes](./media/backup-azure-arm-restore-vms/trigger-restore-operation.png)

## <a name="track-the-restore-operation"></a>Spåra återställningen
När du utlösa återställningen skapas säkerhetskopieringstjänsten ett jobb för spårning av återställningen. Säkerhetskopieringstjänsten skapar även och visar tillfälligt meddelandet i den **meddelanden** område i portalen. Om du inte ser meddelandet, väljer du den **meddelanden** symbol att visa meddelanden.

![Återställning utlöses](./media/backup-azure-arm-restore-vms/restore-notification.png)

Visa åtgärden medan den bearbetar eller visa den när den är klar öppnar du den **säkerhetskopiera jobb** lista.

1. Välj på menyn Azure **Bläddra**, och Skriv i listan över tjänster, **återställningstjänster**. Listan över tjänster justerar du anger. När du ser **Recovery Services-valv**, markerar du den.

    ![Öppna Recovery Services-valvet](./media/backup-azure-arm-restore-vms/open-recovery-services-vault.png)

    Listan över valv i prenumerationen visas.

    ![Lista över Recovery Services-valv](./media/backup-azure-arm-restore-vms/list-of-rs-vaults.png)
2. Välj valvet som är kopplade till den virtuella datorn du har återställt i listan. När du väljer valvet öppnas dess instrumentpanel.

3. I valvet instrumentpanelen på den **säkerhetskopieringsjobb** panelen, väljer **virtuella Azure-datorer** att visa jobb som är kopplad till valvet.

    ![instrumentpanel för valvet](./media/backup-azure-arm-restore-vms/vault-dashboard-jobs.png)

    Den **säkerhetskopiera jobb** blad öppnas och visar listan över jobb.

    ![Lista över virtuella datorer i ett valv](./media/backup-azure-arm-restore-vms/restore-job-in-progress.png)
    
## <a name="use-templates-to-customize-a-restored-vm"></a>Använda mallar för att anpassa en återställd VM
Efter den [diskar återställningen är klar](#Track-the-restore-operation), med mallen som skapades som en del av återställningen kan du skapa en ny virtuell dator med en konfiguration skiljer sig från konfigurationen för säkerhetskopiering. Du kan också använda den för att anpassa namnen på de resurser som skapades under processen med att skapa en ny virtuell dator från en återställningspunkt. 

> [!NOTE]
> Mallar läggs till som en del av återställningen diskar för återställningspunkter efter 1 mars 2017. De är tillämpliga för icke-hanterade disken virtuella datorer. Stöd för hanterade diskar virtuella datorer kommer i kommande versioner. 
>
>

Hämta den mall som skapades som en del av alternativet Återställ diskar:

1. Gå till Jobbinformationen återställning motsvarar jobbet.

2. På den **återställa jobbinformation** väljer **distribuera mallen** initierar malldistribution. 

     ![återställa jobbet nedåt](./media/backup-azure-arm-restore-vms/restore-job-drill-down.png)
   
3. På den **distribuera mallen** bladet för anpassad distribution, Använd malldistribution till [redigera och distribuera mallen](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) eller lägga till fler anpassningar av [redigera en mall för](../azure-resource-manager/resource-group-authoring-templates.md) innan du distribuerar. 

   ![Läsa in malldistribution](./media/backup-azure-arm-restore-vms/loading-template.png)
   
4. När du anger nödvändiga värden måste acceptera den **villkor** och välj **inköp**.

   ![Skicka malldistribution](./media/backup-azure-arm-restore-vms/submitting-template.png)

## <a name="post-restore-steps"></a>Efter återställning steg
* Om du använder en init-molnbaserade Linux-distribution, till exempel Ubuntu, av säkerhetsskäl lösenordet blockeras efter återställningen. Använder VMAccess-tillägget på den återställda virtuella datorn till [återställa lösenordet](../virtual-machines/linux/classic/reset-access.md). Vi rekommenderar att du använder SSH-nycklar på dessa distributioner för att undvika att återställa lösenordet efter återställningen.
* Tillägg finns under konfigurationen för säkerhetskopiering är installerade, men de kommer inte aktiveras. Om du ser ett problem kan du installera om tilläggen. 
* Om den säkerhetskopierade virtuella datorn har statiska IP-efter återställning, har den återställda virtuella datorn en dynamisk IP-adress att undvika konflikter när du skapar en återställd VM. Mer information om hur du kan [lägga till en statisk IP-adress till en återställd VM](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
* En återställd VM har inte en tillgänglighet värdet. Vi rekommenderar att du använder alternativ för återställning-diskar till [lägga till en tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) när du skapar en virtuell dator från PowerShell eller mallar med hjälp av återställts diskar. 


## <a name="backup-for-restored-vms"></a>Säkerhetskopiering för virtuella datorer som har återställts
Om du återställer en virtuell dator till samma grupp med samma namn som den virtuella datorn ursprungligen säkerhetskopierade fortsätter säkerhetskopiering på VM efter återställningen. Om du har återställt den virtuella datorn till en annan resursgrupp eller du har angett ett annat namn för den återställda virtuella datorn, hanteras den virtuella datorn som om det är en ny virtuell dator. Du måste konfigurera säkerhetskopiering för den återställda virtuella datorn.

## <a name="restore-a-vm-during-an-azure-datacenter-disaster"></a>Återställa en virtuell dator under en Azure-datacenter-katastrofåterställning
Azure Backup kan återställning av säkerhetskopierade virtuella datorer till parad datacentret om en katastrof inträffar i det primära datacentrat där virtuella datorer som körs och du har konfigurerat säkerhetskopieringsvalvet ska geo-redundant. Välj ett lagringskonto som finns i en parad datacenter under dessa scenarier. Resten av återställningsprocessen är densamma. Säkerhetskopiering används tjänsten beräkning från parad geo för att skapa den återställda virtuella datorn. Mer information finns i [Azure-datacenter återhämtning](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).

## <a name="restore-domain-controller-vms"></a>Återställa domänkontrollant virtuella datorer
Säkerhetskopiering av en domänkontrollant (DC) är ett scenario som stöds med säkerhetskopiering för virtuella datorer. Du måste dock vara försiktig under återställningsprocessen. Med rätt återställningsprocessen beror på strukturen för domänen. I det enklaste fallet har du en Domänkontrollant i en domän. Ofta för produktion belastningar ha du en enda domän med flera domänkontrollanter, exempelvis med vissa domänkontrollanter lokalt. Slutligen kan du ha en skog med flera domäner. 

Från ett Active Directory-perspektiv är Azure VM som andra Virtuella på en modern hypervisor som stöds. Med lokala hypervisorer största skillnaden är att det finns ingen VM-konsol i Azure. En konsol krävs för vissa scenarier, till exempel återställning med hjälp av en bare metal recovery (BMR)-typen säkerhetskopiering. Återställning av virtuell dator från valvet är dock en fullständig ersättning för BMR. Directory återställningsläge för katalogtjänster (DSRM) är också tillgängliga, så att alla Active Directory-återställningsscenarier är användbart. Mer information finns i [överväganden för säkerhetskopiering och återställning för virtualiserade domänkontrollanter](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) och [planering för Active Directory-skogsåterställning](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Domänkontrollant i en domän
Den virtuella datorn kan återställas (som andra Virtuella) från Azure-portalen eller med hjälp av PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Flera domänkontrollanter i en domän
När andra domänkontrollanter i samma domän kan nås över nätverket, kan du återställa domänkontrollanten som någon virtuell dator. Om det är den sista återstående domänkontrollanten i domänen, eller en återställning i ett isolerat nätverk utförs, måste en skog recovery procedur följas.

### <a name="multiple-domains-in-one-forest"></a>Flera domäner i en skog
När andra domänkontrollanter i samma domän kan nås över nätverket, kan du återställa domänkontrollanten som någon virtuell dator. I andra fall rekommenderar vi en skogsåterställning.

## <a name="restore-vms-with-special-network-configurations"></a>Återställa virtuella datorer med särskilda nätverkskonfigurationer
Det är möjligt att säkerhetskopiera och återställa virtuella datorer med följande särskilda nätverkskonfigurationer. Dessa konfigurationer kräver dock vissa särskilda överväganden när du går igenom återställningsprocessen:

* Virtuella datorer under belastningsutjämnare (interna och externa)
* Virtuella datorer med flera reserverade IP:
* Virtuella datorer med flera nätverkskort

> [!IMPORTANT]
> När du skapar särskilda nätverkskonfigurationen för virtuella datorer, måste du använda PowerShell för att skapa virtuella datorer från de återställda diskarna.
>
>

Följ dessa steg för att fullständigt återskapa de virtuella datorerna när du återställer till disk:

1. Återställ diskar från en Recovery Services-valvet med hjälp av [PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

2. Skapa VM-konfiguration som krävs för belastningsutjämning eller flera nätverkskort/flera reserverade IP-Adressen med hjälp av PowerShell-cmdlets. Du kan använda den för att skapa den virtuella datorn med den konfiguration som du vill:

   a. Skapa en virtuell dator i Molntjänsten med en [intern belastningsutjämnare](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/).

   b. Skapa en virtuell dator att ansluta till en [Internetriktade belastningsutjämnare](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/).

   c. Skapa en virtuell dator med [flera nätverkskort](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/).

   d. Skapa en virtuell dator med [flera reserverade IP-adresser](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/).

## <a name="next-steps"></a>Nästa steg
Nu när du återställer din virtuella dator finns i Felsökning artikeln information om vanliga fel med virtuella datorer. Se även artikel om hur du hanterar uppgifter med dina virtuella datorer.

* [Felsökning av fel](backup-azure-vms-troubleshoot.md#restore)
* [Hantera virtuella datorer](backup-azure-manage-vms.md)
