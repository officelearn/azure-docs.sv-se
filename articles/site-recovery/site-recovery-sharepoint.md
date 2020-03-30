---
title: Haveriberedskap för en SharePoint-app på flera nivåer med Azure Site Recovery
description: I den hÃ¤r artikeln beskrivs hur du konfigurerar haveriã¤ndesÃ¤ringar för en SharePoint-program på flera nivåer med azure site recovery-funktioner.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: d74e28ce470c23bbc8ee2081532a198c260ccea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74706370"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Konfigurera haveriberedskap för ett SharePoint-program på flera nivåer för haveriberedskap med hjälp av Azure Site Recovery

I den hÃ¤r artikeln beskrivs i detalj hur du skyddar ett SharePoint-program med [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Översikt

Microsoft SharePoint är ett kraftfullt program som kan hjälpa en grupp eller avdelning att ordna, samarbeta och dela information. SharePoint kan tillhandahålla intranätportaler, dokument- och filhantering, samarbete, sociala nätverk, extranät, webbplatser, företagssökning och business intelligence. Den har också funktioner för systemintegration, processintegration och automatisering av arbetsflöden. Vanligtvis anser organisationer att det är ett Tier-1-program som är känsligt för driftstopp och dataförlust.

I dag tillhandahåller Microsoft SharePoint inga funktioner för inaktering av katastrofer. Oavsett typ och skala för en katastrof innebär återställning användning av ett väntedatacenter som du kan återställa servergruppen till. Väntelägesdatacenter krävs för scenarier där lokala redundanta system och säkerhetskopior inte kan återställas från avbrottet i det primära datacentret.

En bra lösning för haveriberedskap bör möjliggöra modellering av återställningsplaner runt komplexa programarkitekturer som SharePoint. Det bör också ha möjlighet att lägga till anpassade steg för att hantera programmappningar mellan olika nivåer och därmed ge en enda klick redundans med en lägre RTO i händelse av en katastrof.

I den hÃ¤r artikeln beskrivs i detalj hur du skyddar ett SharePoint-program med [Azure Site Recovery](site-recovery-overview.md). Den här artikeln innehåller metodtips för att replikera ett SharePoint-program på tre nivåer till Azure, hur du kan göra en snabbrmaskin för haveriberedskap och hur du kan redundan programmet till Azure.

Du kan titta på videon nedan om hur du återställer ett program på flera nivåer till Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Krav

Innan du börjar, se till att du förstår följande:

1. [Replikera en virtuell dator till Azure](site-recovery-vmware-to-azure.md)
2. Så här [utformar du ett återställningsnätverk](site-recovery-network-design.md)
3. [Gör en testväxling till Azure](site-recovery-test-failover-to-azure.md)
4. [Gör en redundans till Azure](site-recovery-failover.md)
5. Replikera [en domänkontrollant](site-recovery-active-directory.md)
6. Replikera [SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>SharePoint-arkitektur

SharePoint kan distribueras på en eller flera servrar med hjälp av nivåindelade topologier och serverroller för att implementera en servergruppsdesign som uppfyller specifika mål och mål. En typisk stor SharePoint-servergrupp med hög efterfrågan som stöder ett stort antal samtidiga användare och ett stort antal innehållsobjekt använder tjänstgruppering som en del av sin skalbarhetsstrategi. Den här metoden innebär att köra tjänster på dedikerade servrar, gruppera dessa tjänster tillsammans och sedan skala ut servrarna som en grupp. Följande topologi illustrerar tjänst- och servergrupperingen för en SharePoint-servergrupp på tre nivåer. Mer information om olika SharePoint-topologier finns i SharePoint-dokumentation och produktradarkitekturer. Du hittar mer information om Distributionen av SharePoint 2013 i [det här dokumentet](https://technet.microsoft.com/library/cc303422.aspx).



![Distributionsmönster 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Site Recovery-stöd

Site Recovery är programagnostiker och bör fungera med alla versioner av SharePoint som körs på en dator som stöds. För att skapa den här artikeln användes virtuella VMware-datorer med Windows Server 2012 R2 Enterprise. SharePoint 2013 Enterprise edition och SQL Server 2014 Enterprise edition användes.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**Vmware** | Ja | Ja
**Fysisk server** | Ja | Ja
**Azure** | Ej tillämpligt | Ja


### <a name="things-to-keep-in-mind"></a>Saker att tänka på

Om du använder ett delat diskbaserat kluster som vilken nivå som helst i programmet kan du inte använda Site Recovery-replikering för att replikera dessa virtuella datorer. Du kan använda inbyggd replikering som tillhandahålls av programmet och sedan använda en [återställningsplan](site-recovery-create-recovery-plans.md) för att redundans alla nivåer.

## <a name="replicating-virtual-machines"></a>Replikera virtuella datorer

Följ [den här vägledningen](site-recovery-vmware-to-azure.md) för att börja replikera den virtuella datorn till Azure.

* När replikeringen är klar kontrollerar du att du går till varje virtuell dator på varje nivå och väljer samma tillgänglighetsuppsättning i Replikerade objekt > Inställningar > egenskaper > Beräkning och Nätverk". Om webbnivån till exempel har tre virtuella datorer kontrollerar du att alla tre virtuella datorer är konfigurerade för att vara en del av samma tillgänglighet som anges i Azure.

    ![Uppsättning tillgänglighetsuppsättning](./media/site-recovery-sharepoint/select-av-set.png)

* Mer information om hur du skyddar Active Directory och DNS finns i [Skydda Active Directory och DNS-dokument.](site-recovery-active-directory.md)

* Information om hur du skyddar databasnivån som körs på SQL-servern finns i [Skydda SQL Server-dokument.](site-recovery-sql.md)

## <a name="networking-configuration"></a>Nätverkskonfiguration

### <a name="network-properties"></a>Nätverksegenskaper

* Konfigurera nätverksinställningar i Azure-portalen för app- och webbnivå så att de virtuella datorerna kopplas till rätt DR-nätverk efter redundans.

    ![Välj nätverk](./media/site-recovery-sharepoint/select-network.png)


* Om du använder en statisk IP anger du den IP som du vill att den virtuella datorn ska ta i fältet **Mål-IP**

    ![Ange statisk IP](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS- och trafikroutning

Skapa [en Traffic Manager-profil av typen Prioritet](../traffic-manager/traffic-manager-create-profile.md) för webbplatser som är vända mot internet. Och konfigurera sedan DNS- och Traffic Manager-profilen på följande sätt.


| **Där** | **Källkod** | **Mål**|
| --- | --- | --- |
| Offentligt DNS | Offentliga DNS för SharePoint-webbplatser <br/><br/> Ex: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Lokalt DNS | sharepointonprem.contoso.com | Offentlig ip på den lokala gården |


Skapa [primär- och återställningsslutpunkter](../traffic-manager/traffic-manager-configure-priority-routing-method.md)i Traffic Manager-profilen . Använd den externa slutpunkten för lokal slutpunkt och offentlig IP för Azure-slutpunkt. Kontrollera att prioriteten är högre inställd på lokal slutpunkt.

Vara värd för en testsida på en viss port (till exempel 800) på SharePoint-webbnivån för att Traffic Manager automatiskt ska kunna identifiera tillgänglighet efter redundans. Detta är en lösning om du inte kan aktivera anonym autentisering på någon av dina SharePoint-webbplatser.

[Konfigurera Traffic Manager-profilen](../traffic-manager/traffic-manager-configure-priority-routing-method.md) med inställningarna nedan.

* Routningsmetod - "Prioritet"
* DNS-tid att leva (TTL) - "30 sekunder"
* Inställningar för slutpunktsövervakare – Om du kan aktivera anonym autentisering kan du ange en specifik webbplatsslutpunkt. Du kan också använda en testsida i en viss port (till exempel 800).

## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan

En återställningsplan gör det möjligt att sekvensera redundansen för olika nivåer i ett program på flera nivåer, vilket innebär att programmets konsekvens upprätthålls. Följ stegen nedan när du skapar en återställningsplan för ett webbprogram på flera nivåer. [Läs mer om hur du skapar en återställningsplan](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i redundansgrupper

1. Skapa en återställningsplan genom att lägga till virtuella datorer på app- och webbnivå.
2. Klicka på "Anpassa" för att gruppera de virtuella datorerna. Som standard ingår alla virtuella datorer i Grupp 1.

    ![Anpassa RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Skapa en annan grupp (grupp 2) och flytta virtuella datorer på webbnivå till den nya gruppen. Virtuella datorer på appnivå ska ingå i virtuella datorer på grupp 1 och virtuella datorer på webbnivå ska ingå i Grupp 2. Detta för att säkerställa att virtuella datorer på appnivå startas först följt av virtuella datorer på webbnivå.


### <a name="adding-scripts-to-the-recovery-plan"></a>Lägga till skript i återställningsplanen

Du kan distribuera de vanligaste Azure Site Recovery-skripten till ditt Automation-konto genom att klicka på knappen Distribuera till Azure nedan. När du använder ett publicerat skript ska du se till att du följer anvisningarna i skriptet.

[![Distribuera till Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Lägg till ett föråtgärdsskript i grupp 1 i gruppen med växling i SQL-tillgänglighet. Använd skriptet ASR-SQL-FailoverAG som publicerats i exempelskripten. Se till att du följer vägledningen i skriptet och gör de ändringar som krävs i skriptet på rätt sätt.

    ![Tillägg-AG-Script-steg-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Tillägg-AG-Script-steg-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Lägg till ett inläggsåtgärdsskript för att koppla en belastningsutjämning på den misslyckade över virtuella datorer på webbnivå (grupp 2). Använd skriptet ASR-AddSingleLoadBalancer som publicerats i exempelskripten. Se till att du följer vägledningen i skriptet och gör de ändringar som krävs i skriptet på rätt sätt.

    ![Tillägg-LB-Script-Steg-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Tillägg-LB-Script-Steg-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Lägg till ett manuellt steg för att uppdatera DNS-posterna så att de pekar på den nya servergruppen i Azure.

    * För webbplatser som är vända mot internet krävs inga DNS-uppdateringar efter redundans. Följ stegen som beskrivs i avsnittet Nätverksvägledning för att konfigurera Traffic Manager. Om Traffic Manager-profilen har konfigurerats enligt beskrivningen i föregående avsnitt lägger du till ett skript för att öppna dummy-port (800 i exemplet) på den virtuella Azure-datorn.

    * För interna platser som är vända mot, lägg till ett manuellt steg för att uppdatera DNS-posten så att den pekar på den nya virtuella datorns belastningsutjämnare IP för webbnivå.

4. Lägg till ett manuellt steg för att återställa sökprogrammet från en säkerhetskopia eller starta en ny söktjänst.

5. Följ nedanstående steg om du vill återställa söktjänstprogrammet från en säkerhetskopia.

    * Den här metoden förutsätter att en säkerhetskopia av söktjänstprogrammet utfördes före den oåterkalleliga händelsen och att säkerhetskopian är tillgänglig på DR-platsen.
    * Detta kan enkelt uppnås genom att schemalägga säkerhetskopian (till exempel en gång dagligen) och använda en kopieringsprocedur för att placera säkerhetskopian på DR-platsen. Kopieringsprocedurer kan omfatta skriptprogram som AzCopy (Azure Copy) eller konfigurera DFSR (Distributed File Services Replication).
    * Nu när SharePoint-servergruppen körs navigerar du i centraladministration, Säkerhetskopiering och återställning och väljer Återställ. Återställningen förhör den angivna säkerhetskopieringsplatsen (du kan behöva uppdatera värdet). Välj säkerhetskopian av söktjänstprogrammet som du vill återställa.
    * Sökningen återställs. Tänk på att återställningen förväntar sig att hitta samma topologi (samma antal servrar) och samma hårddiskbokhåller som tilldelats dessa servrar. Mer information finns [i "Återställ söktjänstprogram i SharePoint 2013"-dokumentet.](https://technet.microsoft.com/library/ee748654.aspx)


6. Följ nedanstående steg för att börja med ett nytt söktjänstprogram.

    * Den här metoden förutsätter att en säkerhetskopia av databasen "Sökadministration" är tillgänglig på DR-platsen.
    * Eftersom de andra databaserna för söktjänstprogram inte replikeras måste de återskapas. Det gör du genom att navigera till Central administration och ta bort söktjänstprogrammet. Ta bort indexfilerna på alla servrar som är värdar för sökindexet.
    * Återskapa söktjänstprogrammet och detta återskapar databaserna. Vi rekommenderar att du har ett förberett skript som återskapar det här tjänstprogrammet eftersom det inte är möjligt att utföra alla åtgärder via det grafiska gränssnittet. Det går till exempel bara att ange plats för indexenheten och konfigurera söktopologin med hjälp av SharePoint PowerShell-cmdletar. Använd Windows PowerShell cmdlet Restore-SPEnterpriseSearchServiceApplication och ange den logglevererade och replikerade search administration-databasen, Search_Service__DB. Den här cmdleten ger sökkonfiguration, schema, hanterade egenskaper, regler och källor och skapar en standarduppsättning med andra komponenter.
    * När söktjänstprogrammet har återskapats måste du starta en fullständig genomsökning för varje innehållskälla för att återställa söktjänsten. Du förlorar viss analysinformation från den lokala servergruppen, till exempel sökrekommendationer.

7. När alla steg är slutförda sparar du återställningsplanen och den slutliga återställningsplanen ser ut att följa.

    ![Sparad RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Gör en testväxling
Följ [den här vägledningen](site-recovery-test-failover-to-azure.md) för att göra en testväxling.

1.  Gå till Azure-portalen och välj ditt återställningstjänstvalv.
2.  Klicka på återställningsplanen som skapats för SharePoint-programmet.
3.  Klicka på "Testa redundans".
4.  Välj återställningspunkt och virtuellt Azure-nätverk för att starta testundanundansprocessen.
5.  När den sekundära miljön är uppe kan du utföra dina valideringar.
6.  När valideringarna är klara kan du klicka på Rensa test redundans på återställningsplanen och testundanfallsmiljön rensas.

Mer information om hur du gör test failover för AD och DNS finns i [Test failover considerations for AD and DNS document.](site-recovery-active-directory.md#test-failover-considerations)

Mer information om hur du gör test redundans för SQL Always ON-tillgänglighetsgrupper finns i [Utföra programdr och Azure Site Recovery och göra test redundansdokument.](site-recovery-sql.md#disaster-recovery-of-an-application)

## <a name="doing-a-failover"></a>Gör en redundans
Följ [den här vägledningen](site-recovery-failover.md) för att göra en redundansväxling.

1.  Gå till Azure-portalen och välj ditt Recovery Services-valv.
2.  Klicka på återställningsplanen som skapats för SharePoint-programmet.
3.  Klicka på "Redundans".
4.  Välj återställningspunkt för att starta redundansprocessen.

## <a name="next-steps"></a>Nästa steg
Du kan läsa mer om [hur du replikerar andra program](site-recovery-workload.md) med site recovery.
