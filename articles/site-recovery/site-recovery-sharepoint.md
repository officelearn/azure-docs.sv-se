---
title: "Replikera en SharePoint-flernivåapp med hjälp av Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du replikerar en SharePoint-flernivåapp med hjälp av Azure Site Recovery-funktioner."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/23/2017
ms.author: sutalasi
ms.openlocfilehash: 3610409691b71fcce0c36a3af94184dbe6db8661
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="replicate-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Replikera en SharePoint-flernivåapp för haveriberedskap med hjälp av Azure Site Recovery

Den här artikeln beskrivs i detalj hur du skyddar en SharePoint-program med [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Översikt

Microsoft SharePoint är ett kraftfullt program som kan hjälpa en grupp eller avdelning ordna samarbeta och dela information. SharePoint kan ge intranät portaler, dokument och filhantering, samarbete, sociala nätverk, extranät, webbplatser, enterprise-sökningen och business intelligence. Det har också integrering, integrering och funktioner för automatisering av arbetsflödet. Normalt organisationer tycker att det som en nivå 1 känslig för driftavbrott och dataförlust.

Idag, tillhandahåller Microsoft SharePoint inte några funktioner för out box katastrofåterställning. Återställning innebär användning av ett vänteläge datacenter som du kan återställa grupp till oavsett typen och skalan för en katastrofåterställning. Vänteläge Datacenter krävs för scenarier där lokala redundanta system och säkerhetskopieringar inte kan återställa från avbrott på den primära datacentralen.

En bra lösning för haveriberedskap ska tillåta modellering av återställningsplaner runt komplexa programarkitekturer, till exempel SharePoint. Det bör också ha möjlighet att lägga till anpassade steg för att hantera mappning mellan olika nivåer och därför att tillhandahålla ett enda musklick redundanskluster med en lägre RTO vid en katastrof.

Den här artikeln beskrivs i detalj hur du skyddar en SharePoint-program med [Azure Site Recovery](site-recovery-overview.md). Den här artikeln beskriver bästa praxis för att replikera en tre tjänstnivån SharePoint-programmet till Azure, hur du kan göra en katastrof återställningsgranskning och hur du kan redundansväxla programmet till Azure.

Du kan titta på den nedan video om hur du återställer en multi-nivåprogram till Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du känna till följande:

1. [Replikera en virtuell dator till Azure](site-recovery-vmware-to-azure.md)
2. Så här [utforma ett nätverk för återställning](site-recovery-network-design.md)
3. [Gör ett redundanstest till Azure](site-recovery-test-failover-to-azure.md)
4. [Gör en redundansväxling till Azure](site-recovery-failover.md)
5. Så här [replikera en domänkontrollant](site-recovery-active-directory.md)
6. Så här [replikera SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>SharePoint-arkitektur

SharePoint kan distribueras på en eller flera servrar med nivåindelade topologier och serverroller du implementerar en servergrupp design som uppfyller specifika mål och -mål. En typisk stora, hög SharePoint-servergrupp som stöder ett stort antal samtidiga användare och ett stort antal innehållsobjekt använda tjänsten gruppering som en del av deras skalbarhet-strategi. Den här metoden innebär att du kör tjänster på dedicerade servrar, gruppera dessa tjänster och sedan skala ut servrarna som en grupp. Följande topologin illustrerar tjänsten och server gruppering för ett tre skikt SharePoint-servergrupp. Hänvisa till SharePoint-dokumentationen och produkten rad arkitekturer för detaljerad information om olika SharePoint-topologier. Du hittar mer information om distribution av SharePoint 2013 i [dokumentet](https://technet.microsoft.com/en-us/library/cc303422.aspx).



![Distribution av mönstret 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Site Recovery-stöd

VMware-datorer med Windows Server 2012 R2 Enterprise användes för att skapa den här artikeln. SharePoint 2013 Enterprise edition och SQL server 2014 Enterprise edition användes. Site Recovery replikering är oberoende av programmet, förväntas rekommendationerna här vänta samt följande scenarier.

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

Om du använder en delad disk-kluster som en nivå i ditt program kommer sedan du inte att kunna använda Site Recovery replikering för att replikera de virtuella datorerna. Du kan använda inbyggda replikering som tillhandahålls av programmet och sedan använda en [återställningsplanen](site-recovery-create-recovery-plans.md) alla nivåer för redundans.

## <a name="replicating-virtual-machines"></a>Replikering av virtuella datorer

Följ [vägledningen](site-recovery-vmware-to-azure.md) att starta den virtuella datorn replikeras till Azure.

* När replikeringen är klar måste du gå till varje virtuell dator för varje nivå och välj samma tillgänglighetsuppsättning i ' replikerade objekt > Inställningar > Egenskaper > beräkning och nätverk ”. Till exempel om din webbnivå har 3 virtuella datorer, se till att alla 3 virtuella datorer konfigureras som en del av samma tillgänglighetsuppsättning i Azure.

    ![Set-Availability-Set](./media/site-recovery-sharepoint/select-av-set.png)

* För anvisningar om hur du skyddar Active Directory och DNS Se [skydda Active Directory och DNS](site-recovery-active-directory.md) dokumentet.

* För anvisningar om hur du skyddar databasnivå som körs på SQLServer Se [skydda SQL Server](site-recovery-active-directory.md) dokumentet.

## <a name="networking-configuration"></a>Nätverkskonfiguration

### <a name="network-properties"></a>Egenskaper för nätverk

* Konfigurera nätverksinställningar i Azure-portalen för appen och webb nivå virtuella datorer, så att de virtuella datorerna hämta ansluten till rätt DR-nätverket efter redundans.

    ![Välj nätverk](./media/site-recovery-sharepoint/select-network.png)


* Om du använder en statisk IP-adress, anger du IP-adress som du vill att den virtuella datorn i den **mål-IP** fält

    ![Ange statisk IP-adress](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS- och routning av nätverkstrafik

För platser, mot internet [skapa en trafikhanterarprofil av typen 'Priority'](../traffic-manager/traffic-manager-create-profile.md) i Azure-prenumeration. Och sedan konfigurera din DNS- och Traffic Manager-profil på följande sätt.


| **Där** | **Source** | **Mål**|
| --- | --- | --- |
| Offentliga DNS | Offentliga DNS för SharePoint-webbplatser <br/><br/> Ex: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Lokal DNS | sharepointonprem.contoso.com | Offentliga IP-adress på den lokala gruppen |


I Traffic Manager-profilen [skapar de primära platsen och återställningsplatsen slutpunkterna](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Använd den externa slutpunkten för den lokala slutpunkten och offentliga IP för Azure slutpunkt. Se till att prioriteten ställs in högre till den lokala slutpunkten.

Värd för en testsida på en viss port (t.ex, 800) i SharePoint-webbnivå i ordning för Traffic Manager automatiskt ska identifiera tillgänglighet efter växling vid fel. Detta är en lösning om du inte kan aktivera anonym autentisering på någon av SharePoint-webbplatser.

[Konfigurera trafikhanterarprofilen](../traffic-manager/traffic-manager-configure-priority-routing-method.md) med det under inställningar.

* Routningsmetoden - 'Priority'
* Time to live (TTL) - 30 sekunder'-DNS
* Övervakaren slutpunktsinställningar - om du kan aktivera anonym autentisering kan du ge en viss webbplats slutpunkt. Eller så kan du använda en testsida på en viss port (t.ex, 800).

## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan

En återställningsplan kan ordningsföljd för växling vid fel på olika nivåer i en flernivåapp, därför kan upprätthålla programkonsekvens. Följ de nedanstående steg när du skapar en återställningsplan för ett webbprogram med flera nivåer. [Lär dig mer om hur du skapar en återställningsplan](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer till grupper för växling vid fel

1. Skapa en återställningsplan genom att lägga till appen och webb nivå virtuella datorer.
2. Klicka på Anpassa om du vill gruppera de virtuella datorerna. Som standard är alla virtuella datorer som en del av grupp 1.

    ![Anpassa RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Skapar en ny grupp (Grupp2) och flytta webbnivå virtuella datorer till den nya gruppen. App-nivå VMs ska vara en del av grupp 1 och webbnivå VMs ska vara en del av Grupp2. Detta är att se till att appen nivå VMs Start in först följt av webbnivå virtuella datorer.


### <a name="adding-scripts-to-the-recovery-plan"></a>Att lägga till skript återställningsplanen

Du kan distribuera de mest använda Azure Site Recovery-skript i ditt Automation-konto att klicka på knappen ”distribuera till Azure'. När du använder alla publicerade skript, se till att du följer riktlinjerna i skriptet.

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Lägg till ett skript före åtgärden till grupp 1 redundans SQL-tillgänglighetsgruppen. Använda 'ASR-SQL-FailoverAG'-skript som publiceras i skriptexemplen. Se till att du följer du anvisningarna i skriptet och gör nödvändiga ändringar i skriptet på rätt sätt.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Lägg till en post åtgärd skript om du vill koppla en belastningsutjämnare på den över virtuella datorer i webbnivå (Grupp2). Använda 'ASR AddSingleLoadBalancer'-skript som publiceras i skriptexemplen. Se till att du följer du anvisningarna i skriptet och gör nödvändiga ändringar i skriptet på rätt sätt.

    ![Add-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Lägg till ett manuellt steg för att uppdatera DNS-posterna pekar på den nya gruppen i Azure.

    * För internet facing platser, inga DNS-uppdateringar som är nödvändiga efter växling vid fel. Följ stegen som beskrivs i avsnittet vägledning för nätverk att konfigurera Traffic Manager. Om Traffic Manager-profil har konfigurerats enligt beskrivningen i föregående avsnitt, kan du lägga till ett skript för att öppna dummy port (800 i exemplet) på den virtuella Azure-datorn.

    * Lägg till ett manuellt steg för att uppdatera DNS-post som pekar på den nya Web nivå Virtuella datorns belastningen belastningsutjämnaren IP för interna Internetriktade platser.

4. Lägg till ett manuellt steg om du vill återställa sökprogram från en säkerhetskopia eller starta en ny search-tjänsten.

5. Följ nedanstående steg för att återställa Sök efter tjänstprogram från en säkerhetskopia.

    * Den här metoden förutsätter att en säkerhetskopia av tjänstprogrammet sökningen utfördes före händelsen oåterkalleligt och att säkerhetskopian är tillgänglig på DR-plats.
    * Detta kan enkelt uppnås genom schemalägger säkerhetskopieringen (till exempel, en gång om dagen) och använda en kopia procedur för att placera säkerhetskopian i DR-plats. Kopiera procedurer kan omfatta skriptbaserade program, till exempel AzCopy (Azure kopia) eller konfigurera DFSR (Distributed File Services Replication).
    * Nu när SharePoint-servergruppen kör navigera Central Administration, ”säkerhetskopiera och återställa” och väljer Återställ. Återställningen interrogates säkerhetskopieringsplatsen angetts (du kan behöva uppdatera värdet). Välj Sök efter tjänstprogram säkerhetskopian som du vill återställa.
    * Sökningen har återställts. Tänk på att återställningen förväntar att hitta samma topologi (samma antal servrar) och samma hårddisk bokstäver som tilldelats till dessa servrar. Mer information finns i [återställa Sök-tjänstprogrammet i SharePoint 2013](https://technet.microsoft.com/library/ee748654.aspx) dokumentet.


6. Följ nedanstående steg för för att starta med ett nytt program för Search-tjänsten.

    * Den här metoden förutsätter att en säkerhetskopia av databasen ”sökadministration” är tillgänglig på DR-plats.
    * Eftersom andra databaser för Search-tjänstprogrammet inte replikeras, måste de skapas på nytt. Gör du genom att gå till Central Administration och tar bort tjänstprogrammet sökning. Ta bort indexfilerna på alla servrar som är värd för Search-Index.
    * Återskapa Sök efter tjänstprogram och detta återskapar databaserna. Det rekommenderas att du har en förberedd skript som skapar det här programmet eftersom det inte går att utföra alla åtgärder via Användargränssnittet. Till exempel är att enhetsplats index och konfigurera söktopologi bara möjligt med SharePoint PowerShell-cmdlets. Använda Windows PowerShell-cmdleten återställning SPEnterpriseSearchServiceApplication och ange log levererats och replikerade Search-Administration databasen Search_Service__DB. Denna cmdlet ger sökkonfigurationen, schema, hanterade egenskaper, regler och källor och skapar en standarduppsättning med de andra komponenterna.
    * När sökningen-tjänstprogrammet har skapas på nytt, måste du starta en fullständig crawl för varje innehållskälla att återställa Search-tjänsten. Du förlorar analytics information från lokala servergrupp, till exempel sökning rekommendationer.

7. När alla steg har slutförts, spara återställningsplanen och slutlig återställningsplanen ser ut följande.

    ![Sparade RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Gör ett redundanstest
Följ [vägledningen](site-recovery-test-failover-to-azure.md) att göra ett redundanstest.

1.  Gå till Azure-portalen och välj Recovery-tjänsten-valvet.
2.  Klicka på återställningsplan som skapats för SharePoint-programmet.
3.  Klicka på Testa redundans.
4.  Välj återställningspunkten och Azure-nätverk för att starta processen testa redundans.
5.  Du kan utföra dina verifieringar när den sekundära miljön är upp.
6.  När validering har slutförts kan du på 'Rensa redundanstestet' återställningsplanen och redundanstestmiljön har rensats.

Anvisningar om hur du gör testa redundans för AD och DNS, referera till [Redundanstestning för AD- och DNS](site-recovery-active-directory.md#test-failover-considerations) dokumentet.

För anvisningar om hur du gör testa redundans för SQL Always ON-Tillgänglighetsgrupper Se [gör testa redundans för SQL Server alltid aktiverad](site-recovery-sql.md#steps-to-do-a-test-failover) dokumentet.

## <a name="doing-a-failover"></a>Genomför en redundansväxling enligt
Följ [vägledningen](site-recovery-failover.md) för att göra en växling vid fel.

1.  Gå till Azure-portalen och välj Recovery Services-valvet.
2.  Klicka på återställningsplan som skapats för SharePoint-programmet.
3.  Klicka på 'Redundans'.
4.  Välj återställningspunkt för att starta processen för växling vid fel.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om [replikering av andra program](site-recovery-workload.md) med Site Recovery.
