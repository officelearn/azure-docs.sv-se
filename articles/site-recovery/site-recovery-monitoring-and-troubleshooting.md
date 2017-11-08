---
title: "Övervaka och felsöka skydd för virtuella datorer och fysiska servrar | Microsoft Docs"
description: "Azure Site Recovery samordnar replikering, redundans och återställning av virtuella datorer på lokala servrar till Azure eller ett sekundärt datacenter. Använd den här artikeln för att övervaka och felsöka skydd för Hyper-V eller Virtual Machine Manager-plats."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 0fc8e368-0c0e-4bb1-9d50-cffd5ad0853f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: rajanaki
ms.openlocfilehash: ad1e0bcb2e2c073c8fb186f5a9d8bcb0bac588a0
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Övervaka och felsöka skydd för virtuella datorer och fysiska servrar
Den här övervakning och felsökning hjälper dig att lära dig hur du spårar replikeringshälsa och felsöker tekniker för Azure Site Recovery.

## <a name="understand-the-components"></a>Förstå komponenterna
### <a name="vmware-virtual-machine-or-physical-server-site-deployment-for-replication-between-on-premises-and-azure"></a>Virtuell VMware-dator eller fysisk server webbplatsdistribution för replikering mellan lokala och Azure
Om du vill konfigurera databasåterställning mellan en lokal virtuell VMware-dator eller fysisk server och Azure måste du konfigurera konfigurationsservern, huvudmålservern och processen server-komponenter på den virtuella datorn eller servern. När du aktiverar skyddet för källservern installerar Azure Site Recovery Mobilitetstjänsten från valda processervern om en uppdaterad version inte redan har distribuerats. Efter ett avbrott för lokala och käll-växlar servern över till Azure, kunder behöver du ställer in en processerver i Azure och en huvudmålserver lokalt att återskapa källservern lokalt.

![VMware/fysisk plats distributionen för replikering mellan lokala och Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-sites"></a>Virtual Machine Manager webbplatsdistribution för replikering mellan lokala platser
Om du vill konfigurera databasåterställning mellan två lokala platser måste du ladda ned Azure Site Recovery-providern och installera den på Virtual Machine Manager-servern. Providern måste Internet-anslutning så att alla åtgärder som utlöses från Azure portal hämta översättas till lokala åtgärder.

![Virtual Machine Manager webbplatsdistribution för replikering mellan lokala platser](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>Virtual Machine Manager webbplatsdistribution för replikering mellan lokala platser och Azure
När du ställer in databasåterställning mellan lokala platser och Azure måste du ladda ned Azure Site Recovery-providern och installera den på Virtual Machine Manager-servern. Du måste också installera Azure Recovery Services-agenten, som måste installeras på varje Hyper-V-värd. [Lär dig mer](site-recovery-hyper-v-azure-architecture.md) för mer information.

![Virtual Machine Manager webbplatsdistribution för replikering mellan lokala platser och Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>Distribution av Hyper-V-platsen för replikering mellan lokala platser och Azure
Den här processen liknar Virtual Machine Manager-distribution. Den enda skillnaden är att Azure Site Recovery-providern och Azure Recovery Services-agenten installeras på själva Hyper-V-värden. [Läs mer](site-recovery-hyper-v-azure-architecture.md). .

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Övervaka åtgärder för konfiguration, skydd och återställning
Varje åtgärd i Azure Site Recovery granskas och följs den **jobb** fliken. För att konfigurationen, skydd och Återställningsfel, gå till den **jobb** fliken och Sök efter fel.

![Misslyckades-filter på fliken jobb](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Om du hittar fel under den **jobb** fliken klickar du på projektet och klicka på **FELINFORMATION** för jobbet.

![Knappen FELINFORMATION](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Felinformationen hjälper dig att identifiera en möjlig orsak och rekommendation för problemet.

![Dialogruta som visar information om fel för ett specifikt jobb](media/site-recovery-monitoring-and-troubleshooting/image5.png)

I exemplet ovan verkar en annan åtgärd pågår orsaka skyddskonfigurationen misslyckas. Lös problemet enligt rekommendationerna och klicka sedan på **starta om** att initiera igen.

![Knappen omstart i på fliken jobb](media/site-recovery-monitoring-and-troubleshooting/image6.png)

Den **starta om** alternativet är inte tillgängligt för alla åtgärder. Om en åtgärd som inte har den **starta om** alternativ, gå tillbaka till objektet och gör om åtgärden igen. Du kan avbryta jobb som pågår med hjälp av den **Avbryt** knappen.

![Knappen Avbryt](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machines"></a>Övervaka hälsotillstånd för replikering för virtuella datorer
Du kan använda Azure portal fjärrövervaka Azure Site Recovery-providrar för var och en av de skydda enheterna. Klicka på **skyddade objekt**, och klicka sedan på **VMM-MOLN** eller **SKYDDSGRUPPER**. Den **VMM-MOLN** fliken är endast tillgängligt för distributioner som är baserade på Virtual Machine Manager. För andra scenarier är skyddade enheter under den **SKYDDSGRUPPER** fliken.

![Alternativ för VMM-moln och SKYDDSGRUPPER](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Klicka på en skyddad entitet under respektive moln eller skyddsgrupp för att se alla tillgängliga åtgärder visas längst ned i fönstret.

![Tillgängliga alternativ för en skyddad entitet](media/site-recovery-monitoring-and-troubleshooting/image9.png)

I föregående skärmbild visas hälsotillståndet för virtuell dator är **kritisk**. Du kan klicka på den **FELINFORMATION** knappen längst ned att se felet. Baserat på de **möjliga orsaker** och **rekommendation**, Lös problemet.

![Knappen felinformation](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Fel och rekommendationerna i dialogrutan information om fel](media/site-recovery-monitoring-and-troubleshooting/image11.png)

> [!NOTE]
> Om alla aktiva åtgärder pågår eller inte, gå till den **jobb** Visa som tidigare nämnts felet för ett visst jobb.
>
>

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Felsökning av problem med lokala Hyper-V
Ansluta till lokal Hyper-V manager-konsolen, Välj den virtuella datorn och se hälsotillståndet för replikering.

![Alternativ för att visa replikeringshälsa i Hyper-V manager-konsolen](media/site-recovery-monitoring-and-troubleshooting/image12.png)

I det här fallet **Replikeringshälsa** är **kritisk**. Högerklicka på den virtuella datorn och klicka sedan på **replikering** > **visa Replikeringshälsa** att se detaljerna.

![Hälsotillstånd för replikering för en specifik virtuell dator](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Om replikeringen pausades för den virtuella datorn, högerklicka på den virtuella datorn och klicka sedan på **replikering** > **återuppta replikering**.

![Alternativet att återuppta replikeringen i Hyper-V manager-konsolen](media/site-recovery-monitoring-and-troubleshooting/image19.png)

Om en virtuell dator migreras en ny Hyper-V-värd i klustret eller en fristående dator och Hyper-V-värden har konfigurerats i Azure Site Recovery påverkas replikering för den virtuella datorn inte. Kontrollera att den nya Hyper-V-värden uppfyller alla krav och konfigureras med hjälp av Azure Site Recovery.

### <a name="event-log"></a>Händelseloggen
| Händelsekällan | Information |
| --- |:--- |
| **Program och tjänsten loggar/Microsoft/VirtualMachineManager/Server/Admin** (Virtual Machine Manager-server) |Ger användbar loggning för att felsöka problem med många olika Virtual Machine Manager. |
| **Program och -loggarna/MicrosoftAzureRecoveryServices/replikeringen** (Hyper-V-värd) |Ger användbar loggning för att felsöka problem med många Microsoft Azure Recovery Services-agenten. <br/> ![Platsen för replikering händelsekällan för Hyper-V-värd](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Program och tjänsten loggar/Microsoft/Azure Site Recovery/providern/Operational** (Hyper-V-värd) |Ger användbar loggning för att felsöka problem med många Microsoft Azure Site Recovery-tjänsten. <br/> ![Platsen för operativa händelsekällan för Hyper-V-värd](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Program och tjänsten loggar/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V-värd) |Tillhandahåller användbar loggning för att felsöka problem med många hantering av Hyper-V virtuell dator. <br/> ![Platsen för Virtual Machine Manager händelsekällan för Hyper-V-värd](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |

### <a name="hyper-v-replication-logging-options"></a>Alternativ för loggning av Hyper-V-replikering
Alla händelser som hör till Hyper-V-replikering loggas i Hyper-V-VMMS\\Admin logg finns under program- och tjänstloggar\\Microsoft\\Windows. Dessutom kan du aktivera en analytiska logg för Hyper-V Virtual Machine Management-tjänsten. Om du vill aktivera den här loggfilen först göra de analytiska loggarna och felsökningsloggar kan visas i Loggboken. Öppna Loggboken och klicka sedan på **visa** > **visa analytiska loggar och felsökningsloggar**.

![Alternativet Visa analytiska loggar och felsökningsloggar](media/site-recovery-monitoring-and-troubleshooting/image14.png)

En analytiska loggen visas under **Hyper-V-VMMS**.

![Analytiska loggar i visningsprogrammet träd](media/site-recovery-monitoring-and-troubleshooting/image15.png)

I den **åtgärder** rutan klickar du på **aktivera loggning**. När den är aktiverad, visas den i **Prestandaövervakaren** som en **spårningssessionen för händelser** finns under **datainsamlaruppsättningar.**

![Spårningssessioner för händelser i trädet Prestandaövervakaren](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Om du vill visa den insamlade informationen stoppa spårningssessionen genom att inaktivera loggen. Spara loggen, och öppna den igen i Loggboken eller använda andra verktyg för att konvertera den efter behov.

## <a name="reach-out-for-microsoft-support"></a>Nå ut Support för Microsoft
### <a name="log-collection"></a>Logginsamling
Skydd av Virtual Machine Manager-plats finns i [Azure Site Recovery Logginsamling Support Diagnostics plattformen (SDP) verktyget](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) att samla in loggar som krävs.

Skydd för Hyper-V-platsen, kan du hämta den [verktyget](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) och köra på Hyper-V-värden för att samla in loggarna.

VMware/fysisk server scenarier finns i [Azure Site Recovery Logginsamling för VMware och fysiska plats protection](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) att samla in loggar som krävs.

Verktyget samlar in loggar lokalt i en slumpmässigt undermapp under % LocalAppData%\ElevatedDiagnostics.

![Exempel steg visas från skydd för Hyper-V-platsen.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="open-a-support-ticket"></a>Öppna ett supportärende
Om du vill höja ett supportärende för Azure Site Recovery, nå ut till stöd för Azure med hjälp av URL-Adressen på <http://aka.ms/getazuresupport>.

## <a name="kb-articles"></a>KB-artiklar
* [Hur du bevarar enhetsbeteckningen för skyddade virtuella datorer som redundansväxlas eller migreras till Azure](http://support.microsoft.com/kb/3031135)
* [Hur du hanterar lokala i Azure-skydd av nätverksbandbredd](https://support.microsoft.com/kb/3056159)
* [Azure Site Recovery: ”Det gick inte att hitta klusterresursen” fel vid försök att aktivera skyddet för en virtuell dator](http://support.microsoft.com/kb/3010979)
* [Förstå och felsöka Hyper-V-replikering guide](http://social.technet.microsoft.com/wiki/contents/articles/21948.hyper-v-replica-troubleshooting-guide.aspx)

## <a name="common-azure-site-recovery-errors-and-their-resolutions"></a>Vanliga fel i Azure Site Recovery och sina lösningar
Nedan följer vanliga fel och sina lösningar. Varje fel dokumenteras i en separat wiki-sida.

### <a name="general"></a>Allmänt
* <span style="color:green;">NYA</span> [jobb misslyckas med felet ”en åtgärd pågår”. Fel 505, 514, 532.](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
* <span style="color:green;">NYA</span> [jobb misslyckas med felet ”servern är inte ansluten till Internet”. Fel 25018.](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Konfiguration
* [Virtual Machine Manager-servern kan inte registreras på grund av ett internt fel. Hänvisa till jobbvyn på Site Recovery-portalen för mer information om felet. Kör installationsprogrammet igen för att registrera servern.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
* [Det går inte att upprätta en anslutning till Hyper-V Recovery Manager-valvet. Kontrollera proxyinställningarna eller försök igen senare.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Konfiguration
* [Det gick inte att skapa Skyddsgruppen: ett fel uppstod vid inläsningen av listan över servrar.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
* [Hyper-V-värdkluster innehåller minst ett statiskt nätverkskort eller inga anslutna nätverkskort konfigurerade för DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
* [Virtual Machine Manager har inte behörighet för att slutföra en åtgärd.](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
* [Det går inte att markera storage-konto i prenumerationen vid konfiguration av skydd.](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Skydd
* <span style="color:green;">NYA</span> [Aktivera skydd misslyckas med felet ”Det gick inte att konfigurera skydd för den virtuella datorn”. Fel 60007, 40003.](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
* <span style="color:green;">NYA</span> [Aktivera skydd misslyckas med felet ”Det gick inte att aktivera skydd för den virtuella datorn”. Fel 70094.](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
* <span style="color:green;">NYA</span> [Direktmigrering fel 23848 - den virtuella datorn kommer att flyttas med typen Live. Det kan innebära att återställningsskyddsstatus för den virtuella datorn.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx)
* [Aktivera skyddet misslyckades eftersom agenten inte installerad på värddatorn.](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
* [Det går inte att hitta en lämplig värd för den virtuella replikdatorn - på grund av låg beräkningsresurser.](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
* [En lämplig värd för den replikerade virtuella datorn kan inte hittas - på grund av att inga logiska nätverk är kopplat.](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
* [Det går inte att ansluta till värddatorn för replik - det gick inte att ansluta.](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)

### <a name="recovery"></a>Återställning
* Det går inte att slutföra värdåtgärden på Virtual Machine Manager:
  * [Växla över till den valda återställningspunkten för den virtuella datorn: Allmänt meddelande om nekad åtkomst.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
  * [Det gick inte att växla över till den valda återställningspunkten för virtuella Hyper-V: åtgärden avbröts.  Försök senare återställningspunkt. (0x80004004).](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
  * En anslutning till servern gick inte att upprätta (0x00002EFD).
    * [Det gick inte att aktivera omvänd replikering för virtuella Hyper-V.](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
    * [Det gick inte att aktivera replikering för virtuell dator virtuella Hyper-V.](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
  * [Det gick inte att genomföra redundans för den virtuella datorn.](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
* [Återställningsplanen innehåller virtuella datorer som inte är redo för planerad redundans.](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
* [Den virtuella datorn är inte redo för planerad redundans.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* [Virtuella datorn körs inte och är inte avstängda.](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
* [Det uppstod ett out-of-band-åtgärd på en virtuell dator och genomföra redundans misslyckades.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* Testa redundans
  * [Redundansväxlingen kunde inte initieras eftersom redundanstestning pågår.](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
* <span style="color:green;">NYA</span> växling vid fel på grund av timeout 'PreFailoverWorkflow uppgift WaitForScriptExecutionTaskTimeout' på grund av konfigurationsinställningarna på Nätverkssäkerhetsgrupp kopplad till den virtuella datorn eller undernät som datorn tillhör. Referera till ['PreFailoverWorkflow aktivitet WaitForScriptExecutionTaskTimeout'](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) mer information.

### <a name="configuration-server-process-server-master-target"></a>Konfigurationsservern, processervern, huvudmålservern
* [ESXi-värd som ligger PS/CS som en virtuell dator misslyckas med en lila skärm död.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Fjärrskrivbord felsökning efter växling vid fel
* Många kunder har upptäckt ett problem med att ansluta till den via virtuell dator i Azure. [Använd felsökning dokument till RDP till den virtuella datorn](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Lägga till en offentlig IP-adress på en resource manager virtuell dator
Om den **Anslut** knapp i portalen är nedtonad och du inte är ansluten till Azure via en Express Route eller plats-till-plats VPN-anslutning måste du skapa och tilldela den virtuella datorn en offentlig IP-adress innan du kan använda Remote Desktop/delade Shell. Du kan sedan lägga till en offentlig IP-adress för gränssnitt på den virtuella datorn.  

![Lägger till en offentlig IP-adress på nätverksgränssnittet för redundansväxling virtuell dator](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)
