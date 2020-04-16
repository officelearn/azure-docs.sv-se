---
title: Skala VMware/fysisk haveriberedskap med Azure Site Recovery
description: Lär dig hur du konfigurerar haveriberedskap till Azure för ett stort antal lokala virtuella datorer med VMware eller fysiska servrar med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a3a2317554f02dc1f1198d8019bbfdb50e3cc71c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409773"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Konfigurera haveriberedskap i stor skala för virtuella datorer/fysiska VMware-servrar

I den här artikeln beskrivs hur du konfigurerar haveriberedskap till Azure för ett stort antal (> 1000) lokala virtuella datorer för VMware eller fysiska servrar i produktionsmiljön med hjälp av [Azure Site Recovery-tjänsten.](site-recovery-overview.md)


## <a name="define-your-bcdr-strategy"></a>Definiera din BCDR-strategi

Som en del av din strategi för affärskontinuitet och haveriberedskap (BCDR) definierar du återställningspunktmål (RPOs) och återställningstidsmål (RTOs) för dina affärsappar och arbetsbelastningar. RTO mäter den tids- och servicenivå inom vilken en affärsapp eller process måste återställas och vara tillgänglig för att undvika kontinuitetsproblem.
- Site Recovery ger kontinuerlig replikering för virtuella datorer och fysiska servrar och ett [serviceavtal](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) för RTO.
- När du planerar för storskalig haveriberedskap för virtuella datorer med VMware och ta reda på vilka Azure-resurser du behöver, kan du ange ett RTO-värde som ska användas för kapacitetsberäkningar.


## <a name="best-practices"></a>Bästa praxis

Några allmänna metodtips för storskalig katastrofåterställning. Dessa metodtips beskrivs mer i detalj i nästa avsnitt i dokumentet.

- **Identifiera målkrav:** Uppskatta kapacitets- och resursbehov i Azure innan du ställer in haveriberedskap.
- **Planera för site recovery-komponenter:** Ta reda på vilka site recovery-komponenter (konfigurationsserver, processservrar) du behöver för att uppfylla din uppskattade kapacitet.
- **Konfigurera en eller flera skalningsprocessservrar**: Använd inte processservern som körs som standard på konfigurationsservern. 
- **Kör de senaste uppdateringarna:** Site Recovery-teamet släpper regelbundet nya versioner av Site Recovery-komponenter och du bör se till att du kör de senaste versionerna. Du kan hjälpa till med det genom att spåra [det som är nytt](site-recovery-whats-new.md) för uppdateringar och aktivera och installera [uppdateringar](service-updates-how-to.md) när de släpps.
- **Övervaka proaktivt**: När du får igång haveriberedskap bör du proaktivt övervaka status och hälsa för replikerade datorer och infrastrukturresurser.
- **Katastrofåterställningsövningar:** Du bör köra katastrofåterställningsövningar regelbundet. Dessa påverkar inte din produktionsmiljö, men hjälper till att säkerställa att redundans till Azure fungerar som förväntat när det behövs.



## <a name="gather-capacity-planning-information"></a>Samla in information om kapacitetsplanering

Samla in information om din lokala miljö för att bedöma och uppskatta dina mål (Azure) kapacitetsbehov.
- För VMware kör du distributionsplaneraren för virtuella datorer med VMware för att göra detta.
- Samla in informationen manuellt för fysiska servrar.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Köra distributionsplaneraren för virtuella datorer med VMware

Distributionsplaneraren hjälper dig att samla in information om din lokala VMware-miljö.

- Kör distributionsplaneraren under en period som representerar typisk omsättning för dina virtuella datorer. Detta kommer att generera mer exakta uppskattningar och rekommendationer.
- Vi rekommenderar att du kör distributionsplaneraren på konfigurationsserverdatorn, eftersom Planeraren beräknar dataflödet från den server där den körs. [Läs mer](site-recovery-vmware-deployment-planner-run.md#get-throughput) om mätning av dataflöde.
- Om du ännu inte har konfigurerat en konfigurationsserver:
    - [Få en översikt över](vmware-physical-azure-config-process-server-overview.md) komponenter för webbplatsåterställning.
    - [Konfigurera en konfigurationsserver](vmware-azure-deploy-configuration-server.md)för att köra distributionsplaneraren på den.

Kör sedan Planner enligt följande:

1. [Läs mer om](site-recovery-deployment-planner.md) distributionsplaneraren. Du kan ladda ner den senaste versionen från portalen, eller [ladda ner den direkt](https://aka.ms/asr-deployment-planner).
2. Granska [förutsättningarna](site-recovery-deployment-planner.md#prerequisites) och [de senaste uppdateringarna](site-recovery-deployment-planner-history.md) för distributionsplaneraren och [hämta och extrahera](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) verktyget.
3. [Kör distributionsplaneraren](site-recovery-vmware-deployment-planner-run.md) på konfigurationsservern.
4. [Generera en rapport](site-recovery-vmware-deployment-planner-run.md#generate-report) för att sammanfatta uppskattningar och rekommendationer.
5. Analysera [rapportrekommendationerna](site-recovery-vmware-deployment-planner-analyze-report.md) och [kostnadsuppskattningarna](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Som standard är verktyget konfigurerat för att profilera och genererar rapport för upp till 1 000 virtuella datorer. Du kan ändra den här gränsen genom att öka nyckelvärdet MaxVMsSupported i filen ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Planera målkrav (Azure) krav och kapacitet

Med hjälp av dina samlade uppskattningar och rekommendationer kan du planera för målresurser och kapacitet. Om du körde distributionsplaneraren för virtuella datorer med VMware kan du använda ett antal [rapportrekommendationer](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) som hjälper dig.

- **Kompatibla virtuella datorer:** Använd det här numret för att identifiera antalet virtuella datorer som är redo för haveriberedskap till Azure. Rekommendationer om nätverksbandbredd och Azure-kärnor baseras på det här numret.
- **Obligatorisk nätverksbandbredd:** Observera den bandbredd du behöver för deltareplikering av kompatibla virtuella datorer. 
    - När du kör Planner anger du önskad RPO på några minuter. Rekommendationerna visar den bandbredd som behövs för att uppfylla den RPO 100% och 90% av tiden. 
    - Rekommendationerna för nätverksbandbredd tar hänsyn till den bandbredd som behövs för det totala antalet konfigurationsservrar och processservrar som rekommenderas i planeraren.
- **Nödvändiga Azure-kärnor:** Observera antalet kärnor du behöver i Azure-regionen för mål, baserat på antalet kompatibla virtuella datorer. Om du inte har tillräckligt med kärnor kan site recovery inte skapa de nödvändiga virtuella Azure-datorerna på redundansplats.
- **Rekommenderad VM-batchstorlek:** Den rekommenderade batchstorleken baseras på möjligheten att slutföra den första replikeringen för batchen inom 72 timmar som standard, samtidigt som en RPO på 100 %. Timvärdet kan ändras.

Du kan använda dessa rekommendationer för att planera för Azure-resurser, nätverksbandbredd och VM-batchbearbetning.

## <a name="plan-azure-subscriptions-and-quotas"></a>Planera Azure-prenumerationer och kvoter

Vi vill se till att tillgängliga kvoter i målprenumerationen är tillräckliga för att hantera redundans.

**Aktivitet** | **Detaljer** | **Åtgärd**
--- | --- | ---
**Kontrollera kärnor** | Om kärnorna i den tillgängliga kvoten inte är lika med eller överstiger det totala antalet mål vid tidpunkten för redundansen misslyckas redundans. | För virtuella datorer med VMware kontrollerar du att du har tillräckligt med kärnor i målprenumerationen för att uppfylla huvudrekommendationen för Distributionsplaneraren.<br/><br/> För fysiska servrar kontrollerar du att Azure-kärnor uppfyller dina manuella uppskattningar.<br/><br/> Om du vill kontrollera kvoter klickar du på **Användning + kvoter**i Azure-portalen > **prenumeration**.<br/><br/> [Läs mer](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) om att öka kvoterna.
**Kontrollera redundansgränser** | Antalet redundans får inte överskrida redundansgränser för platsåterställning. |  Om redundans överskrider gränserna kan du lägga till prenumerationer och växla över till flera prenumerationer eller öka kvoten för en prenumeration. 


### <a name="failover-limits"></a>Redundansgränser

Gränserna anger antalet redundans som stöds av Site Recovery inom en timme, förutsatt tre diskar per dator.

Vad betyder det att följa? För att starta en virtuell Azure-dator kräver Azure att vissa drivrutiner är i starttillstånd och tjänster som DHCP som ska ställas in för att starta automatiskt.
- Maskiner som uppfyller kommer redan att ha dessa inställningar på plats.
- För datorer som kör Windows kan du proaktivt kontrollera efterlevnaden och göra dem kompatibla om det behövs. [Läs mer](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linux-maskiner ställs endast in i överensstämmelse vid tidpunkten för redundans.

**Datorn följer Azure?** | **Azure VM-gränser (lyckades disk redundans)**
--- | --- 
Ja | 2000
Inga | 1000

- Gränser förutsätter att minimala andra jobb pågår i målregionen för prenumerationen.
- Vissa Azure-regioner är mindre och kan ha något lägre gränser.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Planera infrastruktur och VM-anslutning

Efter redundans till Azure behöver du dina arbetsbelastningar för att fungera som de gjorde lokalt och för att göra det möjligt för användare att komma åt arbetsbelastningar som körs på virtuella Azure-datorer.

- [Läs mer](site-recovery-active-directory.md#test-failover-considerations) om att misslyckas med din Active Directory eller DNS lokalt infrastruktur till Azure.
- [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) om hur du förbereder anslutningen till virtuella Azure-datorer efter redundans.



## <a name="plan-for-source-capacity-and-requirements"></a>Planera för källkapacitet och krav

Det är viktigt att du har tillräckligt med konfigurationsservrar och utskalningsprocessservrar för att uppfylla kapacitetskraven. När du börjar din storskaliga distribution, börja med en enda konfigurationsserver och en enda skalningsprocessserver. När du når de föreskrivna gränserna lägger du till ytterligare servrar.

>[!NOTE]
> För virtuella datorer med VMware ger distributionsplaneraren några rekommendationer om de konfigurations- och processservrar du behöver. Vi rekommenderar att du använder tabellerna som ingår i följande procedurer i stället för att följa rekommendationen Deployment Planner. 


## <a name="set-up-a-configuration-server"></a>Konfigurera en konfigurationsserver
 
Konfigurationsserverkapacitet påverkas av antalet datorer som replikeras och inte av dataomsättningshastigheten. Om du vill ta reda på om du behöver ytterligare konfigurationsservrar använder du dessa definierade vm-gränser.

**Cpu** | **Minne** | **Cachedisk** | **Gränsen för replikerad dator**
 --- | --- | --- | ---
8 virtuella processorer<br> 2 uttag * 4 kärnor @ 2,5 Ghz | 16 GB | 600 GB | Upp till 550 maskiner<br> Förutsätter att varje dator har tre diskar på 100 GB vardera.

- Dessa gränser baseras på en konfigurationsserver som konfigurerats med hjälp av en OVF-mall.
- Gränserna förutsätter att du inte använder den processserver som körs som standard på konfigurationsservern.

Om du behöver lägga till en ny konfigurationsserver följer du dessa instruktioner:

- [Konfigurera en konfigurationsserver](vmware-azure-deploy-configuration-server.md) för VMware VM-haveriberedskap med hjälp av en OVF-mall.
- [Konfigurera en konfigurationsserver](physical-azure-set-up-source.md) manuellt för fysiska servrar eller för VMware-distributioner som inte kan använda en OVF-mall.

När du konfigurerar en konfigurationsserver bör du tänka på följande:

- När du konfigurerar en konfigurationsserver är det viktigt att tänka på prenumerationen och valvet där den finns, eftersom dessa inte bör ändras efter installationen. Om du behöver ändra valvet måste du ta bort kopplingen mellan konfigurationsservern från valvet och registrera om det. Detta stoppar replikering av virtuella datorer i valvet.
- Om du vill konfigurera en konfigurationsserver med flera nätverkskort bör du göra detta under konfigurationen. Du kan inte göra detta efter registreringen av konfigurationsservern i valvet.

## <a name="set-up-a-process-server"></a>Konfigurera en processserver

Processserverkapacitet påverkas av dataomsättningshastigheter och inte av antalet datorer som är aktiverade för replikering.

- För stora distributioner bör du alltid ha minst en skalningsprocessserver.
- Om du vill ta reda på om du behöver ytterligare servrar använder du följande tabell.
- Vi rekommenderar att du lägger till en server med högsta specifikation. 


**Cpu** | **Minne** | **Cachedisk** | **Omsättningshastighet**
 --- | --- | --- | --- 
12 virtuella processorer<br> 2 uttag *6 kärnor @ 2,5 Ghz | 24 GB | 1 GB | Upp till 2 TB per dag

Konfigurera processservern på följande sätt:

1. Granska [förutsättningarna](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Installera servern i [portalen](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)eller från [kommandoraden](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Konfigurera replikerade datorer för att använda den nya servern. Om du redan har datorer som replikerar:
    - Du kan [flytta](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) en hel processserverarbetsbelastning till den nya processservern.
    - Du kan också [flytta](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) specifika virtuella datorer till den nya processservern.



## <a name="enable-large-scale-replication"></a>Aktivera storskalig replikering

När du har planerat kapacitet och distribuerat de nödvändiga komponenterna och infrastrukturen aktiverar du replikering för ett stort antal virtuella datorer.

1. Sortera maskiner i buntar. Du aktiverar replikering för virtuella datorer i en batch och går sedan vidare till nästa batch.

    - För virtuella datorer med VMware kan du använda den [rekommenderade vm-batchstorleken](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) i rapporten Deployment Planner.
    - För fysiska datorer rekommenderar vi att du identifierar batchar baserat på datorer som har en liknande storlek och mängd data och på tillgängligt nätverksdataflöde. Målet är att batch-datorer som sannolikt kommer att slutföra sin första replikering på ungefär samma tid.
    
2. Om diskomsättningen för en dator är hög eller överskrider gränserna i Deployment thePlanner kan du flytta icke-kritiska filer som du inte behöver replikera (till exempel loggdumpar eller temporära filer) från datorn. För virtuella datorer med VMware kan du flytta dessa filer till en separat disk och sedan [utesluta disken](vmware-azure-exclude-disk.md) från replikering.
3. Innan du aktiverar replikering kontrollerar du att datorer uppfyller [replikeringskraven](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Konfigurera en replikeringsprincip för [virtuella datorer eller](vmware-azure-set-up-replication.md#create-a-policy) fysiska [servrar](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Aktivera replikering för [virtuella datorer eller](vmware-azure-enable-replication.md) fysiska [servrar](physical-azure-disaster-recovery.md#enable-replication). Detta startar den första replikeringen för de valda datorerna.

## <a name="monitor-your-deployment"></a>Övervaka distributionen

När du har sparkat igång replikeringen för den första omgången virtuella datorer börjar du övervaka distributionen enligt följande:  

1. Tilldela en haveriberedskapsadministratör för att övervaka hälsotillståndet för replikerade datorer.
2. [Övervaka händelser](site-recovery-monitor-and-troubleshoot.md) för replikerade objekt och infrastrukturen.
3. [Övervaka hälsan hos](vmware-physical-azure-monitor-process-server.md) dina utskalningsprocessservrar.
4. Registrera dig för att få [e-postmeddelanden](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) för evenemang, för enklare övervakning.
5. Utför regelbundna [haveriberedskapsövningar](site-recovery-test-failover-to-azure.md)för att säkerställa att allt fungerar som förväntat.


## <a name="plan-for-large-scale-failovers"></a>Planera för storskaliga redundans

I händelse av katastrof kan du behöva växla över ett stort antal datorer/arbetsbelastningar till Azure. Förbered dig för den här typen av händelser enligt följande.

Du kan förbereda dig i förväg för redundans enligt följande:

- [Förbered din infrastruktur och virtuella datorer](#plan-infrastructure-and-vm-connectivity) så att dina arbetsbelastningar blir tillgängliga efter redundans och så att användarna kan komma åt virtuella Azure-datorer.
- Observera [redundansgränserna](#failover-limits) tidigare i det här dokumentet. Se till att dina redundans kommer att falla inom dessa gränser.
- Kör regelbundna [haveriberedskapsövningar](site-recovery-test-failover-to-azure.md). Borrar hjälper till att:
    - Hitta luckor i distributionen före redundans.
    - Uppskatta heltäckande RTO för dina appar.
    - Uppskatta end-to-end RPO för dina arbetsbelastningar.
    - Identifiera konflikter i IP-adressintervall.
    - När du kör övningar rekommenderar vi att du inte använder produktionsnätverk för borrmaskiner, undviker att använda samma undernätsnamn i produktions- och testnätverk och rensar testundans efter varje övning.

Om du vill köra en storskalig redundans rekommenderar vi följande:

1. Skapa återställningsplaner för arbetsbelastningsöverslag.
    - Varje återställningsplan kan utlösa redundans på upp till 100 datorer.
    - [Läs mer](recovery-plan-overview.md) om återställningsplaner.
2. Lägg till Azure Automation-runbook-skript i återställningsplaner för att automatisera eventuella manuella uppgifter på Azure. Typiska uppgifter är att konfigurera belastningsutjämnare, uppdatera DNS etc. [Läs mer](site-recovery-runbook-automation.md)
2. Förbered Windows-datorer före redundans så att de följer Azure-miljön. [Redundansgränser](#plan-azure-subscriptions-and-quotas) är högre för maskiner som uppfyller kraven. [Läs mer](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) om runbooks.
4.  Utlösa redundans med [Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell cmdlet, tillsammans med en återställningsplan.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Övervaka Site Recovery](site-recovery-monitor-and-troubleshoot.md)
