---
title: Felsöka replikeringsproblem för haveriberedskap av virtuella datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft-dokument
description: Den här artikeln innehåller felsökningsinformation för vanliga replikeringsproblem vid haveriberedskap av virtuella datorer med VMware och fysiska servrar till Azure med hjälp av Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 7237bb7e0538ba1a9b6333ccb6589efe657a247d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74423959"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Felsöka replikeringsproblem för virtuella datorer med VMware och fysiska servrar

I den hÃ¤r artikeln beskrivs några vanliga problem och specifika fel som kan uppstÃ¤r uppgÃ¤ã¤r uppstÃ¤nsmåld nÃ¤r du replikerar lokala virtuella datorer med hmware och fysiska servrar till Azure med [site recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Steg 1: Övervaka processserverns hälsa

Site Recovery använder [processservern](vmware-physical-azure-config-process-server-overview.md#process-server) för att ta emot och optimera replikerade data och skicka den till Azure.

Vi rekommenderar att du övervakar hälsotillståndet för processservrar i portalen, för att säkerställa att de är anslutna och fungerar korrekt och att replikeringen fortskrider för källdatorerna som är associerade med processservern.

- [Läs mer om](vmware-physical-azure-monitor-process-server.md) övervakning av processservrar.
- [Granska metodtips](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Felsöka](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) processserverhälsa.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Steg 2: Felsöka anslutnings- och replikeringsproblem

Inledande och pågående replikeringsfel orsakas ofta av anslutningsproblem mellan källservern och processservern eller mellan processservern och Azure. 

Lös dessa problem genom [att felsöka anslutning och replikering](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Steg 3: Felsöka källdatorer som inte är tillgängliga för replikering

NÃ¤r du fÃ¶rsÃ¶ks fÃ¶rsÃ¶k att välja källdÃ¤r fÃ¶r replikering med platsåterställning kanske datorn inte är tillgänglig av något av följande skäl:

* **Två virtuella datorer med samma instans UUID:** Om två virtuella datorer under vCenter har samma instans UUID, visas den första virtuella datorn som upptäcktes av konfigurationsservern i Azure-portalen. LÃ¶s problemet genom att kontrollera att inga två virtuella datorer har samma lÃ¤5 sproblem UUID. Det här scenariot ses ofta i fall där en virtuell säkerhetskopiering blir aktiv och är inloggad i våra identifieringsposter. Se [Azure Site Recovery VMware-to-Azure: Så här rensar du dubblett- eller inaktuella poster](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) för att matcha.
* **Felaktiga vCenter-användaruppgifter:** Kontrollera att du har lagt till rätt vCenter-autentiseringsuppgifter när du konfigurerar konfigurationsservern med hjälp av OVF-mallen eller enhetliga inställningar. Autentiseringsuppgifter som du lade till under installationen finns i [Ändra autentiseringsuppgifter för automatisk identifiering](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **vCenter otillräckliga privilegier:** Om de behörigheter som anges för att komma åt vCenter inte har de behörigheter som krävs, kan det inte uppstå några identifieringsdetã¤rdÃ¤ndare. Kontrollera att behörigheterna som beskrivs i [Förbered ett konto för automatisk identifiering](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) läggs till i vCenter-användarkontot.
* **Hanteringsservrar för Azure Site Recovery:** Om den virtuella datorn används som hanteringsserver under en eller flera av följande roller - Konfigurationsserver /skalningsprocessserver / Huvudmålserver, kommer du inte att kunna välja den virtuella datorn från portalen. Hanteringsservrar kan inte replikeras.
* **Redan skyddad/misslyckades via Azure Site Recovery-tjänster:** Om den virtuella datorn redan är skyddad eller misslyckades via Site Recovery är den virtuella datorn inte tillgänglig för att välja skydd i portalen. Kontrollera att den virtuella datorn du letar efter i portalen inte redan skyddas av någon annan användare eller under en annan prenumeration.
* **vCenter inte anslutet**: Kontrollera om vCenter är i anslutet läge. Kontrollera genom att gå till Recovery Services-valv > infrastruktur för platsåterställning > konfigurationsservrar > Klicka på respektive konfigurationsserver > ett blad öppnas till höger med information om associerade servrar. Kontrollera om vCenter är anslutet. Om det är i tillståndet "Inte ansluten" löser du problemet och uppdaterar sedan [konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server) på portalen. Därefter visas den virtuella datorn på portalen.
* **ESXi avstängd:** Om ESXi-värd under vilken den virtuella datorn finns är i avstängningsläge, kommer den virtuella datorn inte att listas eller kommer inte att kunna väljas på Azure-portalen. Slå på ESXi-värden, [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server) på portalen. Därefter visas den virtuella datorn på portalen.
* **Väntande omstart:** Om det finns en väntande omstart på den virtuella datorn kan du inte välja datorn på Azure-portalen. Se till att slutföra väntande omstart aktiviteter, [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Därefter visas den virtuella datorn på portalen.
* **IP hittades inte:** Om den virtuella datorn inte har en giltig IP-adress som är associerad med den, kommer du inte att kunna välja datorn på Azure-portalen. Kontrollera att du tilldelar en giltig IP-adress till den virtuella datorn, [uppdaterar konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Därefter visas den virtuella datorn på portalen.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Felsöka skyddade virtuella datorer nedtonade i portalen

Virtuella datorer som replikeras under Webbplatsåterställning är inte tillgängliga i Azure-portalen om det finns dubblettposter i systemet. Mer information om hur du tar bort inaktuella poster och löser problemet finns i [Azure Site Recovery VMware-to-Azure: Så här rensar du dubblett- eller inaktuella poster](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Ingen krasch konsekvent återställningspunkt tillgänglig för den virtuella datorn i de senaste "XXX" minuter

Några av de vanligaste problemen listas nedan

### <a name="initial-replication-issues-error-78169"></a>Inledande replikeringsproblem [fel 78169]

Över ett ovanstående se till att det inte finns några anslutnings-, bandbredds- eller tidssynkroniseringsrelaterade problem, se till att:

- Inget antivirusprogram blockerar Azure Site Recovery. Läs [mer](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) om mapputeslutningar som krävs för Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Källmaskiner med hög omsättning [fel 78188]

Möjliga orsaker:
- Dataändringshastigheten (skrivbyten/sek) på de angivna diskarna på den virtuella datorn är mer än [de azure site recovery-gränser](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) som stöds för replikeringsmållagringskontotypen.
- Det finns en plötslig ökning av omsättningshastigheten på grund av vilken hög mängd data väntar på överföring.

Så här löser du problemet:
- Kontrollera att mållagringskontotypen (Standard eller Premium) etableras enligt omsättningshastighetskravet vid källan.
- Om du redan replikerar till en Premium-hanterad disk (asrseeddisktyp) kontrollerar du att diskens storlek stöder den observerade omsättningshastigheten enligt gränserna för webbplatsåterställning. Du kan öka storleken på asrseeddisken om det behövs. Följ stegen nedan:
    - Navigera till diskbladet för den påverkade replikerade datorn och kopiera replikdisknamnet
    - Navigera till den här replikhanterade disken
    - Du kan se en banderoll på bladet Översikt som säger att en SAS-URL har genererats. Klicka på den här bannern och avbryt exporten. Ignorera det här steget om du inte ser banderollen.
    - Så snart SAS-URL:en har återkallats går du till konfigurationsbladet för den hanterade disken och ökar storleken så att ASR stöder den observerade omsättningshastigheten på källdisken
- Om den observerade omsättningen är tillfällig väntar du några timmar på att den väntande dataöverföringen ska komma ikapp och skapa återställningspunkter.
- Om disken innehåller icke-kritiska data som tillfälliga loggar, testdata etc., överväg att flytta dessa data någon annanstans eller helt utesluta den här disken från replikering
- Om problemet kvarstår använder du [distributionsplaneraren för platsåterställning](site-recovery-deployment-planner.md#overview) för att planera replikering.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Källmaskiner utan hjärtslag [fel 78174]

Detta händer när Azure Site Recovery Mobility agent på source-datorn inte kommunicerar med Configuration Server (CS).

LÃ¶s problemet genom att anã¶ndra nÃ¤r du ã¤ndrar nÃ¤r nÃ¤r nÃ¤llningen frÃ¥n källdatorn till Config Server:

1. Kontrollera att källdatorn körs.
2. Logga in på källdatorn med ett konto som har administratörsbehörighet.
3. Kontrollera att följande tjänster körs och om inte starta om tjänsterna:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application Service
4. På källmaskinen undersöker du loggarna på platsen för felinformation:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Processserver utan pulsslag [fel 806]
Om det inte finns några pulsslag från Process Server (PS), kontrollera att:
1. PS VM är igång
2. Kontrollera följande loggar på PS för felinformation:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Huvudmålserver utan pulsslag [fel 78022]

Detta händer när Azure Site Recovery Mobility agent på huvudmålet inte kommunicerar med konfigurationsservern.

LÃ¶s problemet genom att anã¤ nÃ¤r du ansÃ¶ser servicestatus:

1. Kontrollera att den virtuella datorn för huvudmål körs.
2. Logga in på den virtuella datorn för huvudmål med ett konto som har administratörsbehörighet.
    - Kontrollera att tjänsten är igång. Om den körs startar du om tjänsten
    - Kontrollera loggarna på platsen för felinformation:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. Om du vill registrera huvudmålet med konfigurationsservern navigerar du till mappen **%PROGRAMDATA%\ASR\Agent**och kör följande i kommandotolken:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Fel-ID 78144 - Ingen appkonsekvent återställningspunkt tillgänglig för den virtuella datorn under de senaste XXX-minuterna

Förbättringar har gjorts i mobility agent [9.23](vmware-physical-mobility-service-overview.md#from-923-version-onwards) & [9.27](site-recovery-whats-new.md#update-rollup-39) versioner för att hantera VSS-installationsfel. Se till att du är på de senaste versionerna för bästa vägledning om felsökning VSS-fel.

Några av de vanligaste problemen listas nedan

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Orsak 1: Känt problem i SQL-servern 2008/2008 R2 
**Så här åtgärdar du** : Det finns ett känt problem med SQL-servern 2008/2008 R2. Se den här [KB-artikeln Azure Site Recovery Agent eller andra VSS-säkerhetskopiering som inte är komponent misslyckas för en server som är värd för SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Orsak 2: Azure Site Recovery-jobb misslyckas på servrar som är värdar för alla versioner av SQL Server-instanser med AUTO_CLOSE DBs 
**Så här fixar du:** Se [kb-artikel](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Orsak 3: Känt problem i SQL Server 2016 och 2017
**Så här fixar du:** Se [kb-artikel](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 


### <a name="more-causes-due-to-vss-related-issues"></a>Fler orsaker på grund av VSS-relaterade problem:

Om du vill felsöka ytterligare kontrollerar du filerna på källmaskinen för att få den exakta felkoden för fel:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Hur hittar du felen i filen?
Sök efter strängen "vacpError" genom att öppna filen vacp.log i en redigerare
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

I exemplet ovan är **2147754994** felkoden som berättar om felet som visas nedan

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS-brännare är inte installerad - Fel 2147221164 

*Åtgärda:* För att generera programkonsekvenstagg använder Azure Site Recovery Microsoft Volume Shadow copy Service (VSS). Den installerar en VSS-provider för dess funktion för att ta ögonblicksbilder av appkonsekvenser. Denna VSS-provider installeras som en tjänst. Om VSS Provider-tjänsten inte är installerad misslyckas skapandet av ögonblicksbilder av programkonsekvens med fel-ID 0x80040154 "Klassen har inte registrerats". </br>
Se [artikel för felsökning av VSS-brännareinstallation](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS-brännare är inaktiverad - Fel 2147943458

**Åtgärda:** För att generera programkonsekvenstagg använder Azure Site Recovery Microsoft Volume Shadow copy Service (VSS). Den installerar en VSS-provider för dess funktion för att ta ögonblicksbilder av appkonsekvenser. Denna VSS-provider installeras som en tjänst. Om VSS Provider-tjänsten inaktiveras misslyckas skapandet av ögonblicksbilder av programkonsekvens med fel-ID:t "Den angivna tjänsten är inaktiverad och kan inte startas(0x80070422)". </br>

- Om VSS är inaktiverat
    - Kontrollera att starttypen för VSS Provider-tjänsten är inställd **på Automatisk**.
    - Starta om följande tjänster:
        - VSS-tjänst
        - VSS-provider för Azure Site Recovery
        - VDS-tjänst

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS-LEVERANTÖR NOT_REGISTERED - Fel 2147754756

**Åtgärda:** För att generera programkonsekvenstagg använder Azure Site Recovery Microsoft Volume Shadow copy Service (VSS). Kontrollera om tjänsten Azure Site Recovery VSS Provider är installerad eller inte. </br>

- Försök igen providerinstallationen med följande kommandon:
- Avinstallera befintlig provider: C:\Program-filer (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Installera om: C:\Program-filer (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Kontrollera att starttypen för VSS Provider-tjänsten är inställd **på Automatisk**.
    - Starta om följande tjänster:
        - VSS-tjänst
        - VSS-provider för Azure Site Recovery
        - VDS-tjänst

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du ställa din fråga i [Azure Site Recovery-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Vi har en aktiv gemenskap, och en av våra ingenjörer kan hjälpa dig.
