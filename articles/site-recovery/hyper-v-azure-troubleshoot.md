---
title: Felsökning av Hyper-V-replikering Azure med Azure Site Recovery | Microsoft Docs
description: Beskriver hur till felsökning av problem med Hyper-V till Azure med Azure Site Recovery-replikering
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 04/09/2018
ms.author: rayne
ms.openlocfilehash: 8b16f495a21ff0b3da415390877777650a9fbb82
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048211"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Felsöka Hyper-V till Azure-replikering och redundans

Den här artikeln beskrivs vanliga problem som kan uppstå när du replikera lokala virtuella Hyper-V-datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Aktivera skyddsproblem

Om du får problem när du aktiverar skydd för Hyper-V virtuella datorer måste du kontrollera följande:

1. Kontrollera att dina Hyper-V-värdar och virtuella datorer som är kompatibel med alla [krav och förutsättningar](hyper-v-azure-support-matrix.md).
2. Om Hyper-V-servrar finns i System Center Virtual Machine Manager (VMM)-moln, kontrollerar du att du har förberett den [VMM-servern](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Kontrollera att Hyper-V Virtual Machine Management-tjänsten körs på Hyper-V-värdar.
4. Sök efter problem som visas i Hyper-V-VMMS\Admin loggen på den virtuella datorn. Den här loggfilen finns i **program- och tjänstloggar** > **Microsoft** > **Windows**.
5. Kontrollera att WMI är aktiverat och tillgänglig på den Virtuella gästdatorn.
  - [Lär dig mer om](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) grundläggande WMI-tester.
  - [Felsöka](https://aka.ms/WMiTshooting) WMI.
  - [Felsöka ](https://technet.microsoft.com/library/ff406382.aspx#H22) problem med WMI-skript och tjänster.
5. Se till att den senaste versionen av Integration Services körs på Virtuella gästdatorn.
    - [Kontrollera](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) att du har den senaste versionen.
    - [Behåll](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Integration Services uppdaterade.
    
## <a name="replication-issues"></a>Replikeringsproblem

Felsöka problem med inledande och pågående replikering på följande sätt:

1. Kontrollera att du kör den [senaste versionen](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) av Site Recovery services.
2. Kontrollera om replikeringen har pausats:
  - Kontrollera VM hälsostatus i Hyper-V Manager-konsolen.
  - Om det är viktigt, högerklickar du på den virtuella datorn > **replikering** > **visa Replikeringshälsa**.
  - Om replikeringen har pausats, klickar du på **återuppta replikering**.
3. Kontrollera att de nödvändiga tjänsterna körs. Starta om dem om de inte.
    - Om du replikerar Hyper-V utan VMM, kontrollera att tjänsterna körs på Hyper-V-värd:
        - Virtual Machine Management-tjänsten
        - Tjänsten Microsoft Azure Recovery Services-agenten
        - Microsoft Azure Site Recovery-tjänsten
        - Tjänsten värd för WMI-Provider
    - Om du replikerar med VMM i miljön, måste du kontrollera att tjänsterna körs:
        - Kontrollera att Virtual Machine Management-tjänsten Microsoft Azure Recovery Services-agenten och tjänsten värd för WMI-providern körs på Hyper-V-värden.
        - Kontrollera att System Center Virtual Machine Manager-tjänsten körs på VMM-servern.
4. Kontrollera anslutningen mellan Hyper-V-servern och Azure. Det gör du genom att öppna Aktivitetshanteraren på Hyper-V-värden. På den **prestanda** klickar du på **öppna Resursövervakaren**. På den **nätverk** fliken > **Processess med nätverksaktivitet**, kontrollera om cbengine.exe aktivt skickar stora datamängder (MB).
5. Kontrollera om Hyper-V-värdar kan ansluta till URL: en för Azure storage blob. Välj om du vill göra det, och kontrollera **cbengine.exe**. Visa **TCP-anslutningar** att verifiera anslutning från värden till Azure storage-blob.
6. Kontrollera prestandaproblem, som beskrivs nedan.
    
### <a name="performance-issues"></a>Prestandaproblem

Begränsningar i nätverksbandbredd kan påverka replikering. Felsökning av problem på följande sätt:

1. [Kontrollera](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) om det finns bandbredd eller begränsning av begränsningarna i din miljö.
2. Kör den [distribution Planner profileraren](hyper-v-deployment-planner-run.md).
3. När du kör profileraren följer den [bandbredd](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) och [lagring](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) rekommendationer.
4. Kontrollera [data omsättningsuppdateringar begränsningar](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Om du ser hög data för att trycka på en virtuell dator kan du göra följande:
  - Kontrollera om den virtuella datorn har markerats för omsynkronisering.
  - Följ [här](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) att undersöka källan för dataomsättningen.
  - Omsättningen kan inträffa när HRL loggfiler överstiger 50% av det tillgängliga diskutrymmet. Om problemet är att etablera mer lagringsutrymme för alla virtuella datorer som du har problem.
  - Kontrollera att replikeringen inte är pausad. Om det är fortfarande skriva ändringar till hrl-fil, som kan bidra till den ökade storleken.
 

## <a name="critical-replication-state-issues"></a>Problem med kritisk replikering

1. Om du vill kontrollera replikeringshälsa ansluter till lokala Hyper-V Manager-konsolen, Välj den virtuella datorn och verifiera hälsotillstånd.

    ![Replikeringshälsa](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Klicka på **visa Replikeringshälsa** visas information:

    - Om replikeringen har pausats, högerklickar du på den virtuella datorn > **replikering** > **återuppta replikering**.
    - Om en virtuell dator på en Hyper-V-värd som har konfigurerats i Site Recovery migrerar till en annan Hyper-V-värd i samma kluster eller en fristående dator, påverkas replikering för den virtuella datorn inte. Bara kontrollera att den nya Hyper-V-värden uppfyller alla krav och är konfigurerad i Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Programkonsekventa ögonblicksbilder problem

En programkonsekventa ögonblicksbilder är en tidpunkt i ögonblicksbild av programdata inuti den virtuella datorn. Volume Shadow Copy Service (VSS) säkerställer att apparna på den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas.  Det här avsnittet beskrivs några vanliga problem som kan uppstå.

### <a name="vss-failing-inside-the-vm"></a>VSS misslyckas inuti den virtuella datorn

1. Kontrollera att den senaste versionen av Integration services är installerat och körs.  Kontrollera om en uppdatering är tillgänglig genom att köra följande kommando från en upphöjd PowerShell-Kommandotolken på Hyper-V-värd: **get-vm | Välj namn, status, IntegrationServicesState**.
2. Kontrollera att VSS-tjänsterna körs och är felfria:
    - Logga in på den Virtuella gästdatorn för att göra detta. Sedan öppna en kommandotolk för administratörer och kör följande kommandon för att kontrollera om alla VSS-skrivare har felfri.
        - **Vssadmin listan skrivare**
        - **Vssadmin förteckning skuggor**
        - **Vssadmin listan providers**
    - Kontrollera utdata. Om skrivare är i ett felaktigt tillstånd kan du göra följande:
        - Kontrollera programhändelseloggen på den virtuella datorn VSS-fel för åtgärden.
    - Försök att starta om tjänsterna som är associerade med misslyckade skrivaren:
        - Volume Shadow Copy
         - Azure Site Recovery-providern för VSS
    - När du gör detta Vänta några timmar att se om programkonsekventa ögonblicksbilder ska kunna genereras.
    - Som en sista utväg försök att starta om den virtuella datorn. Problemet kan lösas tjänster som har statusen svarar inte.
3. Kontrollera att du inte dynamiska diskar på den virtuella datorn. Detta stöds inte för programkonsekventa ögonblicksbilder. Du kan kontrollera i Diskhantering (diskmgmt.msc).

    ![Dynamisk disk](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Kontrollera att du inte har en iSCSI-disk som är kopplade till den virtuella datorn. Det stöds inte.
5. Kontrollera att tjänsten säkerhetskopiering är aktiverad. Kontrollera detta i **Hyper-V-inställningar** > **Integration Services**.
6. Kontrollera att det inte föreligger några konflikter med appar med VSS-ögonblicksbilder. Om flera appar försöker ta VSS-ögonblicksbilder på samma gång konflikter kan uppstå. Om exempelvis en säkerhetskopiering app tar VSS-ögonblicksbilder när Site Recovery har schemalagts för replikeringsprincipen att ta en ögonblicksbild.   
7. Kontrollera om den virtuella datorn upplever en hög omsättningen:
    - Du kan mäta dagliga förändringstakten för data för gäst-VM med hjälp av prestandaräknare på Hyper-V-värd. Gör detta genom att aktivera följande räknare. Aggregrate ett exempel på det här värdet på VM-diskarna för 5 – 15 minuter att hämta VM omsättning.
        - Kategori: ”Hyper-V virtuell lagringsenhet”
        - Räknaren ”: skriva byte / sek”</br>
        - Den här omsättningen data ökar eller kvar på en hög nivå, beroende på hur upptagen den virtuella datorn eller dess apps är.
        - Den genomsnittliga källa disk dataomsättningen är 2 MB/s för standardlagring för Site Recovery. [Läs mer](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Dessutom kan du [Kontrollera lagring skalbarhetsmål](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#scalability-targets-for-a-storage-account).
8. Kör den [distribution Planner](hyper-v-deployment-planner-run.md).
9. Granska rekommendationer för [nätverk](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) och [lagring](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS-fel i Hyper-V-värden

1. Kontrollera händelseloggarna för VSS-fel och rekommendationer:
    - Öppna loggen i Hyper-V-administratör på Hyper-V-värdservern **Loggboken** > **program- och tjänstloggar** > **Microsoft**  >  **Windows** > **Hyper-V** > **Admin**.
    - Kontrollera om det finns händelser som anger programkonsekventa ögonblicksbilder fel.
    - En typisk felet är ”: Det gick inte att generera en uppsättning av VSS-ögonblicksbilder för den virtuella datorn” XYZ ”Hyper-V: uppstod ett fel uppstod för skrivaren. Starta om tjänsten VSS kan lösa problem om tjänsten inte svarar ”.

2. Kontrollera att Hyper-V-integreringstjänsterna är installerade på den virtuella datorn och att säkerhetskopiering (VSS) Integration Service är aktiverat för att generera VSS-ögonblicksbilder för den virtuella datorn.
    - Kontrollera att VSS-tjänsterna Integration service/Daemon körs på gästen och är i ett **OK** tillstånd.
    - Du kan kontrollera detta från en upphöjd PowerShell-session på Hyper-V-värden med kommandot **et VMIntegrationService - VMName<VMName>-namnet VSS** du kan också få den här informationen genom att logga in på den Virtuella gästdatorn. [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Se till att säkerhetskopiering/VSS-integreringstjänsterna på den virtuella datorn körs och i felfritt tillstånd. Om det inte finns startar du om dessa tjänster och och Hyper-V Volume Shadow Copy-tjänsten för begärande på Hyper-V-värdservern.

### <a name="common-errors"></a>Vanliga fel

**Felkod** | **meddelande** | **Detaljer**
--- | --- | ---
**0x800700EA** | ”Det gick inte att generera en uppsättning av VSS-ögonblicksbilder för den virtuella datorn Hyper-V: flera data är tillgängliga. (0x800700EA). Ange ögonblicksbildgenerering VSS kan misslyckas om säkerhetskopiering pågår.<br/><br/> Replikeringsåtgärden för den virtuella datorn misslyckades: flera data är tillgängliga ”. | Kontrollera om den virtuella datorn har en dynamisk disk som är aktiverad. Det stöds inte.
**0x80070032** | ”Det gick inte att ansluta till virtuella Hyper-V Volume Shadow Copy begärande <. / VMname > eftersom versionen som inte matchar den version som förväntades av Hyper-V | Kontrollera om de senaste uppdateringarna är installerade.<br/><br/> [Uppgradera](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services.md#keep-integration-services-up-to-date) till den senaste versionen av Integration Services.



## <a name="collect-replication-logs"></a>Samla in replikeringsloggar

Alla Hyper-V-replikering händelse loggas i loggen för Hyper-V-VMMS\Admin, finns i **program- och tjänstloggar** > **Microsoft** > **Windows**. Dessutom kan kan du aktivera en analytiska logg för Hyper-V Virtual Machine Management-tjänsten på följande sätt:

1. Kontrollera de analytiska loggarna och felsökningsloggar kan visas i Loggboken. Om du vill göra detta, i Loggboken, klickar du på **visa** > **visa analytiska loggar och felsökningsloggar.**. Analytiska loggen visas under **Hyper-V-VMMS**.
2. I den **åtgärder** rutan klickar du på **aktivera loggning**. 

    ![Aktivera loggen](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. När den är aktiverad, visas den i **Prestandaövervakaren**, som en **spårningssessionen för händelser** under **datainsamlaruppsättningar**. 
4. Stoppa spårningssessionen genom att inaktivera loggen om du vill visa den insamlade informationen. Sedan spara loggen, och öppna den igen i Loggboken eller använda andra verktyg för att konvertera den efter behov.


### <a name="event-log-locations"></a>Händelseloggen platser

**Händelseloggen** | **Detaljer** |
--- | ---
**Program och tjänsten loggar/Microsoft/VirtualMachineManager/Server/Admin** (VMM-server) | Loggar för felsökning av problem med VMM.
**Program och -loggarna/MicrosoftAzureRecoveryServices/replikeringen** (Hyper-V-värd) | Loggar för felsökning av problem med Microsoft Azure Recovery Services-agenten. 
**Program och tjänsten loggar/Microsoft/Azure Site Recovery/providern/Operational** (Hyper-V-värd)| Loggar för felsökning av problem med Microsoft Azure Site Recovery-tjänsten.
**Program och tjänsten loggar/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V-värd) | Loggar för felsökning av problem med hantering av Hyper-V-dator.

### <a name="log-collection-for-advanced-troubleshooting"></a>Logginsamling för avancerad felsökning

Dessa verktyg kan hjälpa dig med avancerad felsökning:

-   VMM kan utföra i Site Recovery loggen samlingen använder den [Support Diagnostics plattformen (SDP) verktyget](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   För Hyper-V utan VMM [hämta verktyget](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab), och kör det på Hyper-V-värden för att samla in loggarna.

