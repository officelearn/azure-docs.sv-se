---
title: "Återställa en virtuella datorer från en säkerhetskopia | Microsoft Docs"
description: "Lär dig hur du återställer en virtuell Azure-dator från en återställningspunkt"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "återställa en säkerhetskopia. Så här återställer du; återställningspunkten;"
ms.assetid: fed877b3-b496-49fb-99e4-653be7c23e3a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: trinadhk; jimpark;
ms.openlocfilehash: fc52c909df5e91741ec1fa21fb911487be039fdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="restore-virtual-machines-in-azure"></a>Återställa virtuella datorer i Azure
> [!div class="op_single_selector"]
> * [Återställa virtuella datorer i Azure-portalen](backup-azure-arm-restore-vms.md)
> * [Återställa virtuella datorer i klassisk portal](backup-azure-restore-vms.md)
>
>

Återställa en virtuell dator till en ny virtuell dator från säkerhetskopior som lagras i ett Azure Backup-valv med följande steg.

> [!IMPORTANT]
> Nu kan du uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Mer information finns i artikeln [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Uppgradera ett säkerhetskopieringsvalv till ett Recovery Services-valv). Microsoft rekommenderar att du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valv.<br/> **Den 15 oktober 2017** kan du inte längre använda PowerShell för att skapa säkerhetskopieringsvalv. <br/> **Från den 1 november 2017**:
>- Alla återstående säkerhetskopieringsvalv uppgraderas automatiskt till Recovery Services-valv.
>- Du kan inte längre komma åt dina säkerhetskopierade data i den klassiska portalen. Använd i stället Azure Portal till att få åtkomst till dina säkerhetskopierade data i Recovery Services-valv.
>

## <a name="restore-workflow"></a>Återställ arbetsflöde
### <a name="step-1-choose-an-item-to-restore"></a>Steg 1: Välj ett objekt om du vill återställa
1. Navigera till den **skyddade objekt** fliken och markera den virtuella datorn som du vill återställa till en ny virtuell dator.

    ![Skyddade objekt](./media/backup-azure-restore-vms/protected-items.png)

    Den **återställningspunkt** kolumnen i den **skyddade objekt** sidan informerar dig antalet återställningspunkter för en virtuell dator. Den **nyaste återställningspunkt** kolumnen visar tidpunkten för den senaste säkerhetskopian som en virtuell dator kan återställas.
2. Klicka på **återställa** att öppna den **återställa ett objekt** guiden.

    ![Återställa ett objekt](./media/backup-azure-restore-vms/restore-item.png)

### <a name="step-2-pick-a-recovery-point"></a>Steg 2: Välj en återställningspunkt
1. I den **Välj en återställningspunkt** skärmen, du kan återställa från den senaste återställningspunkten, eller från en tidigare punkt i tiden. Standardalternativet markerad när guiden öppnar *nyaste återställningspunkt*.

    ![Välj en återställningspunkt](./media/backup-azure-restore-vms/select-recovery-point.png)
2. Om du vill välja en tidigare punkt i tiden, Välj den **Välj datum** alternativ i listrutan och välj ett datum i kalendern genom att klicka på den **Kalender-ikonen**. Alla datum som har återställningspunkter fylls med en ljus grå skugga för kontrollen och är valbara av användaren.

    ![Välj ett datum](./media/backup-azure-restore-vms/select-date.png)

    När du klickar på ett datum i kalendern återställningspunkter tillgängliga på att datum ska visas i återställningspunkter nedan. Den **tid** kolumnen anger den tidpunkt då ögonblicksbilden togs. Den **typen** visar kolumnen den [konsekvenskontroll](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points) för återställningspunkten. Rubrikrad visar antalet tillgängliga återställningspunkter på den dagen inom parentes.

    ![Återställningspunkter](./media/backup-azure-restore-vms/recovery-points.png)
3. Välj återställningspunkten från den **återställningspunkter** tabell och klicka på pilen Nästa för att gå till nästa sida.

### <a name="step-3-specify-a-destination-location"></a>Steg 3: Ange en målplats
1. I den **Välj återställa instans** skärmen Ange information om var du vill återställa den virtuella datorn.

   * Ange namnet på virtuella datorn: namnet på virtuella datorn i en viss molntjänst ska vara unika. Vi stöder inte skriva över befintliga VM.
   * Välj en molntjänst för den virtuella datorn: Detta är obligatoriskt för att skapa en virtuell dator. Du kan välja att använda en befintlig molntjänst eller skapa en ny molntjänst.

        Oavsett molntjänstnamnet är utvald ska vara globalt unika. Normalt molntjänstnamnet hämtar associerade med en offentlig URL i formatet [cloudservice]. cloudapp.net. Azure kan inte skapa en ny molntjänst om namnet har redan använts. Om du vill skapa en ny molntjänst, får den samma namn som den virtuella datorn – fall på det Virtuella datornamnet plockats ska vara unikt ska tillämpas på den associera Molntjänsten.

        Vi bara att visa molntjänster och virtuella nätverk som inte är associerade med alla tillhörighetsgrupper i instansinformation för återställning. [Lär dig mer](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
2. Välj ett lagringskonto för den virtuella datorn: Detta är obligatoriskt för att skapa den virtuella datorn. Du kan välja från befintliga lagringskonton i samma region som Azure Backup-valvet. Storage-konton som är zonen redundant eller Premium-lagring stöds inte.

    Om det finns ingen storage-konton med konfigurationer som stöds kan skapa ett lagringskonto konfiguration som stöds innan du startar återställningen.

    ![Välj ett virtuellt nätverk](./media/backup-azure-restore-vms/restore-sa.png)
3. Välj ett virtuellt nätverk: virtuella nätverk (VNET) för den virtuella datorn ska väljas vid tidpunkten för att skapa den virtuella datorn. Återställningen Användargränssnittet visar alla Vnet i den här prenumerationen som kan användas. Det är inte obligatoriskt att välja ett virtuellt nätverk för den återställda virtuella datorn – du kommer att kunna ansluta till den återställda virtuella datorn via internet, även om det virtuella nätverket inte har tillämpats.

    Om den molntjänst som valts är associerad med ett virtuellt nätverk kan ändra du inte det virtuella nätverket.

    ![Välj ett virtuellt nätverk](./media/backup-azure-restore-vms/restore-cs-vnet.png)
4. Välj ett undernät: VNET har undernät, om det första undernätet kommer väljas som standard. Välj undernätet önskat alternativ i listrutan. För information om undernät, gå till nätverk tillägget på den [portalens startsida](https://manage.windowsazure.com/), gå till **virtuella nätverk** och markera det virtuella nätverket och öka detaljnivån konfigurera om du vill se information om undernät.

    ![Välj ett undernät](./media/backup-azure-restore-vms/select-subnet.png)
5. Klicka på den **skicka** ikon i guiden för att skicka informationen och skapa en återställningsjobbet.

## <a name="track-the-restore-operation"></a>Spåra återställningen
När du har indata all information i guiden Återställning och skicka den görs Azure Backup försök att skapa ett jobb för att spåra återställningen.

![Skapar ett jobb för återställning](./media/backup-azure-restore-vms/create-restore-job.png)

Om jobbet skapandet lyckas visas ett popup-meddelande meddelande om att jobbet har skapats. Du kan få mer information genom att klicka på den **visa jobb** knapp som tar dig till **jobb** fliken.

![Återställningsjobbet har skapats](./media/backup-azure-restore-vms/restore-job-created.png)

När återställningen är klar, den kommer att markeras som slutförda i **jobb** fliken.

![Återställningsjobbet har slutförts](./media/backup-azure-restore-vms/restore-job-complete.png)

När du återställer den virtuella datorn du kan behöva installera tillägg finns på den ursprungliga virtuella datorn och [ändra slutpunkterna](../virtual-machines/windows/classic/setup-endpoints.md) för den virtuella datorn i Azure-portalen.

## <a name="post-restore-steps"></a>Efter återställning steg
Om du använder en moln-init-baserade Linux-distribution, till exempel Ubuntu, av säkerhetsskäl lösenord kommer att blockeras efter återställningen. Använd VMAccess-tillägget på den återställda virtuella datorn till [återställa lösenordet](../virtual-machines/linux/classic/reset-access.md). Vi rekommenderar att du använder SSH-nycklar på dessa distributioner för att undvika att återställa lösenordet efter återställning.

## <a name="backup-for-restored-vms"></a>Säkerhetskopiering för virtuella datorer som har återställts
Om du har återställt VM till samma molntjänst med samma namn som det ursprungligen säkerhetskopierades från VM, fortsätter säkerhetskopiering på VM efter återställningen. Om du har återställt VM till en annan molntjänst eller ange ett annat namn för den återställda virtuella datorn, kommer att behandlas som en ny virtuell dator och du behöver installationsprogrammet säkerhetskopiering för den återställda virtuella datorn.

## <a name="restoring-a-vm-during-azure-datacenter-disaster"></a>Återställa en virtuell dator under Azure DataCenter-katastrofåterställning
Azure Backup kan återställa säkerhetskopierade virtuella datorer till parad datacentret ifall de primära datacenter där virtuella datorer som körs upplevelser katastrofåterställning och du har konfigurerat Backup-valvet ska geo-redundant. Du måste välja ett lagringskonto som finns i parad datacenter och resten av återställningsprocessen förblir samma under dessa scenarier. Azure Backup använder beräkning tjänst från parad geo för att skapa den återställda virtuella datorn. Lär dig mer om [Azure Data center återhämtning](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)

## <a name="restoring-domain-controller-vms"></a>Återställa Domain Controller virtuella datorer
Säkerhetskopiering av virtuella datorer för domänkontrollanten (DC) är ett scenario som stöds med Azure Backup. Dock måste vara försiktig under återställningsprocessen. Med rätt återställningsprocessen beror på strukturen för domänen. I det enklaste fallet har en Domänkontrollant i en domän. Ofta för produktion belastningar har du en enda domän med flera domänkontrollanter, kanske med några domänkontrollanter som lokalt. Slutligen kan du ha en skog med flera domäner.

Ur ett Active Directory är Azure VM som andra Virtuella på en modern hypervisor som stöds. Med lokala hypervisorer största skillnaden är att det finns ingen VM-konsol i Azure. En konsol krävs för vissa scenarier, till exempel återställning med hjälp av en säkerhetskopia av typen Bare Metal Recovery (BMR). Återställning av virtuell dator från valvet är dock en fullständig ersättning för BMR. Active Directory återställningsläge (DSRM) är också tillgängliga, så att alla Active Directory-återställningsscenarier är användbart. Mer information finns i [säkerhetskopierar och återställer överväganden för virtualiserade domänkontrollanter](https://technet.microsoft.com/en-us/library/virtual_active_directory_domain_controller_virtualization_hyperv(v=ws.10).aspx#backup_and_restore_considerations_for_virtualized_domain_controllers) och [planering för återställning av Active Directory-skog](https://technet.microsoft.com/en-us/library/planning-active-directory-forest-recovery(v=ws.10).aspx).

### <a name="single-dc-in-a-single-domain"></a>Domänkontrollant i en domän
Den virtuella datorn kan återställas (som andra Virtuella) från Azure-portalen eller med hjälp av PowerShell.

### <a name="multiple-dcs-in-a-single-domain"></a>Flera domänkontrollanter i en domän
När andra domänkontrollanter i samma domän kan nås över nätverket, kan du återställa domänkontrollanten som någon virtuell dator. Om det är den sista återstående domänkontrollanten i domänen, eller en återställning i ett isolerat nätverk utförs, måste en skog recovery procedur följas.

### <a name="multiple-domains-in-one-forest"></a>Flera domäner i en skog
När andra domänkontrollanter i samma domän kan nås över nätverket, kan du återställa domänkontrollanten som någon virtuell dator. I annat fall rekommenderas en skogsåterställning dock.

<!--- WK: this following original supportability statement is incorrect, taking it out.
The challenge arises because DSRM mode is not present in Azure. So to restore such a VM, you cannot use the Azure portal. The only supported restore mechanism is disk-based restore using PowerShell.

> [!WARNING]
> For Domain Controller VMs in a multi-DC environment, do not use the Azure portal for restore! Only PowerShell based restore is supported
>
>

Read more about the [USN rollback problem](https://technet.microsoft.com/library/dd363553) and the strategies suggested to fix it.
--->

## <a name="restoring-vms-with-special-network-configurations"></a>Återställa virtuella datorer med särskilda nätverkskonfigurationer
Azure Backup stöder säkerhetskopiering för följande särskilda nätverks-konfigurationer av virtuella datorer.

* Virtuella datorer under belastningsutjämnare (interna och externa)
* Virtuella datorer med flera reserverade IP:
* Virtuella datorer med flera nätverkskort

De här konfigurationerna tilldela följande överväganden vid återställning dem.

> [!TIP]
> Använd PowerShell-baserad återställning flödet för att återskapa särskilda nätverkskonfigurationen för virtuella datorer efter återställning.
>
>

### <a name="restoring-from-the-ui"></a>Återställa från Användargränssnittet:
När från Gränssnittet **alltid välja en ny molntjänst**. Observera att eftersom portal tar bara obligatoriska parametrar under återställning flödet, virtuella datorer som har återställts med hjälp av Användargränssnittet förlorar de har särskilda nätverkskonfigurationen. Med andra ord återställningen virtuella datorer kommer att vara normal virtuella datorer utan konfiguration av belastningsutjämnare eller flera nätverkskort eller flera reserverade IP: N.

### <a name="restoring-from-powershell"></a>Återställa från PowerShell:
PowerShell har möjlighet att bara återställa Virtuella diskar från en säkerhetskopia och inte att skapa den virtuella datorn. Det här är användbart när du återställer virtuella datorer som kräver särskild nätverkskonfigurationer som nämns ovan.

Följ dessa steg för att återskapa fullständigt återställa diskar för virtuella datorer efter:

1. Återställa diskarna från säkerhetskopieringsvalvet använder [Azure Backup PowerShell](backup-azure-vms-classic-automation.md#restore-an-azure-vm)
2. Skapa VM-konfiguration som krävs för belastningsutjämning eller flera nätverkskort/flera reserverade IP: N med hjälp av PowerShell-cmdlets och Använd den för att skapa den virtuella datorn för desired configuration.

   * Skapa virtuell dator i Molntjänsten med [interna belastningsutjämnare](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)
   * Skapa virtuell dator att ansluta till [belastningsutjämnaren mot Internet](https://azure.microsoft.com/en-us/documentation/articles/load-balancer-internet-getstarted/)
   * Skapa virtuell dator med [flera nätverkskort](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics/)
   * Skapa virtuell dator med [flera reserverade IP-adresser](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/)

## <a name="next-steps"></a>Nästa steg
* [Felsökning av fel](backup-azure-vms-troubleshoot.md#restore)
* [Hantera virtuella datorer](backup-azure-manage-vms.md)
