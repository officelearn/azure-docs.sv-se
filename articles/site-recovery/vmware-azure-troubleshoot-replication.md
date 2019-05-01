---
title: Felsöka problem med replikering för akutåterställning av virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller information om att felsöka vanliga replikeringsproblem under haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 3221b2bd18b8b0756f280d88fffc6016d0498b8f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924815"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Felsöka problem med replikering för virtuella VMware-datorer och fysiska servrar

Den här artikeln beskrivs några vanliga problem och specifika fel som kan uppstå när du replikerar lokala virtuella VMware-datorer och fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Steg 1: Övervakare för hälsa för processen-server

Site Recovery använder den [processervern](vmware-physical-azure-config-process-server-overview.md#process-server) att ta emot och optimera replikerade data och skicka den till Azure.

Vi rekommenderar att du övervaka hälsotillståndet för processervrar i portalen, så att de är anslutna och fungerar korrekt och att replikeringen fortlöper för källdatorer som är kopplade till processervern.

- [Lär dig mer om](vmware-physical-azure-monitor-process-server.md) övervakning processervrar.
- [Granska metodtips](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Felsöka](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) process server-hälsotillstånd.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Steg 2: Felsöka problem med anslutningen och replikering

Initiala lösenord och fortlöpande replikeringsfel orsakas ofta av problem med nätverksanslutningen mellan käll- och processervern eller mellan processervern och Azure. 

Du kan lösa dessa problem [Felsök anslutning och replikering](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Steg 3: Felsöka källdatorer som inte är tillgängliga för replikering

När du försöker markera källdatorn för att aktivera replikering med Site Recovery kanske datorn inte är tillgängliga för en av följande orsaker:

* **Två virtuella datorer med samma instans UUID**: Om två virtuella datorer under vCenter har samma instans UUID, visas den första virtuella datorn som identifierats av konfigurationsservern på Azure-portalen. Se till att inga två virtuella datorer har samma instans UUID för att lösa problemet. Det här scenariot uppstår vanligen i fall där en säkerhetskopierade virtuell dator blir aktiv och är inloggad i vår identifieringsposter. Referera till [Azure Site Recovery VMware till Azure: Hur du rensar dubbletter eller inaktuella poster](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) att lösa.
* **Felaktig vCenter användarautentiseringsuppgifter**: Se till att du har lagt till korrekt vCenter-autentiseringsuppgifter när du ställer in konfigurationsservern med hjälp av OVF-mall eller en enhetlig installation. För att kontrollera de autentiseringsuppgifter som du har lagt till under installationen, se [ändra autentiseringsuppgifter för automatisk identifiering](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **inte tillräcklig behörighet för vCenter**: Om de behörigheter som finns att få åtkomst till vCenter inte har behörigheterna som krävs kan kan identifiera virtuella datorer misslyckas. Se till att behörigheterna som beskrivs i [förbereda ett konto för automatisk identifiering](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) läggs till vCenter-användarkonto.
* **Azure Site Recovery-hanteringsservrar**: Om den virtuella datorn används som hanteringsserver under en eller flera av följande roller - Configuration server /scale-out processervern / Master-målserver, och du inte kommer att kunna välja den virtuella datorn från portalen. Management-servrar kan inte replikeras.
* **Redan skyddad/redundansväxlats via Azure Site Recovery services**: Om den virtuella datorn är redan skyddad eller har redundansväxlats genom Site Recovery, är den virtuella datorn inte kan väljas för skydd i portalen. Se till att den virtuella datorn som du söker i portalen inte redan skyddas av en annan användare eller en annan prenumeration.
* **vCenter som inte är anslutna**: Kontrollera att vCenter är i anslutet tillstånd. Du kan kontrollera genom att gå till Recovery Services-valv > Site Recovery-infrastruktur > Konfigurationsservrar > Klicka på respektive konfigurationsservern > öppnas ett blad på din direkt med information om associerade servrar. Kontrollera om vCenter är ansluten. Om det är i tillståndet ”inte anslutna” Lös problemet och sedan [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server) på portalen. Därefter kan listas virtuella datorer på portalen.
* **ESXi avstängd**: Om ESXi-värd som den virtuella datorn finns i avstängd, har virtuella datorn visas inte eller kan inte väljas på Azure portal. Starta ESXi-värd [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server) på portalen. Därefter kan listas virtuella datorer på portalen.
* **Väntar på omstart**: Om det finns en väntande omstart på den virtuella datorn, kommer sedan du inte att kunna välja dator på Azure-portalen. Se till att slutföra väntande omstart-aktiviteter [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Därefter kan listas virtuella datorer på portalen.
* **Det gick inte att hitta IP**: Om den virtuella datorn inte har en giltig IP-adress som är kopplade till den, kommer sedan du inte att kunna välja dator på Azure-portalen. Se till att tilldela en giltig IP-adress till den virtuella datorn, [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Därefter kan listas virtuella datorer på portalen.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Felsöka skyddade virtuella datorer som är nedtonat i portalen

Virtuella datorer som replikeras under Site Recovery är inte tillgängliga i Azure-portalen om det finns dubblettvärden i systemet. Om du vill lära dig mer om att ta bort inaktuella poster och lösa problemet, referera till [Azure Site Recovery VMware till Azure: Hur du rensar dubbletter eller inaktuella poster](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-solutions"></a>Vanliga fel och lösningar

### <a name="initial-replication-issues-error-78169"></a>Den inledande replikeringsproblem [fel 78169]

Över en ovan att säkerställa att det finns ingen anslutning, bandbredd eller tid synkronisera relaterad information, se till att:

- Inga ett antivirusprogram hindrar Azure Site Recovery. Lär dig [mer](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) på mappundantag som krävs för Azure Site Recovery.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>Saknade appkonsekventa återställningspunkter [fel 78144]

 Detta inträffar på grund av problem med Volume Shadow copy Service (VSS). För att lösa detta: 
 
- Kontrollera att den installerade versionen av Azure Site Recovery-agenten är minst 9.22.2. 
- Kontrollera att VSS-providern är installerad som en tjänst i Windows-tjänster och kontrollera komponenten Service MMC för att kontrollera att Azure Site Recovery VSS Provider visas också.
- Om VSS-leverantören inte är installerat finns det [installationsfel felsökningsartikeln](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Om VSS är inaktiverad
    - Kontrollera att starttypen för tjänsten VSS-Provider har angetts till **automatisk**.
    - Starta om följande tjänster:
        - VSS-tjänsten
        - Azure Site Recovery VSS Provider
        - VDS-tjänsten

### <a name="source-machines-with-high-churn-error-78188"></a>Källdatorer med hög omsättning [fel 78188]

Möjliga orsaker:
- Dataändringshastigheten (skrivna byte/sek) på de angivna diskarna för den virtuella datorn är mer än den [stöds gränser för Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) för kontot för replikeringsmållagring.
- Det finns en plötslig insamling i omsättningsfrekvensen på grund av vilka hög mängden data som är i vänteläge för överföring.

Att lösa problemet:
- Se till att target lagringskontotypen (Standard eller Premium) är etablerad enligt kravet på omsättning vid källan.
- Om den observerade omsättningen är tillfällig, Vänta några timmar innan data väntar överföringen komma ikapp och för att skapa återställningspunkter.
- Om problemet kvarstår kan du använda Site Recovery [Distributionshanteraren](site-recovery-deployment-planner.md#overview) för att planera replikering.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Källdatorer med inga pulsslag [fel 78174]

Detta händer när Azure Site Recovery-mobilitetsagenten på källdatorn inte kommunicerar med den konfigurationsserver (CS).

Använd följande steg för att lösa problemet genom för att verifiera nätverksanslutningen från den Virtuella källdatorn till Config-Server:

1. Kontrollera att källdatorn är igång.
2. Logga in på källdatorn med ett konto som har administratörsbehörighet.
3. Kontrollera att följande tjänster körs och om inte starta om tjänsterna:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application Service
4. På källdatorn, kontrollera loggarna på platsen för felinformation:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Processerver med inga pulsslag [fel 806]
Om det finns inget pulsslag från Process Server (PS), kontrollerar du att:
1. PS VM är igång
2. Kontrollera följande loggar på PS för felinformation:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Huvudmålservern med inga pulsslag [fel 78022]

Detta händer när Azure Site Recovery-mobilitetsagenten på Huvudmålet inte kommunicerar med konfigurationsservern.

Lös problemet genom att använda följande steg för att kontrollera status för tjänsten:

1. Kontrollera att Master Target Virtuella datorn körs.
2. Logga in på Master Target-VM med ett konto som har administratörsbehörighet.
    - Kontrollera att tjänsten svagents körs. Starta om tjänsten om den körs
    - Titta på loggfilerna på plats för felinformation:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log



## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du ställa din fråga i den [Azure Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Vi har en aktiv community och en av våra tekniker kan hjälpa dig.
