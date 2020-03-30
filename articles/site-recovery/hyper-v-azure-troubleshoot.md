---
title: Felsök Hyper-V-haveriberedskap med Azure Site Recovery
description: Beskriver felsÃ¶kning av problem med haveriberedskap med Hyper-V to Azure replication med Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: rajanaki
ms.openlocfilehash: 0a3e5c922009353e4ba9ccab12cf70ea2b5992da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961485"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Felsöka Hyper-V till Azure-replikering och redundans

I den här artikeln beskrivs vanliga problem som kan uppstå när du replikerar lokala virtuella hyper-virtuella datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Aktivera skyddsproblem

Om du får problem när du aktiverar skydd för virtuella datorer med hyper-v-datorer kontrollerar du följande rekommendationer:

1. Kontrollera att dina Hyper-V-värdar och virtuella datorer uppfyller alla [krav och förutsättningar](hyper-v-azure-support-matrix.md).
2. Om Hyper-V-servrar finns i VMM-moln (System Center Virtual Machine Manager) kontrollerar du att du har förberett [VMM-servern](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Kontrollera att hyper-V-tjänsten för hantering av virtuella datorer körs på Hyper-V-värdar.
4. Sök efter problem som visas i Hyper-V-VMMS\Admin-inloggningen till den virtuella datorn. Den här loggen finns i **Program- och tjänstloggar** > **Microsoft** > **Windows**.
5. Kontrollera att WMI är aktiverat och tillgängligt på gästdatorn.
   - [Läs mer om](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) grundläggande WMI-testning.
   - [Felsöka](https://aka.ms/WMiTshooting) Wmi.
   - [Felsöka](https://technet.microsoft.com/library/ff406382.aspx#H22) problem med WMI-skript och -tjänster.
6. På gästdatorn kontrollerar du att den senaste versionen av Integration Services körs.
    - [Kontrollera](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) att du har den senaste versionen.
    - [Behåll](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Integrationstjänster uppdaterade.
    
## <a name="replication-issues"></a>Replikeringsproblem

Felsök problem med inledande och fortlöpande replikering så här:

1. Kontrollera att du kör den [senaste versionen](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) av Site Recovery-tjänster.
2. Kontrollera om replikeringen är pausad:
   - Kontrollera hälsotillståndet för den virtuella datorn i Hyper-V Manager-konsolen.
   - Om det är kritiskt högerklickar du på den virtuella datorn > **replikeringsreplikeringshälsa** > **View Replication Health**.
   - Om replikeringen pausas klickar du på **Återuppta replikering**.
3. Kontrollera att nödvändiga tjänster körs. Om de inte är det, starta om dem.
    - Om du replikerar Hyper-V utan VMM kontrollerar du att dessa tjänster körs på Hyper-V-värden:
        - Tjänsten Virtual Machine Management
        - Tjänsten Microsoft Azure Recovery Services Agent
        - Tjänsten Microsoft Azure Site Recovery
        - Tjänsten WMI Provider Host
    - Om du replikerar med VMM i miljön kontrollerar du att dessa tjänster körs:
        - Kontrollera att tjänsten Hantering av virtuella datorer, Microsoft Azure Recovery Services Agent och WMI-providervärdtjänsten körs på Hyper-V-värden.
        - Kontrollera att Tjänsten System Center Virtual Machine Manager körs på VMM-servern.
4. Kontrollera anslutningen mellan Hyper-V-servern och Azure. Om du vill kontrollera anslutningen öppnar du Aktivitetshanteraren på Hyper V-värden. Klicka på Öppna **resursövervakare**på fliken **Prestanda** . Kontrollera om cbengine.exe aktivt skickar stora volymer (Mbs) data på > fliken **Nätverk** med **nätverksaktivitet.**
5. Kontrollera om Hyper-V-värdarna kan ansluta till Azure storage blob URL. Om du vill kontrollera om värdarna kan ansluta markerar du och kontrollerar **cbengine.exe**. Visa **TCP-anslutningar** för att verifiera anslutningen från värden till Azure storage blob.
6. Kontrollera prestandaproblem enligt beskrivningen nedan.
    
### <a name="performance-issues"></a>Prestandaproblem

Begränsningar av nätverksbandbredd kan påverka replikeringen. Felsöka problem enligt följande:

1. [Kontrollera](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) om det finns begränsningar för bandbredd eller begränsning i din miljö.
2. Kör profiler för [distributionsplanerare](hyper-v-deployment-planner-run.md).
3. När du har kört profileren följer du [bandbredds-](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) och [lagringsrekommendationerna.](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
4. Kontrollera [begränsningar för dataomsättning](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Om du ser hög dataomsättning på en virtuell dator gör du följande:
   - Kontrollera om den virtuella datorn är markerad för omsynkronisering.
   - Följ [dessa steg](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) för att undersöka källan till omsättningen.
   - Churn kan uppstå när HRL-loggfilerna överstiger 50 % av det tillgängliga diskutrymmet. Om detta är problemet, etablera mer lagringsutrymme för alla virtuella datorer där problemet uppstår.
   - Kontrollera att replikeringen inte är pausad. Om så är fallet fortsätter den att skriva ändringarna i hrl-filen, vilket kan bidra till dess ökade storlek.
 

## <a name="critical-replication-state-issues"></a>Problem med kritiskt replikeringstillstånd

1. Om du vill kontrollera replikeringshälsan ansluter du till den lokala Hyper-V Manager-konsolen, väljer den virtuella datorn och verifierar hälsotillståndet.

    ![Replikeringshälsa](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Klicka på **Visa replikeringshälsa** om du vill se mer information:

    - Om replikeringen pausas högerklickar du på den virtuella datorn >**Replikeringsreplikationen**för **replikering** > .
    - Om en virtuell dator på en Hyper-V-värd som konfigurerats i Site Recovery migreras till en annan Hyper-V-värd i samma kluster, eller till en fristående dator, påverkas inte replikering för den virtuella datorn. Kontrollera bara att den nya Hyper-V-värden uppfyller alla förutsättningar och är konfigurerad i Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problem med appkonsekvent ögonblicksbild

En appkonsekvent ögonblicksbild är en ögonblicksbild av programdata i den virtuella datorn. Vss (Volume Shadow Copy Service) säkerställer att appar på den virtuella datorn är i ett konsekvent tillstånd när ögonblicksbilden tas.  I det här avsnittet beskrivs några vanliga problem som kan uppstå.

### <a name="vss-failing-inside-the-vm"></a>VSS misslyckas inuti den virtuella datorn

1. Kontrollera att den senaste versionen av integrationstjänsterna är installerad och körs.  Kontrollera om en uppdatering är tillgänglig genom att köra följande kommando från en upphöjd PowerShell-prompt på Hyper-V-värden: **get-vm | välj Namn, Tillstånd, IntegrationServicesState**.
2. Kontrollera att VSS-tjänster körs och är felfria:
   - Om du vill kontrollera tjänsterna loggar du in på gästdatorn. Öppna sedan en kommandotolk för administratörer och kör följande kommandon för att kontrollera om alla VSS-författare är felfria.
       - **Vssadmin lista författare**
       - **Vssadmin lista skuggor**
       - **Vssadmin lista leverantörer**
   - Kontrollera utgången. Om författare är i ett misslyckat tillstånd gör du följande:
       - Kontrollera programhändelseloggen på den virtuella datorn för VSS-åtgärdsfel.
   - Prova att starta om dessa tjänster som är associerade med den misslyckade skrivaren:
     - Kopiera volymskugga
       - VSS-provider för Azure Site Recovery
   - När du har gjort detta väntar du i ett par timmar för att se om appkonsekventa ögonblicksbilder har genererats.
   - Som en sista utväg försöka starta om den virtuella datorn. Detta kan lösa tjänster som inte svarar.
3. Kontrollera att du inte har dynamiska diskar i den virtuella datorn. Det här stöds inte för appkonsekventa ögonblicksbilder. Du kan checka in Diskhantering (diskmgmt.msc).

    ![Dynamisk disk](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Kontrollera att du inte har en iSCSI-disk kopplad till den virtuella datorn. Det stöds inte.
5. Kontrollera att säkerhetskopieringstjänsten är aktiverad. Kontrollera att den är aktiverad i **Hyper-V-inställningar** > **Integration Services**.
6. Kontrollera att det inte finns några konflikter med appar som tar VSS-ögonblicksbilder. Om flera appar försöker ta VSS-ögonblicksbilder samtidigt som konflikter kan uppstå. Om till exempel en säkerhetskopieringsapp tar VSS-ögonblicksbilder när site recovery schemaläggs av replikeringsprincipen för att ta en ögonblicksbild.   
7. Kontrollera om den virtuella datorn har en hög omsättningshastighet:
    - Du kan mäta den dagliga dataändringshastigheten för gästdatalsmaskiner med hjälp av prestandaräknare på Hyper-V-värden. Om du vill mäta dataändringshastigheten aktiverar du följande räknare. Samla ett exempel på det här värdet över VM-diskarna i 5-15 minuter för att få den virtuella datorns omsättning.
        - Kategori: "Hyper-V virtuell lagringsenhet"
        - Räknare: "Skriv byte / sek"</br>
        - Den här dataomsättningshastigheten ökar eller ligger kvar på en hög nivå, beroende på hur upptagen den virtuella datorn eller dess appar är.
        - Den genomsnittliga källdiskdataomsättningen är 2 MB/s för standardlagring för webbplatsåterställning. [Läs mer](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Dessutom kan du [verifiera lagringskalbarhetsmål](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).
8. Kör [distributionsplaneraren](hyper-v-deployment-planner-run.md).
9. Läs rekommendationerna för [nätverk](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) och [lagring](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS misslyckas inuti Hyper-V-värden

1. Kontrollera om det finns VSS-fel och rekommendationer i händelseloggarna:
    - Öppna händelseloggen Hyper-V Admin i **Loggboken** > **program och tjänster loggar** > **Microsoft** > **Windows** > Hyper-V Admin på**Hyper-V-värdservern** > **Admin**.
    - Kontrollera om det finns några händelser som indikerar appkonsekventa ögonblicksbildfel.
    - Ett typiskt fel är: "Hyper-V misslyckades med att generera VSS-ögonblicksbilduppsättning för den virtuella datorn 'XYZ': Författaren upplevde ett icke-övergående fel. Om du startar om VSS-tjänsten kan problem lösas om tjänsten inte svarar."

2. Om du vill generera VSS-ögonblicksbilder för den virtuella datorn kontrollerar du att Hyper-V-integrationstjänster är installerade på den virtuella datorn och att tjänsten för integrering av säkerhetskopiering (VSS) är aktiverad.
    - Kontrollera att Tjänsten Integration Services VSS/daemons körs på gästen och är i **ok-tillstånd.**
    - Du kan kontrollera detta från en upphöjd PowerShell-session på Hyper-V-värden med kommandot **Get-VMIntegrationService -VMName\<VMName>-Name VSS** Du kan också hämta den här informationen genom att logga in på gästdatorn. [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Kontrollera att säkerhetskopierings-/VSS-integrationstjänsterna på den virtuella datorn körs och är i felfritt tillstånd. Om inte, starta om dessa tjänster och Hyper-V Volume Shadow Copy-requestor-tjänsten på Hyper-V-värdservern.

### <a name="common-errors"></a>Vanliga fel

**Felkod** | **Meddelande** | **Detaljer**
--- | --- | ---
**0x800700EA** | "Hyper-V kunde inte generera VSS-ögonblicksbilduppsättning för virtuell dator: Mer data är tillgängliga. (0x800700EA). VSS ögonblicksbild uppsättning generation kan misslyckas om säkerhetskopiering pågår.<br/><br/> Replikeringsåtgärden för virtuell dator misslyckades: Det finns mer data tillgängligt." | Kontrollera om den virtuella datorn har aktiverat dynamisk disk. Det stöds inte.
**0x80070032** | "Hyper-V Volume Shadow Copy Requestor misslyckades med att ansluta till virtuell dator </VMname> eftersom versionen inte matchar den version som förväntas av Hyper-V | Kontrollera om de senaste Windows-uppdateringarna är installerade.<br/><br/> [Uppgradera](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) till den senaste versionen av Integration Services.



## <a name="collect-replication-logs"></a>Samla in replikeringsloggar

Alla Hyper-V-replikeringshändelse loggas i Hyper-V-VMMS\Admin-loggen, som finns i **Program och tjänster loggar** > **Microsoft** > **Windows**. Dessutom kan du aktivera en analytisk logg för Hyper-V Virtual Machine Management Service enligt följande:

1. Gör de analytiska och felsökningsloggarna synliga i Loggboken. Om du vill göra loggarna tillgängliga klickar du på **Visa** > **analytiska och felsökningsloggar**i Loggboken. Den analytiska loggen visas under **Hyper-V-VMMS**.
2. Klicka på Aktivera **logg**i **åtgärdsfönstret** . 

    ![Aktivera logg](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. När den har aktiverats visas den i **Prestandaövervakaren**, som en **händelsespårningssession** under **datainsamlaruppsättningar**. 
4. Om du vill visa den insamlade informationen stoppar du spårningssessionen genom att inaktivera loggen. Spara sedan loggen och öppna den igen i Loggboken, eller använd andra verktyg för att konvertera den efter behov.


### <a name="event-log-locations"></a>Platser för händelselogg

**Händelseloggen** | **Detaljer** |
--- | ---
**Program- och tjänstloggar/Microsoft/VirtualMachineManager/Server/Admin** (VMM-server) | Loggar för att felsöka VMM-problem.
**Program och tjänstloggar/MicrosoftAzureRecoveryServices/Replication** (Hyper-V-värd) | Loggar för felsökning av Problem med Microsoft Azure Recovery Services Agent. 
**Program- och tjänstloggar/Microsoft/Azure Site Recovery/Provider/Operational** (Hyper-V-värd)| Loggar för felsökning av problem med Microsoft Azure Site Recovery Service.
**Program- och tjänstloggar/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V-värd) | Loggar för felsökning av hanteringsproblem för virtuella datorer med virtuella datorer.

### <a name="log-collection-for-advanced-troubleshooting"></a>Loggsamling för avancerad felsökning

De här verktygen kan hjälpa dig med avancerad felsökning:

-   För VMM utför du loggsamling för platsåterställning med [verktyget Support Diagnostics Platform (SDP).](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)
-   För Hyper-V utan VMM [hämtar du det här verktyget](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)och kör det på Hyper-V-värden för att samla in loggarna.

