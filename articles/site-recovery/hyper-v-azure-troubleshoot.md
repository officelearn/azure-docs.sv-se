---
title: Felsök haveri beredskap för Hyper-V med Azure Site Recovery
description: Beskriver hur du felsöker katastrof återställnings problem med Hyper-V till Azure-replikering med hjälp av Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: rajanaki
ms.openlocfilehash: 0a3e5c922009353e4ba9ccab12cf70ea2b5992da
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961485"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Felsöka Hyper-V till Azure-replikering och redundans

I den här artikeln beskrivs vanliga problem som kan uppstå i samband med replikeringen av lokala virtuella Hyper-V-datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Aktivera skydds problem

Om du får problem när du aktiverar skydd för virtuella Hyper-V-datorer kontrollerar du följande rekommendationer:

1. Kontrol lera att Hyper-V-värdar och virtuella datorer uppfyller alla [krav och krav](hyper-v-azure-support-matrix.md).
2. Om Hyper-V-servrar finns i System Center Virtual Machine Manager-moln (VMM) kontrollerar du att du har för berett [VMM-servern](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional).
3. Kontrol lera att tjänsten Hyper-V Virtual Machine Management körs på Hyper-V-värdar.
4. Sök efter problem som visas i Hyper-V-VMMS\Admin logga in på den virtuella datorn. Den här loggen finns i **program-och tjänst loggar** > **Microsoft** > **Windows**.
5. På den virtuella gäst datorn kontrollerar du att WMI är aktiverat och tillgängligt.
   - [Lär dig mer om](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) grundläggande WMI-testning.
   - [Felsöka](https://aka.ms/WMiTshooting) Tjänst.
   - [Felsök](https://technet.microsoft.com/library/ff406382.aspx#H22) problem med WMI-skript och-tjänster.
6. Se till att den senaste versionen av Integration Services körs på den virtuella gäst datorn.
    - [Kontrol lera](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) att du har den senaste versionen.
    - [Behåll](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Integrerings tjänsterna är aktuella.
    
## <a name="replication-issues"></a>Replikeringsproblem

Felsök problem med inledande och fortlöpande replikering så här:

1. Kontrol lera att du kör den [senaste versionen](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) av Site Recovery Services.
2. Verifiera om replikeringen har pausats:
   - Kontrol lera hälso tillståndet för den virtuella datorn i Hyper-V Manager-konsolen.
   - Om det är kritiskt, högerklickar du på den virtuella datorn > **replikering** > **Visa replikeringsstatus**.
   - Om replikeringen har pausats klickar du på **återuppta replikering**.
3. Kontrol lera att de nödvändiga tjänsterna körs. Om de inte gör det startar du om dem.
    - Om du replikerar Hyper-V utan VMM kontrollerar du att dessa tjänster körs på Hyper-V-värden:
        - Tjänsten Virtual Machine Management
        - Tjänsten Microsoft Azure Recovery Services Agent
        - Tjänsten Microsoft Azure Site Recovery
        - Tjänsten WMI Provider Host
    - Om du replikerar med VMM i miljön kontrollerar du att dessa tjänster körs:
        - På Hyper-V-värden kontrollerar du att tjänsten hantering av virtuell dator, Microsoft Azure Recovery Services agenten och värd tjänsten för WMI-providern körs.
        - Kontrol lera att tjänsten System Center Virtual Machine Manager körs på VMM-servern.
4. Kontrollera anslutningen mellan Hyper-V-servern och Azure. Öppna aktivitets hanteraren på Hyper V-värden för att kontrol lera anslutningen. Klicka på **öppna resursövervakare**på fliken **prestanda** . På fliken **nätverk** > **process med nätverks aktivitet**kontrollerar du om cbengine. exe aktivt skickar stora volymer (MB) data.
5. Kontrol lera om Hyper-V-värdarna kan ansluta till BLOB-URL: en för Azure Storage. Om du vill kontrol lera om värdarna kan ansluta väljer du och kontrollerar **cbengine. exe**. Visa **TCP-anslutningar** för att verifiera anslutningen från värden till Azure Storage-blobben.
6. Kontrol lera prestanda problemen enligt beskrivningen nedan.
    
### <a name="performance-issues"></a>Prestandaproblem

Begränsningar för nätverks bandbredd kan påverka replikeringen. Felsök problem på följande sätt:

1. [Kontrol lera](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) om det finns bandbredds-eller begränsnings begränsningar i din miljö.
2. Kör [Deployment Planner profiler](hyper-v-deployment-planner-run.md).
3. När du har kört profileraren följer du rekommendationerna för [bandbredd](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) och [lagring](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation) .
4. Kontrol lera [begränsningar för data omsättning](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Om du ser hög data omsättning på en virtuell dator gör du följande:
   - Kontrol lera om den virtuella datorn har marker ATS för omsynkronisering.
   - Följ [dessa steg](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) om du vill undersöka Omsättningens källa.
   - Omsättningen kan inträffa när HRL-loggfilerna överskrider 50% av det tillgängliga disk utrymmet. Om detta är problemet etablerar du mer lagrings utrymme för alla virtuella datorer där problemet uppstår.
   - Kontrol lera att replikeringen inte är pausad. Om så är fallet fortsätter det att skriva ändringarna till HRL-filen, vilket kan bidra till dess ökade storlek.
 

## <a name="critical-replication-state-issues"></a>Problem med kritisk replikeringstillstånd

1. Om du vill kontrol lera replikeringstillståndet ansluter du till den lokala Hyper-V Manager-konsolen, väljer den virtuella datorn och kontrollerar hälsa.

    ![Hälso tillstånd för replikering](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Klicka på **Visa replikeringsstatus** för att se information:

    - Om replikeringen har pausats högerklickar du på den virtuella datorn > **replikering** > **återuppta replikering**.
    - Om en virtuell dator på en Hyper-V-värd som kon figurer ATS i Site Recovery migreras till en annan Hyper-V-värd i samma kluster, eller till en fristående dator, påverkas inte replikeringen för den virtuella datorn. Kontrol lera bara att den nya Hyper-V-värden uppfyller alla krav och att den är konfigurerad i Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problem med programkonsekventa ögonblicks bilder

En programkonsekvent ögonblicks bild är en tidpunkts ögonblicks bild av program data i den virtuella datorn. Tjänsten Volume Shadow Copy (VSS) säkerställer att appar på den virtuella datorn är i ett konsekvent tillstånd när ögonblicks bilden tas.  I det här avsnittet beskrivs några vanliga problem som kan uppstå.

### <a name="vss-failing-inside-the-vm"></a>VSS-återställning i den virtuella datorn

1. Kontrol lera att den senaste versionen av Integration Services är installerad och körs.  Kontrol lera om en uppdatering är tillgänglig genom att köra följande kommando från en upphöjd PowerShell-kommandotolk på Hyper-V-värden: **Get-VM | Välj namn, tillstånd, IntegrationServicesState**.
2. Kontrol lera att VSS-tjänsterna körs och är felfria:
   - Logga in på den virtuella gäst datorn för att kontrol lera tjänsterna. Öppna sedan en administratörs kommando tolk och kör följande kommandon för att kontrol lera om alla VSS-skrivare är felfria.
       - **Vssadmin list Writers**
       - **Vssadmin list Shadows**
       - **Vssadmin list-leverantörer**
   - Kontrol lera utdata. Gör följande om skrivare är i ett felaktigt tillstånd:
       - Kontrol lera program händelse loggen på den virtuella datorn för VSS-åtgärds fel.
   - Försök att starta om de här tjänsterna som är kopplade till den misslyckade skrivaren:
     - Skugg kopia av volym
       - Azure Site Recovery VSS Provider
   - När du har gjort det väntar du några timmar för att se om programkonsekventa ögonblicks bilder genereras korrekt.
   - Som en sista utväg försöker du starta om den virtuella datorn. Detta kan lösa tjänster som är i ett tillstånd som inte svarar.
3. Kontrol lera att du inte har dynamiska diskar på den virtuella datorn. Detta stöds inte för programkonsekventa ögonblicks bilder. Du kan checka in disk hantering (diskmgmt. msc).

    ![Dynamisk disk](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Kontrol lera att du inte har någon iSCSI-disk ansluten till den virtuella datorn. Det stöds inte.
5. Kontrol lera att säkerhets kopierings tjänsten är aktive rad. Kontrol lera att den är aktive rad i **Hyper-V-inställningar** > **integrerings tjänster**.
6. Se till att det inte finns några konflikter med appar som tar VSS-ögonblicksbilder. Om flera appar försöker att ta VSS-ögonblicksbilder på samma tidpunkt kan konflikter uppstå. Om en säkerhets kopia till exempel tar ögonblicks bilder av VSS när Site Recovery schemaläggs enligt din replikeringsprincip för att ta en ögonblicks bild.   
7. Kontrol lera om den virtuella datorn har en hög omsättnings taxa:
    - Du kan mäta den dagliga data ändrings hastigheten för de virtuella gäst datorerna med hjälp av prestanda räknare på Hyper-V-värden. Aktivera följande räknare för att mäta data ändrings takten. Samla ett exempel på det här värdet mellan de virtuella dator diskarna i 5-15 minuter för att hämta VM-omsättningen.
        - Kategori: "Hyper-V virtuell lagrings enhet"
        - Räknare: "Skriv byte/s"</br>
        - Den här data omsättningen ökar eller fortsätter att vara på hög nivå, beroende på hur upptagen den virtuella datorn eller dess appar är.
        - Den genomsnittliga data omsättningen för käll disken är 2 MB/s för standard lagring för Site Recovery. [Läs mer](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Dessutom kan du [Verifiera lagrings skalbarhets mål](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).
8. Kör [distributions planeraren](hyper-v-deployment-planner-run.md).
9. Läs igenom rekommendationerna för [nätverk](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) och [lagring](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS-återställning i Hyper-V-värden

1. Kontrol lera händelse loggarna för VSS-fel och rekommendationer:
    - På Hyper-V-värdservern öppnar du Hyper-V-administratörens händelse logg i **Loggboken** > **program-och tjänst loggar** > **Microsoft** > **Windows** > **Hyper-v** > **admin**.
    - Kontrol lera om det finns några händelser som indikerar problem med programkonsekventa ögonblicks bilder.
    - Ett vanligt fel är: "Hyper-V kunde inte skapa VSS-ögonblicksbilden för den virtuella datorn" XYZ ": skrivaren fick ett icke-tillfälligt fel. Att starta om VSS-tjänsten kan lösa problem om tjänsten inte svarar. "

2. Om du vill skapa VSS-ögonblicksbilder för den virtuella datorn kontrollerar du att Hyper-V-integrerings tjänsterna är installerade på den virtuella datorn och att integrerings tjänsten för säkerhets kopiering (VSS) är aktive rad.
    - Se till att VSS-tjänsten/daemon för integrerings tjänsterna körs på gästen och är i ett **OK** -tillstånd.
    - Du kan kontrol lera detta från en upphöjd PowerShell-session på Hyper-V-värden med kommandot **Get-VMIntegrationService-VMName\<VMName >-Name VSS** du kan också få den här informationen genom att logga in på den virtuella gäst datorn. [Läs mer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Se till att tjänsterna för säkerhets kopiering/VSS-integrering på den virtuella datorn körs och är i felfritt tillstånd. Om inte, startar du om tjänsterna och tjänsten Hyper-V Volume Shadow Copy reservering på Hyper-V-värdservern.

### <a name="common-errors"></a>Vanliga fel

**Felkod** | **Meddelande** | **Detaljer**
--- | --- | ---
**0x800700EA** | "Hyper-V kunde inte skapa en ögonblicks bild uppsättning för VSS för virtuell dator: mer data är tillgängliga. (0x800700EA). Generering av ögonblicks bild uppsättning för VSS kan inte utföras om säkerhets kopiering pågår.<br/><br/> Det gick inte att utföra replikering för den virtuella datorn: fler data är tillgängliga. | Kontrol lera om den virtuella datorn har dynamisk disk aktive rad. Det stöds inte.
**0x80070032** | "Det gick inte att ansluta till den virtuella datorn <./VMname > eftersom versionen inte matchar den version som förväntas av Hyper-v-providern | Kontrol lera om de senaste Windows-uppdateringarna är installerade.<br/><br/> [Uppgradera](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) till den senaste versionen av Integration Services.



## <a name="collect-replication-logs"></a>Samla in loggar för replikering

All Hyper-V-replikering loggas i Hyper-V-VMMS\Admin-loggen, som finns i **program-och tjänst loggar** > **Microsoft** > **Windows**. Dessutom kan du aktivera en analytisk logg för tjänsten hantering av virtuell dator för Hyper-V enligt följande:

1. Gör de analytiska och fel söknings loggar som visas i Loggboken. Om du vill göra loggarna tillgängliga klickar du på **visa** > **Visa analytiska loggar och fel söknings loggar**i Loggboken. Analys loggen visas under **Hyper-V-VMMS**.
2. I fönstret **åtgärder** klickar du på **Aktivera logg**. 

    ![Aktivera logg](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. När den är aktive rad visas den i **prestanda övervakaren**, som en **Event Trace-Session** under **Datainsamlaruppsättningar**. 
4. Om du vill visa insamlad information stoppar du spårningssessionen genom att inaktivera loggen. Spara sedan loggen och öppna den igen i Loggboken, eller Använd andra verktyg för att konvertera den efter behov.


### <a name="event-log-locations"></a>Händelse logg platser

**Händelse logg** | **Detaljer** |
--- | ---
**Program-och tjänst loggar/Microsoft/VirtualMachineManager/server/admin** (VMM-Server) | Loggar för att felsöka VMM-problem.
**Program-och tjänst loggar/MicrosoftAzureRecoveryServices/replikering** (Hyper-V-värd) | Loggar för att felsöka problem med Microsoft Azure Recovery Services agent. 
**Program-och tjänst loggar/Microsoft/Azure Site Recovery/Provider/Operational** (Hyper-V-värd)| Loggar för att felsöka problem med Microsoft Azure Site Recovery-tjänsten.
**Program-och tjänst loggar/Microsoft/Windows/Hyper-V-VMMS/admin** (Hyper-v-värd) | Loggar för att felsöka problem med hantering av virtuella Hyper-V-datorer.

### <a name="log-collection-for-advanced-troubleshooting"></a>Logg insamling för avancerad fel sökning

Dessa verktyg kan vara till hjälp vid avancerad fel sökning:

-   För VMM utför du Site Recovery logg insamling med hjälp av [SDP-verktyget (Support Diagnostics Platform)](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   För Hyper-V utan VMM [laddar du ned det här verktyget](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)och kör det på Hyper-v-värden för att samla in loggarna.

