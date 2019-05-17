---
title: Konfigurera katastrofåterställning till Azure för stort antal virtuella VMware-datorer eller fysiska servrar med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du konfigurerar haveriberedskap till Azure för stora mängder lokala virtuella VMware-datorer eller fysiska servrar med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: raynew
ms.openlocfilehash: 593a82221ec94e43d50b7382ba89053d5f68864a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65805928"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Konfigurera haveriberedskap i stor skala för VMware-datorer/fysiska servrar

Den här artikeln beskrivs hur du konfigurerar haveriberedskap till Azure har många (> 1000) av lokala virtuella VMware-datorer eller fysiska servrar i din produktionsmiljö med den [Azure Site Recovery](site-recovery-overview.md) service.


## <a name="define-your-bcdr-strategy"></a>Definiera din BCDR-strategi

Som en del av affärskontinuitet och haveriberedskap (BCDR) kan definiera du återställningspunktmål (Rpo) och mål för återställningstid (RTO) för appar och arbetsbelastningar. Återställningstidsmålet mäter varaktigheten för tid och tjänsten nivå som en affärsapp eller process måste återställda och vara tillgängligt, för att undvika problem för affärskontinuitet.
- Site Recovery ger ständig replikering för VMware-datorer och fysiska servrar och en [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) för Återställningstid.
- När du planerar för storskaliga haveriberedskap för virtuella VMware-datorer och ta reda på de Azure-resurser måste ange du ett RTO-värde som ska användas för kapacitet beräkningar.


## <a name="best-practices"></a>Regelverk

Vissa allmänna Metodtips för storskaliga katastrofåterställning. Dessa bästa metoder beskrivs närmare i nästa avsnitt i dokumentet.

- **Identifiera krav för target**: Beräkna ut kapacitet och resursbehov i Azure innan du konfigurerar haveriberedskap.
- **Planera för Site Recovery-komponenter**: Ta reda på vilka Site Recovery-komponenter (konfigurationsservern, processervrar) som du behöver för att uppfylla din uppskattade kapacitet.
- **Konfigurera en eller flera skala ut processervrar**: Använd inte den processerver som körs som standard på konfigurationsservern. 
- **Kör de senaste uppdateringarna**: Site Recovery-teamet släpper nya versioner av Site Recovery-komponenter körs regelbundet och du bör kontrollera att du kör de senaste versionerna. För att hjälpa med som, spåra [nyheter](site-recovery-whats-new.md) efter uppdateringar, och [aktivera och installera uppdateringar](service-updates-how-to.md) när de Frigör.
- **Övervaka proaktivt**: När du får haveriberedskap och drift, bör du proaktivt övervaka status och hälsa för replikerade datorer och resurser i infrastrukturen.
- **Tester av haveriberedskap**: Du bör köra tester av haveriberedskap med jämna mellanrum. Dessa inte påverkar på din produktionsmiljö, men att säkerställa att redundansväxlingen till Azure fungerar som förväntat när det behövs.



## <a name="gather-capacity-planning-information"></a>Samla in information om kapacitetsplanering

Samla in information om din lokala miljö, för att utvärdera och beräkna mål (Azure) kapacitetsbehov.
- För VMware, kör Distributionsplaneraren för VMware-datorer att göra detta.
- Samla in information manuellt för fysiska servrar.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Kör Distributionsplaneraren för VMware-datorer

Kapacitetsplaneraren hjälper dig att samla in information om din lokala VMware-miljö.

- Kör Distributionshanteraren under en period som representerar vanliga omsättning för dina virtuella datorer. Detta genererar mer exakta beräkningar och rekommendationer.
- Vi rekommenderar att du kör Distributionshanteraren på configuration server-datorn, eftersom Planeringsverktyget beräknar dataflödet från servern där den körs. [Läs mer](site-recovery-vmware-deployment-planner-run.md#get-throughput) om mäta dataflödet.
- Om du ännu inte har en konfigurationsserver konfigurera:
    - [Få en översikt](vmware-physical-azure-config-process-server-overview.md) av Site Recovery-komponenter.
    - [Ställa in en konfigurationsserver](vmware-azure-deploy-configuration-server.md), för att köra Distributionshanteraren på den.

Kör Planeringsverktyget på följande sätt:

1. [Lär dig mer om](site-recovery-deployment-planner.md) Deployment Planner. Du kan hämta den senaste versionen från portalen eller [ladda ned den direkt](https://aka.ms/asr-deployment-planner).
2. Granska den [krav](site-recovery-deployment-planner.md#prerequisites) och [senaste uppdateringarna](site-recovery-deployment-planner-history.md) för Kapacitetsplaneraren, och [ladda ned och extrahera](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) verktyget.
3. [Kör Distributionshanteraren](site-recovery-vmware-deployment-planner-run.md) på konfigurationsservern.
4. [Generera en rapport](site-recovery-vmware-deployment-planner-run.md#generate-report) att sammanfatta uppskattningar och rekommendationer.
5. Analysera den [rapportera rekommendationer](site-recovery-vmware-deployment-planner-analyze-report.md) och [kostnad få uppskattningar](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Som standard verktyget är konfigurerat att profilera och genereras rapporten för upp till 1 000 virtuella datorer. Du kan ändra den här gränsen genom att öka nyckelvärdet maxvmssupported i filen ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Planera mål (Azure) krav och kapacitet

Med dina insamlade uppskattningar och rekommendationer kan planera du för target-resurser och kapacitet. Om du körde Deployment Planner för virtuella VMware-datorer kan du använda ett antal den [rapportera rekommendationer](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) som hjälper dig att.

- **Kompatibla virtuella datorer**: Använd det här numret för att identifiera hur många virtuella datorer som är klara för haveriberedskap till Azure. Rekommendationer om nätverkets bandbredd och Azure-kärnor baseras på det här talet.
- **Krävs nätverksbandbredd**: Observera den bandbredd som du behöver för deltareplikering av kompatibla virtuella datorer. 
    - När du kör Planeringsverktyget ange önskat rpo-mål i minuter. Rekommendationerna visar den bandbredd som krävs för att uppnå den RPO 100% och 90% av tiden. 
    - Bandbredd nätverksrekommendationer ta hänsyn till den bandbredd som krävs för totala antalet konfigurationsservrar och processervrar som rekommenderas i planeraren.
- **Krävs för Azure-kärnor**: Obs antalet kärnor som du behöver i mål-Azure-region, baserat på antalet kompatibla virtuella datorer. Om du inte har tillräckligt många kärnor, vid en redundansväxling Site Recovery inte att skapa nödvändiga virtuella Azure-datorer.
- **Rekommenderad batchstorlek för virtuella datorer**: Den rekommenderade omgångsstorleken är baserad på möjligheten att slutföra inledande replikering för batch inom 72 timmar som standard, samtidigt som kraven för ett Återställningspunktmål på 100%. Du kan ändra värdet för timme.

Du kan använda de här rekommendationerna för att planera för Azure-resurser, nätverkets bandbredd och batchbearbetning av virtuell dator.

## <a name="plan-azure-subscriptions-and-quotas"></a>Planera Azure-prenumerationer och kvoter

Vi vill se till att tillgängligt kvoter i målprenumerationen är tillräckliga för att hantera redundans.

**Aktivitet** | **Detaljer** | **Åtgärd**
--- | --- | ---
**Kontrollera kärnor** | Om kärnor i tillgänglig kvot inte lika med eller överskrider antalet totala mål vid tidpunkten för redundansväxlingen, misslyckas redundans. | Kontrollera du har tillräckligt många kärnor i målprenumerationen att uppfylla Distributionshanteraren core rekommendationen för VMware-datorer.<br/><br/> Kontrollera att Azure-kärnor uppfyller dina manuella beräkningar för fysiska servrar.<br/><br/> Kontrollera kvoter i Azure portal > **prenumeration**, klickar du på **användning + kvoter**.<br/><br/> [Läs mer](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) öka kvoter.
**Kontrollera begränsningar för redundans** | Antalet redundansväxlingar får inte överstiga gränserna för Site Recovery-redundans. |  Om redundans överskrider gränserna är kan du lägga till prenumerationer, och växla över till flera prenumerationer eller öka kvoten för en prenumeration. 


### <a name="failover-limits"></a>Gränser för redundans

Gränserna ange antal redundans som stöds av Site Recovery inom en timme, förutsatt att tre diskar per dator.

Vad efterlever medelvärdet? Azure för att starta en Azure-dator kräver vissa drivrutiner kan på miljö för start och -tjänster såsom DHCP anges för att starta automatiskt.
- Datorer som uppfyller redan de här inställningarna på plats.
- För datorer som kör Windows, kan du proaktivt Kontrollera efterlevnad och gör dem kompatibla om det behövs. [Läs mer](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linux-datorer ansluts endast kompatibla vid tidpunkten för redundans.

**Datorn uppfyller Azure?** | **Azure VM-gränser (hanterad disk redundans)**
--- | --- 
Ja | 2000
Nej | 1000

- Gränser förutsätter att minimal andra jobb som pågår i målregionen för prenumerationen.
- Vissa Azure-regioner är mindre och kan ha något lägre gränser.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Planera infrastrukturen och VM-anslutning

Efter redundansväxlingen till Azure måste du dina arbetsbelastningar ska fungera som de gjorde på plats och att användarna kan komma åt arbetsbelastningar som körs på virtuella Azure-datorer.

- [Läs mer](site-recovery-active-directory.md#test-failover-considerations) om redundansväxlingen lokala Active Directory eller DNS-infrastrukturen till Azure.
- [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) om hur du förbereder att ansluta till virtuella Azure-datorer efter redundansväxling.



## <a name="plan-for-source-capacity-and-requirements"></a>Planera för kapacitet för källa och krav

Det är viktigt att du har tillräckligt med konfigurationsservrar och skala ut processervrar som uppfyller krav på lagringskapacitet. När du börjar distributionen av storskaliga, börja med en konfigurationsserver och en enda skalbar processerver. Lägga till ytterligare servrar som du når gränsvärdena.

>[!NOTE]
> För virtuella VMware-datorer gör Kapacitetsplaneraren några rekommendationer om konfiguration och process-servrar du behöver. Vi rekommenderar att du använder de tabeller som ingår i följande procedurer i stället för att följa rekommendationen Deployment Planner. 


## <a name="set-up-a-configuration-server"></a>Ställa in en konfigurationsserver
 
Konfiguration av serverkapacitet påverkas av antalet datorer som replikeras och inte av data dataomsättningsfrekvensen. Använd gränserna definierade VM för att ta reda på om du behöver ytterligare konfiguration för servrar.

**CPU** | **Minne** | **Cachedisk** | **Replikerade datorn gräns**
 --- | --- | --- | ---
8 virtuella processorer<br> 2 platser * 4 kärnor @ 2,5 Ghz | 16 GB | 600 TB | Upp till 550 datorer<br> Förutsätter att varje dator har tre diskar på 100 GB vardera.

- Dessa gränser är baserade på en konfigurationsserver med en OVF-mall.
- Gränserna förutsätter att du inte använder den processerver som körs som standard på konfigurationsservern.

Om du vill lägga till en ny configuration server kan du följa instruktionerna nedan:

- [Ställa in en konfigurationsserver](vmware-azure-deploy-configuration-server.md) för VMware-VM-katastrofåterställning, med hjälp av en OVF-mall.
- [Ställa in en konfigurationsserver](physical-azure-set-up-source.md) manuellt för fysiska servrar eller för VMware-distributioner som inte kan använda en OVF-mall.

Observera att när du konfigurerar en konfigurationsserver:

- Det är viktigt att tänka på prenumeration och valv där den finns, eftersom dessa inte bör ändras efter installationen när du ställer in en konfigurationsserver. Om du behöver ändra valvet som du behöver ta bort koppling till konfigurationsservern från valvet och registrera om den. Detta stoppar replikering av virtuella datorer i valvet.
- Om du vill ställa in en konfigurationsserver med flera nätverkskort ska du göra detta under. Du kan inte göra detta när du registrerar konfigurationsservern i valvet.

## <a name="set-up-a-process-server"></a>Konfigurera en processerver

Processen serverkapacitet påverkas av data omsättningsfrekvensen, inte av antalet datorer som har aktiverats för replikering.

- Du bör alltid ha minst en skalbar processerver för stora distributioner.
- Använd följande tabell för att ta reda på om du behöver ytterligare servrar.
- Vi rekommenderar att du lägger till en server med den högsta spec. 


**CPU** | **Minne** | **Cachedisk** | **Dataomsättningsfrekvensen**
 --- | --- | --- | --- 
12 virtuella processorer<br> 2 platser * 6 kärnor @ 2,5 Ghz | 24 GB | 1 GB | Upp till 2 TB per dag

Konfigurera processervern enligt följande:

1. Granska den [krav](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Installera servern i den [portal](vmware-azure-set-up-process-server-scale.md#install-from-the-ui), eller från den [kommandoraden](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Konfigurera replikerade datorer om du vill använda den nya servern. Om du redan har datorer som replikeras:
    - Du kan [flytta](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) en server arbetsbelastning i hela processen till den nya processervern.
    - Du kan också [flytta](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) specifika virtuella datorer till den nya processervern.



## <a name="enable-large-scale-replication"></a>Aktivera storskaliga replikering

När du planerar kapaciteten och distribution av nödvändiga komponenter och infrastruktur, aktiverar du replikering för stort antal virtuella datorer.

1. Sortera datorer i batchar. Du aktiverar replikering för virtuella datorer i en batch- och sedan gå vidare till nästa batch.

    - För virtuella VMware-datorer, kan du använda den [rekommenderad batchstorlek för virtuella datorer](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) i Distributionshanteraren för rapporten.
    - För fysiska datorer rekommenderar vi du identifiera skalningsuppsättningarna baserat på datorer som har en liknande storlek och mängden data och på tillgängligt dataflöde i nätverket. Syftet är att batch-datorer som sannolikt kommer att slutföra sina inledande replikering i runt lika lång tid.
    
2. Om disken omsättning för en dator är hög eller överskrider gränserna i distributionen thePlanner, kan du flytta icke-kritiska filer som du inte behöver replikera datorn (till exempel log Dumpar eller tillfälliga filer). För virtuella VMware-datorer, kan du flytta filerna till en separat disk och sedan [undanta disken](vmware-azure-exclude-disk.md) från replikering.
3. Innan du aktiverar replikering, kontrollera som datorer uppfyller [replikeringskraven](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Konfigurera en replikeringsprincip för [virtuella VMware-datorer](vmware-azure-set-up-replication.md#create-a-policy) eller [fysiska servrar](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Aktivera replikering för [virtuella VMware-datorer](vmware-azure-enable-replication.md) eller [fysiska servrar](physical-azure-disaster-recovery.md#enable-replication). Detta startar den inledande replikeringen för de valda datorerna.

## <a name="monitor-your-deployment"></a>Övervaka distributionen

När du startar replikeringen för den första batchen med virtuella datorer, kan du börja övervaka din distribution på följande sätt:  

1. Tilldela en disaster recovery-administratör för att övervaka hälsotillståndet för replikerade datorer.
2. [Övervaka händelser](site-recovery-monitor-and-troubleshoot.md) för replikerade objekt och infrastrukturen.
3. [Övervaka hälsotillståndet](vmware-physical-azure-monitor-process-server.md) servrarnas skalbarhet processen.
4. Registrera dig för att hämta [e-postmeddelanden](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot.md#subscribe-to-email-notifications) för händelser för lättare övervakning.
5. Genomför regular [programåterställningstest](site-recovery-test-failover-to-azure.md), för att kontrollera att allt fungerar som förväntat.


## <a name="plan-for-large-scale-failovers"></a>Planera för storskaliga redundans

I händelse av katastrof, kan du behöva växla över ett stort antal datorer/arbetsbelastningar till Azure. Förbered för den här typen av händelse på följande sätt.

Du kan förbereda i förväg för redundans på följande sätt:

- [Förbereda din infrastruktur och dina virtuella datorer](#plan-infrastructure-and-vm-connectivity) så att dina arbetsbelastningar ska vara tillgängliga efter redundans och så att användarna kan komma åt virtuella Azure-datorer.
- Obs den [redundans gränser](#failover-limits) tidigare i det här dokumentet. Kontrollera att din redundans kommer faller inom gränserna.
- Kör regular [programåterställningstest](site-recovery-test-failover-to-azure.md). Övningar hjälper till att:
    - Hitta luckor i distributionen före redundans.
    - Beräkna RTO för slutpunkt till slutpunkt för dina appar.
    - Beräkna RPO för slutpunkt till slutpunkt för dina arbetsbelastningar.
    - Identifiera IP-adressintervallet orsakar en konflikt.
    - När du kör tester, rekommenderar vi att du inte använda produktionsnätverk för tester, Undvik att använda samma namn på undernät i nätverk för produktion och testning och rensar redundanstestning efter varje test.

För att köra storskaliga redundans, rekommenderar vi följande:

1. Skapa återställningsplaner för arbetsbelastningsväxling vid fel.
    - Varje återställningsplan kan utlösa redundans för upp till 50 datorer.
    - [Läs mer](recovery-plan-overview.md) om återställningsplaner.
2. Lägga till Azure Automation runbook-skript i återställningsplaner för att automatisera några manuella aktiviteter i Azure. Vanliga uppgifter innefattar att konfigurera belastningsutjämnare, uppdaterar DNS osv. [Läs mer](site-recovery-runbook-automation.md)
2. Förbereda Windows-datorer före redundans, så att de är kompatibla med Azure-miljön. [Redundans gränser](#plan-azure-subscriptions-and-quotas) är högre för datorer som är kompatibla. [Läs mer](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) om runbook-flöden.
4.  Utlösa redundans med den [Start AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell-cmdleten tillsammans med en återställningsplan.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka Site Recovery](site-recovery-monitor-and-troubleshoot.md)
