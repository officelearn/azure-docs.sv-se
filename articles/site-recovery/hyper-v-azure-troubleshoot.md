---
title: Felsöka Hyper-V till Azure-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du felsöker disaster recovery problem med Hyper-V till Azure med Azure Site Recovery-replikering
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 94b2ab0263ccb7b6835a7bbe76ed8776aadb1a65
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228210"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Felsöka Hyper-V till Azure replikering och redundans

Den här artikeln beskrivs vanliga problem som du kan stöta på när du replikera lokala virtuella Hyper-V-datorer till Azure, med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Aktivera problem med att skydda

Om det uppstår problem när du aktiverar skydd för Hyper-V-datorer kan du kontrollera följande rekommendationer:

1. Kontrollera att dina Hyper-V-värdar och virtuella datorer uppfyller alla [krav och förutsättningar](hyper-v-azure-support-matrix.md).
2. Om Hyper-V-servrar finns i System Center Virtual Machine Manager (VMM)-moln, kontrollerar du att du har förberett den [VMM-servern](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Kontrollera att Hyper-V Virtual Machine Management-tjänsten körs på Hyper-V-värdar.
4. Sök efter problem som visas i Hyper-V-VMMS\Admin logga in på den virtuella datorn. Den här loggfilen finns i **applikationer och tjänsteloggar** > **Microsoft** > **Windows**.
5. På den Virtuella gästdatorn, kontrollerar du att WMI är aktiverat och kan nås.
  - [Lär dig mer om](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) grundläggande WMI-tester.
  - [Felsöka](https://aka.ms/WMiTshooting) WMI.
  - [Felsöka ](https://technet.microsoft.com/library/ff406382.aspx#H22) problem med WMI-skript och tjänster.
6. Se till att den senaste versionen av Integration Services körs på Virtuella gästdatorn.
    - [Kontrollera](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) att du har den senaste versionen.
    - [Behåll](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) integreringstjänster är uppdaterad.
    
## <a name="replication-issues"></a>Replikeringsproblem

Felsöka problem med initiala lösenord och fortlöpande replikering på följande sätt:

1. Kontrollera att du kör den [senaste versionen](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) av Site Recovery-tjänster.
2. Kontrollera om pausas replikeringen:
  - Kontrollera hälsostatus för virtuell dator i Hyper-V Manager-konsolen.
  - Om det är viktigt, högerklickar du på den virtuella datorn > **replikering** > **visa Replikeringshälsa**.
  - Om replikeringen har pausats, klickar du på **återuppta replikering**.
3. Kontrollera att de nödvändiga tjänsterna körs. Starta om dem om de inte är.
    - Om du replikerar virtuella Hyper-V utan VMM, kontrollera att de här tjänsterna körs på Hyper-V-värd:
        - Virtual Machine Management-tjänsten
        - Microsoft Azure Recovery Services Agent-tjänsten
        - Microsoft Azure Site Recovery-tjänsten
        - Tjänsten värd för WMI-Provider
    - Om du replikerar med VMM i miljön, kan du kontrollera att de här tjänsterna körs:
        - Kontrollera att Virtual Machine Management-tjänsten, Microsoft Azure Recovery Services-agenten och tjänsten WMI-providern körs på Hyper-V-värd.
        - Se till att System Center Virtual Machine Manager-tjänsten körs på VMM-servern.
4. Kontrollera anslutningen mellan Hyper-V-servern och Azure. Öppna Aktivitetshanteraren på Hyper-V-värden för att kontrollera anslutningen. På den **prestanda** fliken **öppna Resursövervakaren**. På den **nätverk** fliken > **processen med nätverksaktivitet**, kontrollera om cbengine.exe aktivt skickar stora datamängder (MB).
5. Kontrollera om Hyper-V-värdar kan ansluta till Azure storage blob URL: en. Kontrollera om värdarna kan ansluta, välja och kontrollera **cbengine.exe**. Visa **TCP-anslutningar** att verifiera anslutningarna från värden till Azure storage-blob.
6. Kontrollera prestandaproblem, enligt beskrivningen nedan.
    
### <a name="performance-issues"></a>Prestandaproblem

Begränsningar i nätverksbandbredd kan påverka replikering. Felsöka problem på följande sätt:

1. [Kontrollera](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) om det finns bandbredd eller begränsning av begränsningarna i din miljö.
2. Kör den [Distributionshanteraren profiler](hyper-v-deployment-planner-run.md).
3. När du kör profileraren, följer du de [bandbredd](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) och [storage](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) rekommendationer.
4. Kontrollera [dataomsättning begränsningar](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Om du ser hög dataomsättning på en virtuell dator kan du göra följande:
  - Kontrollera om din virtuella dator har markerats för omsynkronisering.
  - Följ [här](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) att undersöka orsaken omsättningen.
  - Omsättning kan inträffa när HRL-loggfilerna överskrider 50% av det tillgängliga diskutrymmet. Om detta är ett problem kan tillhandahålla mer lagringsutrymme för alla virtuella datorer där problemet uppstår.
  - Kontrollera att replikeringen inte är pausad. Om det är fortsätter det skriva ändringar till hrl-fil, som kan bidra till ökad storlek.
 

## <a name="critical-replication-state-issues"></a>Problem med kritisk replikering

1. Om du vill kontrollera replikeringshälsan, ansluta till en lokal Hyper-V Manager-konsolen, Välj den virtuella datorn och kontrollera hälsa.

    ![Replikeringshälsa](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Klicka på **visa Replikeringshälsa** kan se vilka:

    - Om replikeringen har pausats, högerklickar du på den virtuella datorn > **replikering** > **återuppta replikering**.
    - Om en virtuell dator på en Hyper-V-värd som konfigurerats i Site Recovery har migrerats till en annan Hyper-V-värd i samma kluster eller till en fristående virtuell dator, påverkas replikering för den virtuella datorn inte. Bara kontrollera att den nya Hyper-V-värden uppfyller alla krav och är konfigurerad i Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problem med programkonsekvent ögonblicksbild

En appkompatibel ögonblicksbild är en point-in-time-ögonblicksbild av programdata på den virtuella datorn. Volume Shadow Copy Service (VSS) säkerställer att program på den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas.  Det här avsnittet beskriver några vanliga problem som kan uppstå.

### <a name="vss-failing-inside-the-vm"></a>VSS misslyckas på den virtuella datorn

1. Kontrollera att den senaste versionen av Integration services är installerat och körs.  Kontrollera om en uppdatering är tillgänglig genom att köra följande kommando från en upphöjd PowerShell-kommandotolk på Hyper-V-värd: **get-vm | Välj namn, status, IntegrationServicesState**.
2. Kontrollera att VSS-tjänsterna är körs och är felfria:
    - Kontrollera tjänsten genom att logga in på den Virtuella gästdatorn. Sedan öppna Kommandotolken som administratör och kör följande kommandon för att kontrollera om alla VSS-skrivarna är felfri.
        - **Vssadmin list writers**
        - **Vssadmin list shadows**
        - **Vssadmin list-providers**
    - Kontrollera utdata. Om skrivarna är i ett felaktigt tillstånd kan du göra följande:
        - Kontrollera programmets händelselogg på den virtuella datorn finns VSS-fel för åtgärden.
    - Försök att starta om tjänsterna som är associerade med den misslyckade skrivaren:
        - Volume Shadow Copy
         - Azure Site Recovery VSS Provider
    - När du gör det Vänta ett par timmar att se om appkonsekventa ögonblicksbilder skapas har.
    - Som en sista utväg försök att starta om den virtuella datorn. Detta kan lösa tjänster som har statusen inte svarar.
3. Kontrollera att du inte har dynamiska diskar på den virtuella datorn. Detta stöds inte för appkonsekventa ögonblicksbilder. Du kan kontrollera i Diskhantering (diskmgmt.msc).

    ![Dynamisk disk](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Kontrollera att du inte har en iSCSI-disk som är ansluten till den virtuella datorn. Det stöds inte.
5. Kontrollera att Backup-tjänsten är aktiverad. Kontrollera att den är aktiverad i **Hyper-V-inställningar** > **Integration Services**.
6. Kontrollera att det finns några konflikter med appar med VSS-ögonblicksbilder. Om flera appar försöker ta VSS-ögonblicksbilder på samma gång konflikter kan uppstå. Om exempelvis en Backup-app tar VSS-ögonblicksbilder när Site Recovery med din replikeringsprincip har schemalagts för att ta en ögonblicksbild.   
7. Kontrollera om den virtuella datorn upplever en hög omsättning hastighet:
    - Du kan mäta dagliga förändringstakten för data för de virtuella gästdatorerna med prestandaräknare på Hyper-V-värd. Aktivera följande räknare för att mäta förändringstakten för data. Aggregera ett exempel på det här värdet för VM-diskar för 5 till 15 minuter för att få omsättning för virtuell dator.
        - Kategori: ”Hyper-V virtuell lagringsenhet”
        - Räknare: ”Skrivna byte / sek”</br>
        - Den här omsättningsfrekvensen data kommer att öka eller är kvar på en hög nivå, beroende på hur upptagna den virtuella datorn eller dess appar är.
        - Den genomsnittliga källa dataomsättningen för disken är 2 MB/s för standardlagring för Site Recovery. [Läs mer](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Dessutom kan du [Kontrollera storage skalbarhetsmål](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#scalability-targets-for-a-storage-account).
8. Kör den [Distributionshanteraren](hyper-v-deployment-planner-run.md).
9. Läs igenom rekommendationerna för [nätverk](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) och [storage](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS misslyckas i Hyper-V-värd

1. Kontrollera händelseloggarna för VSS-fel och rekommendationer:
    - Öppna loggen i Hyper-V-administratör på Hyper-V-värdservern **Loggboken** > **applikationer och tjänsteloggar** > **Microsoft**  >  **Windows** > **Hyper-V** > **Admin**.
    - Kontrollera om det finns några händelser som indikerar appkompatibel ögonblicksbild fel.
    - En typisk felet är: ”Det gick inte att skapa uppsättning av VSS-ögonblicksbilder för den virtuella datorn” XYZ ”Hyper-V: Det uppstod ett fel uppstod för skrivaren. Starta om VSS-tjänsten kan lösa problem om tjänsten inte svarar ”.

2. Kontrollera att Hyper-V-integreringstjänsterna är installerade på den virtuella datorn och att säkerhetskopiering (VSS) Integration-tjänsten är aktiverad för att generera VSS-ögonblicksbilder för den virtuella datorn.
    - Se till att Integration Services VSS-tjänsten/Daemon körs på gästen och finns i en **OK** tillstånd.
    - Du kan kontrollera detta från en upphöjd PowerShell-session på Hyper-V-värden med kommandot **et-VMIntegrationService - VMName<VMName>-namnet VSS** du kan också få den här informationen genom att logga in den Virtuella gästdatorn. [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Se till att säkerhetskopiering/VSS-integreringstjänsterna på den virtuella datorn körs och är i felfritt tillstånd. Annars kan du starta om tjänsterna och Hyper-V Volume Shadow Copy-tjänsten för begärande på Hyper-V-värdservern.

### <a name="common-errors"></a>Vanliga fel

**Felkod** | **meddelande** | **Detaljer**
--- | --- | ---
**0x800700EA** | ”Hyper-V kunde inte generera uppsättning av VSS-ögonblicksbilder för den virtuella datorn: Det finns mer information. (0x800700EA). Ställ in VSS ögonblicksbildgenerering kan misslyckas om säkerhetskopiering pågår.<br/><br/> Det gick inte att replikera virtuella datorer: Flera data är tillgängliga ”. | Kontrollera om den virtuella datorn har en dynamisk disk som är aktiverad. Det stöds inte.
**0x80070032** | ”Det gick inte att ansluta till det virtuella Hyper-V Volume Shadow Copy begärande <. / VMname > eftersom versionen inte matchar den version som förväntades av Hyper-V | Kontrollera om de senaste Windows-uppdateringarna är installerade.<br/><br/> [Uppgradera](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) till den senaste versionen av Integration Services.



## <a name="collect-replication-logs"></a>Samla in replikeringsloggar

Alla Hyper-V-replikering händelse loggas i Hyper-V-VMMS\Admin loggen, finns i **applikationer och tjänsteloggar** > **Microsoft** > **Windows**. Dessutom kan aktivera du en analytiska logg för Hyper-V Virtual Machine Management-tjänsten på följande sätt:

1. Kontrollera de analytiska loggarna och felsökningsloggar kan visas i Loggboken. Om du vill göra loggarna tillgängliga i Loggboken, klickar du på **visa** > **visa analytiska loggar och felsökningsloggar.**. Analytiska loggen visas under **Hyper-V-VMMS**.
2. I den **åtgärder** fönstret klickar du på **aktivera loggning**. 

    ![Aktivera log](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. När den är aktiverad, visas den i **Prestandaövervakaren**, som en **spårningssessionen för händelser** under **datainsamlaruppsättningar**. 
4. Stoppa spårningssessionen genom att inaktivera loggen om du vill visa Insamlad information. Sedan spara loggen, och öppna det igen i Loggboken eller använda andra verktyg för att konvertera den efter behov.


### <a name="event-log-locations"></a>Händelseloggen platser

**Händelseloggen** | **Detaljer** |
--- | ---
**Program och tjänsten loggar/Microsoft/VirtualMachineManager/Server/Admin** (VMM-server) | Loggar för att felsöka problem med VMM.
**Program och -loggarna/MicrosoftAzureRecoveryServices/replikeringen** (Hyper-V-värd) | Loggar för att felsöka problem med Microsoft Azure Recovery Services Agent. 
**Program och tjänsten loggar/Microsoft/Azure Site Recovery/providern/Operational** (Hyper-V-värd)| Loggar för att felsöka problem med Microsoft Azure Site Recovery-tjänsten.
**Program och tjänsten loggar/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V-värd) | Loggar för att felsöka problem med hantering av Hyper-V-dator.

### <a name="log-collection-for-advanced-troubleshooting"></a>Logginsamling för avancerad felsökning

Dessa verktyg kan hjälpa dig med avancerad felsökning:

-   VMM kan utföra i Site Recovery log en samling med hjälp av den [Support diagnostik plattformen (SDP) verktyget](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   För Hyper-V utan VMM, [hämta verktyget](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab), och kör den på Hyper-V-värden och samlar in loggarna.

