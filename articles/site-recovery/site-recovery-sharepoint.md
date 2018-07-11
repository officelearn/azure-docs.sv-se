---
title: Replikera en SharePoint-flernivåapp med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du replikerar en SharePoint-flernivåapp med hjälp av Azure Site Recovery-funktioner.
services: site-recovery
documentationcenter: ''
author: sujayt
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: eb5801988b6fa966a0326c39be4a267ea08500a8
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916793"
---
# <a name="replicate-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Replikera ett flerskiktat SharePoint-program för haveriberedskap med hjälp av Azure Site Recovery

Den här artikeln beskrivs i detalj hur du skyddar en SharePoint-program med [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Översikt

Microsoft SharePoint är ett kraftfullt program som kan hjälpa en grupp eller avdelning ordna, samarbeta och dela information. SharePoint kan ge intranät portaler, dokument och filhantering, samarbete, sociala nätverk, extranät, webbplatser, företagssökning och business intelligence. Det har även systemintegrering, processintegrering och funktioner för automatisering av arbetsflöde. Normalt organisationer du betrakta dem som ett program på nivå 1 känsliga för stopptid och dataförlust.

Microsoft SharePoint ger idag, inte alla funktioner för out-of the box katastrofåterställning. Återställning innebär användningen av en standby-datacenter som du kan återställa servergrupp till oavsett typ och skalning av ett haveri. Standby-Datacenter krävs för scenarier där lokala redundanta system och säkerhetskopieringar inte kan återställa från strömavbrott i det primära datacentralen.

En bra katastrofåterställningslösning ska tillåta modellering av återställningsplaner runt arkitekturer för komplexa program som SharePoint. Det bör även ha möjligheten att lägga till anpassade steg för att hantera mappning mellan olika nivåer och därför att tillhandahålla ett enda musklick redundans med en lägre RTO vid ett haveri.

Den här artikeln beskrivs i detalj hur du skyddar en SharePoint-program med [Azure Site Recovery](site-recovery-overview.md). Den här artikeln beskriver Metodtips för att replikera en tre nivån, SharePoint-program till Azure, hur du kan göra ett haveriberedskapstest och hur du kan redundansväxla programmet till Azure.

Du kan titta på den nedan video om hur du återställer en multi-nivåprogram till Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du förstår följande:

1. [Replikera en virtuell dator till Azure](site-recovery-vmware-to-azure.md)
2. Så här [utforma återställningsnätverk](site-recovery-network-design.md)
3. [Gör ett redundanstest till Azure](site-recovery-test-failover-to-azure.md)
4. [Gör en redundansväxling till Azure](site-recovery-failover.md)
5. Så här [replikera en domänkontrollant](site-recovery-active-directory.md)
6. Så här [replikera SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>SharePoint-arkitektur

SharePoint kan distribueras på en eller flera servrar med hjälp av nivåindelade topologier och serverroller du implementerar en servergrupp design som uppfyller specifika mål och delmål. En typisk stora, hög efterfrågan SharePoint-servergrupp som stöder ett stort antal samtidiga användare och ett stort antal innehållsposter använda tjänsten gruppering som en del av sin strategi för skalbarhet. Den här metoden innebär att köra tjänster på dedicerade servrar, gruppera de här tjänsterna och sedan skala ut servrar som en grupp. Följande topologi illustrerar tjänsten och server gruppering för en SharePoint-servergrupp med tre nivåer. Se SharePoint-dokumentationen och produkten rad arkitekturer för detaljerad information om olika SharePoint-topologier. Du hittar mer information om distribution av SharePoint 2013 i [det här dokumentet](https://technet.microsoft.com/library/cc303422.aspx).



![Mönster för distribution 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Site Recovery-stöd

VMware-datorer med Windows Server 2012 R2 Enterprise användes för att skapa den här artikeln. SharePoint 2013 Enterprise edition och SQL server 2014 Enterprise edition användes. Eftersom Site Recovery-replikering är oberoende av program, förväntas de rekommendationer som anges här vänta för följande scenarier.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Fysisk server** | Ja | Ja
**Azure** | Ej tillämpligt | Ja

### <a name="sharepoint-versions"></a>SharePoint-versioner
Följande SharePoint server-versioner stöds.

* SharePoint server 2013 Standard
* SharePoint server 2013 Enterprise
* SharePoint server 2016 Standard
* SharePoint server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Saker att tänka på

Om du använder ett delat kluster diskbaserade som valfri nivå i din app kommer sedan du inte att kunna använda Site Recovery-replikering för att replikera de virtuella datorerna. Du kan använda inbyggda replikering som tillhandahålls av programmet och sedan använda en [återställningsplanen](site-recovery-create-recovery-plans.md) att redundansväxla alla nivåer.

## <a name="replicating-virtual-machines"></a>Replikering av virtuella datorer

Följ [den här vägledningen](site-recovery-vmware-to-azure.md) att börja replikera den virtuella datorn till Azure.

* När replikeringen är klar kan du se till att du går till varje virtuell dator på respektive nivå och välj samma tillgänglighetsuppsättning i ”replikerat objekt > Inställningar > Egenskaper > beräkning och nätverk”. Exempel: om din webbnivå har 3 virtuella datorer, se till att alla 3 virtuella datorer är konfigurerade som en del av samma tillgänglighetsuppsättning i Azure.

    ![Ställ in Tillgänglighetsuppsättning](./media/site-recovery-sharepoint/select-av-set.png)

* Anvisningar om hur du skyddar Active Directory och DNS avser [skydda Active Directory och DNS](site-recovery-active-directory.md) dokumentet.

* Vägledning om hur du skyddar databasnivån som körs på SQLServer finns i [skydda SQL Server](site-recovery-active-directory.md) dokumentet.

## <a name="networking-configuration"></a>Nätverkskonfigurationen

### <a name="network-properties"></a>Nätverksegenskaper

* För appen och webb-nivån virtuella datorer kan du konfigurera nätverksinställningar i Azure portal så att de virtuella datorerna får ansluts till rätt DR-nätverket efter redundans.

    ![Välj nätverk](./media/site-recovery-sharepoint/select-network.png)


* Om du använder en statisk IP-adress, anger du IP-adress som du vill att den virtuella datorn i den **mål-IP** fält

    ![Ställ in statisk IP-adress](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS- och routning av nätverkstrafik

För platser som riktas mot internet [skapa en Traffic Manager-profil av typen ”prioritet”](../traffic-manager/traffic-manager-create-profile.md) i Azure-prenumeration. Och sedan konfigurera din DNS- och Traffic Manager-profil på följande sätt.


| **där** | **Källa** | **Mål**|
| --- | --- | --- |
| Offentliga DNS | Offentliga DNS för SharePoint-webbplatser <br/><br/> Exempel: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Den lokala DNS | sharepointonprem.contoso.com | Offentlig IP-adress på en lokal grupp |


I Traffic Manager-profilen [skapa primära slutpunkter](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Använd den externa slutpunkten för den lokala slutpunkten och offentlig IP-adress för Azure-slutpunkt. Se till att prioriteten ställs högre till den lokala slutpunkten.

Vara värd för en testsida på en viss port (t.ex, 800) i SharePoint-webbnivån i ordning för Traffic Manager att automatiskt identifiera tillgänglighet efter redundans. Det här är en lösning om du inte kan aktivera anonym autentisering på någon av dina SharePoint-webbplatser.

[Konfigurera Traffic Manager-profilen](../traffic-manager/traffic-manager-configure-priority-routing-method.md) med den under inställningar.

* Routningsmetod - ”prioritet”
* DNS-tid att live (TTL) - 30 sekunder'
* Inställningar för slutpunktsövervakning - om du kan aktivera anonym autentisering kan du ge en viss webbplats-slutpunkt. Eller du kan använda en testsida på en viss port (t.ex, 800).

## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan

En återställningsplan kan ordningsföljd redundans för olika nivåer i ett flerskiktat program, därför kan underhålla programkonsekvens. Följ de nedanstående steg när du skapar en återställningsplan för ett webbprogram med flera nivåer. [Läs mer om hur du skapar en återställningsplan](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i grupper för växling vid fel

1. Skapa en återställningsplan genom att lägga till appen och webb-nivån virtuella datorer.
2. Klicka på Anpassa om du vill gruppera de virtuella datorerna. Som standard har alla virtuella datorer ingår i ”grupp 1”.

    ![Anpassa RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Skapar en ny grupp (Grupp2) och flytta virtuella datorer på webbnivå till den nya gruppen. Din App-nivå virtuella datorer ska vara en del av ”grupp 1” och virtuella datorer på webbnivå ska vara en del av ”Grupp2”. Det här är att se till att de App-nivå virtuella datorer startar upp först följt av virtuella datorer på webbnivå.


### <a name="adding-scripts-to-the-recovery-plan"></a>Lägga till skript i en återställningsplan

Du kan distribuera de mest använda Azure Site Recovery-skript i ditt Automation-konto att klicka på knappen ”distribuera till Azure” nedan. När du använder alla publicerade skript, se till att du följer riktlinjerna i skriptet.

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Lägg till en åtgärd skript till ”grupp 1” till redundans SQL-tillgänglighetsgrupp. Använd ”ASR-SQL-FailoverAG'-skript som publiceras i exempelskripten. Se till att du följer riktlinjerna i skriptet och gör nödvändiga ändringar i skriptet på rätt sätt.

    ![Lägg till-AG-skript-steg-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Lägg till-AG-skript-steg-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Lägg till en post åtgärd skript om du vill koppla en belastningsutjämnare på den redundansväxlade virtuella datorerna på webbnivå (Grupp2). Använd ”ASR-AddSingleLoadBalancer'-skript som publiceras i exempelskripten. Se till att du följer riktlinjerna i skriptet och gör nödvändiga ändringar i skriptet på rätt sätt.

    ![Lägg till-LB-skript-steg-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Lägg till-LB-skript-steg-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Lägg till en manuell åtgärd för att uppdatera DNS-posterna pekar på den nya gruppen i Azure.

    * För platser som riktas mot internet, är inga DNS-uppdateringar krävs efter redundans. Följ stegen som beskrivs i avsnittet ”nätverksvägledning” om du vill konfigurera Traffic Manager. Om Traffic Manager-profil har konfigurerats enligt beskrivningen i föregående avsnitt, lägger du till ett skript för att öppna dummy-porten (800 i det här exemplet) på Azure-VM.

    * Lägg till en manuell åtgärd för att uppdatera DNS-post som pekar på den nya Web nivån Virtuella datorns IP-load balancer för interna kundinriktade webbplatser.

4. Lägg till en manuell åtgärd för att återställa sökprogram från en säkerhetskopia eller starta en ny söktjänst.

5. Följ stegen nedan för att återställa Search service-program från en säkerhetskopia.

    * Den här metoden förutsätter att en säkerhetskopia av Search Service Application utfördes innan den i händelsen av katastrof och att säkerhetskopian är tillgänglig på DR-plats.
    * Detta kan enkelt uppnås genom att schemalägga säkerhetskopieringen (till exempel, en gång per dag) och använda en kopia procedur för att placera säkerhetskopian på DR-plats. Kopiera procedurer kan omfatta skriptbaserade program, till exempel AzCopy (Azure Copy) eller hur du konfigurerar DFSR (Distributed File Services Replication).
    * Nu när SharePoint-servergruppen kör kan navigera den centraladministrationen av ”säkerhetskopiera och återställa” och väljer Återställ. Återställningen interrogates säkerhetskopieringsplatsen angetts (du kan behöva uppdatera värdet). Välj Search Service Application-säkerhetskopia som du vill återställa.
    * Sök igen. Tänk på att återställningen förväntar sig att hitta samma topologi (samma antal servrar) och samma hårddisk bokstäver som är tilldelade till dessa servrar. Mer information finns i [återställa Search service application i SharePoint 2013](https://technet.microsoft.com/library/ee748654.aspx) dokumentet.


6. Följ stegen nedan för att starta med ett nytt program för Search-tjänsten.

    * Den här metoden förutsätter att en säkerhetskopia av databasen ”sökadministration” är tillgänglig på DR-plats.
    * Eftersom de andra Search Service Application-databaserna inte replikeras, måste de skapas på nytt. Gå till Central Administration för att göra det, och ta bort Search Service Application. Ta bort indexfilerna på alla servrar som är värd för Search-Index.
    * Återskapa Search Service Application och det här återskapar databaserna. Vi rekommenderar att ha en förberedd skript som skapar det här programmet eftersom det inte går att utföra alla åtgärder via Användargränssnittet. Till exempel är att enhetsplats index och konfigurera söktopologin bara kan utföras med hjälp av SharePoint PowerShell-cmdletar. Använda Windows PowerShell-cmdleten Restore-SPEnterpriseSearchServiceApplication och ange log-levererade och replikerade Search Administration databasen Search_Service__DB. Denna cmdlet kan Sökkonfiguration, schema, hanterade egenskaper, regler och källor och skapa en standarduppsättning med andra komponenter.
    * När Search Service Application har återskapas, måste du starta en fullständig crawl för varje innehållskälla att återställa söktjänsten. Du förlorar analytics information från den lokala gruppen, t.ex Sök efter rekommendationer.

7. När alla steg har slutförts, spara återställningsplanen och sista återställningsplanen ska se ut så här.

    ![Sparade RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Gör ett redundanstest
Följ [den här vägledningen](site-recovery-test-failover-to-azure.md) att göra ett redundanstest.

1.  Gå till Azure portal och välj Recovery Service-valv.
2.  Klicka på återställningsplanen som skapats för SharePoint-programmet.
3.  Klicka på ”Redundanstest”.
4.  Välj återställningspunkten och Azure-nätverk för att börja testa redundans.
5.  När den sekundära miljön är igång, kan du utföra dina verifieringar.
6.  När validering är klar kan du kan klicka på Rensa redundanstestning i återställningsplanen och redundanstestmiljön har rensats.

Anvisningar om hur du gör redundanstestningen för AD och DNS, referera till [testa redundans överväganden för AD- och DNS](site-recovery-active-directory.md#test-failover-considerations) dokumentet.

Vägledning om hur du gör redundanstestningen för SQL Always ON-Tillgänglighetsgrupper finns i [gör testa redundansväxlingen för SQL Server Always On](site-recovery-sql.md#steps-to-do-a-test-failover) dokumentet.

## <a name="doing-a-failover"></a>Gör en redundansväxling
Följ [den här vägledningen](site-recovery-failover.md) för att göra en redundansväxling.

1.  Gå till Azure portal och välj Recovery Services-valvet.
2.  Klicka på återställningsplanen som skapats för SharePoint-programmet.
3.  Klicka på ”redundans”.
4.  Välj återställningspunkt för att starta redundansprocessen.

## <a name="next-steps"></a>Nästa steg
Du kan läsa mer om [replikering av andra program](site-recovery-workload.md) med Site Recovery.
