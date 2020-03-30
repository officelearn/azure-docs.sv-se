---
title: Planera kapacitet för VMware-haveriberedskap med Azure Site Recovery
description: Den här artikeln kan hjälpa dig att planera kapacitet och skalning när du ställer in haveriberedskap för virtuella virtuella datorer med VMware till Azure med hjälp av Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 467c70a722b8a243be6ac2826188a4ba3459aa06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257620"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planera kapacitet och skalning för VMware-haveriberedskap till Azure

Använd den här artikeln för att planera kapacitet och skalning när du replikerar lokala virtuella datorer med VMware och fysiska servrar till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Hur startar jag kapacitetsplanering?

Om du vill veta mer om infrastrukturkrav för Azure Site Recovery samlar du in information om replikeringsmiljön genom att köra [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) för VMware-replikering. Mer information finns i [Om Site Recovery Deployment Planner för VMware till Azure](site-recovery-deployment-planner.md). 

Site Recovery Deployment Planner innehåller en rapport som innehåller fullständig information om kompatibla och inkompatibla virtuella datorer, diskar per virtuell dator och dataomsättning per disk. Verktyget sammanfattar också kraven på nätverksbandbredd för att uppfylla mål-RPO och Azure-infrastrukturen som krävs för lyckad replikering och test redundans.

## <a name="capacity-considerations"></a>Överväganden för kapaciteter

Komponent | Information
--- | ---
**Replikering** | **Maximal daglig ändringshastighet:** En skyddad dator kan bara använda en processserver. En enda processerver kan hantera en daglig ändrings hastighet på upp till 2 TB. Så 2 TB är den maximala dagliga dataändringshastigheten som stöds för en skyddad dator.<br /><br /> **Maximalt dataflöde:** En replikerad dator kan tillhöra ett lagringskonto i Azure. Ett standardkonto för Azure Storage kan hantera maximalt 20 000 begäranden per sekund. Vi rekommenderar att du begränsar antalet in-/utdataåtgärder per sekund (IOPS) över en källdator till 20 000. Om du till exempel har en källdator som har fem diskar och varje disk genererar 120 IOPS (8 K i storlek) på källdatorn ligger källdatorn inom Azure-IOPS-gränsen per disk på 500. (Antalet lagringskonton som krävs är lika med den totala källmaskinen IOPS dividerat med 20 000.)
**Konfigurationsserver** | Konfigurationsservern måste kunna hantera den dagliga förändringshastighetens kapacitet för alla arbetsbelastningar som körs på skyddade datorer. Konfigurationsmaskinen måste ha tillräcklig bandbredd för att kontinuerligt replikera data till Azure Storage.<br /><br /> En bra metod är att placera konfigurationsservern i samma nätverks- och LAN-segment som de datorer som du vill skydda. Du kan placera konfigurationsservern i ett annat nätverk, men datorer som du vill skydda bör ha nätverkssynlighet på lager 3.<br /><br /> Storleksrekommendationer för konfigurationsservern sammanfattas i tabellen i följande avsnitt.
**Bearbeta server** | Den första processservern installeras som standard på konfigurationsservern. Du kan distribuera ytterligare processservrar för att skala din miljö. <br /><br /> Processservern tar emot replikeringsdata från skyddade datorer. Processservern optimerar data med hjälp av cachelagring, komprimering och kryptering. Sedan skickar processservern data till Azure. Processserverdatorn måste ha tillräckliga resurser för att utföra dessa uppgifter.<br /><br /> Processservern använder en diskbaserad cache. Använd en separat cachedisk på 600 GB eller mer för att hantera dataändringar som lagras om en flaskhals eller avbrott uppstår i nätverket.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Storleksrekommendationer för konfigurationsservern och den inbyggda processservern

En konfigurationsserver som använder en inbyggd processserver för att skydda arbetsbelastningen kan hantera upp till 200 virtuella datorer baserat på följande konfigurationer:

Processor | Minne | Cachediskstorlek | Dataändringshastighet | Skyddade datorer
--- | --- | --- | --- | ---
8 virtuella processorer (2 uttag * \@ 4 kärnor 2,5 GHz) | 16 GB | 300 GB | 500 GB eller mindre | Används för att replikera färre än 100 datorer.
12 virtuella processorer (2 uttag * \@ 6 kärnor 2,5 GHz) | 18 GB | 600 GB | 501 GB till 1 TB | Används för att replikera 100 till 150 maskiner.
16 vCPUs (2 uttag * \@ 8 kärnor 2,5 GHz) | 32 GB | 1 TB | >1 TB till 2 TB | Används för att replikera 151 till 200 maskiner.
Distribuera en annan konfigurationsserver med hjälp av en [OVF-mall](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). | | | | Distribuera en ny konfigurationsserver om du replikerar fler än 200 datorer.
Distribuera en annan [processserver](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Distribuera en ny skalningsprocessserver om den totala dagliga dataändringshastigheten är större än 2 TB.

I dessa konfigurationer:

* Varje källmaskin har tre diskar på 100 GB vardera.
* Vi använde benchmarkinglagring av åtta signaturenheter med delad åtkomst på 10 K RPM med RAID 10 för cachediskmätningar.

## <a name="size-recommendations-for-the-process-server"></a>Storleksrekommendationer för processservern

Processservern är den komponent som hanterar datareplikering i Azure Site Recovery. Om den dagliga ändringshastigheten är större än 2 TB måste du lägga till skalningsprocessservrar för att hantera replikeringsbelastningen. Om du vill skala ut kan du:

* Öka antalet konfigurationsservrar genom att distribuera med hjälp av en [OVF-mall](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template). Du kan till exempel skydda upp till 400 datorer med hjälp av två konfigurationsservrar.
* Lägg till [utskalningsprocessservrar](vmware-azure-set-up-process-server-scale.md#download-installation-file). Använd skalningsprocessservrarna för att hantera replikeringstrafik i stället för (eller utöver) konfigurationsservern.

I följande tabell beskrivs det här scenariot:

* Du kan ställa in en utskalningsprocessserver.
* Du har konfigurerat skyddade virtuella datorer för att använda utskalningsprocessservern.
* Varje skyddad källdator har tre diskar på 100 GB vardera.

Ytterligare processserver | Cachediskstorlek | Dataändringshastighet | Skyddade datorer
--- | --- | --- | ---
4 vCPUs (2 uttag * \@ 2 kärnor 2,5 GHz), 8 GB minne | 300 GB | 250 GB eller mindre | Används för att replikera 85 eller färre datorer.
8 virtuella processorer (2 uttag * \@ 4 kärnor 2,5 GHz), 12 GB minne | 600 GB | 251 GB till 1 TB | Används för att replikera 86 till 150 maskiner.
12 virtuella processorer (2 uttag * \@ 6 kärnor 2,5 GHz) 24 GB minne | 1 TB | >1 TB till 2 TB | Används för att replikera 151 till 225 maskiner.

Hur du skalar servrarna beror på dina önskemål om en skalnings- eller skalningsmodell. Om du vill skala upp distribuerar du några avancerade konfigurationsservrar och processservrar. Distribuera fler servrar som har mindre resurser för att skala ut. Om du till exempel vill skydda 200 datorer med en total daglig dataändringshastighet på 1,5 TB kan du vidta någon av följande åtgärder:

* Konfigurera en enda processserver (16 vCPU, 24 GB RAM).
* Konfigurera två processservrar (2 x 8 vCPU, 2* 12 GB RAM).

## <a name="control-network-bandwidth"></a>Kontrollera nätverkets bandbredd

När du har använt [Site Recovery Deployment Planner](site-recovery-deployment-planner.md) för att beräkna den bandbredd du behöver för replikering (inledande replikering och sedan delta) har du ett par alternativ för att kontrollera den mängd bandbredd som används för replikering:

* **Throttle bandbredd:** VMware trafik som replikerar till Azure går igenom en viss processserver. Du kan begränsa bandbredden på de datorer som körs som processservrar.
* **Påverka bandbredden:** Du kan påverka den bandbredd som används för replikering med hjälp av ett par registernycklar:
  * Registervärdet **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** anger antalet trådar som används för dataöverföring (initial eller deltareplikering) på en disk. Ett högre värde ökar nätverksbandbredden som används för replikering.
  * Registervärdet **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** anger antalet trådar som används för dataöverföring under återställning efter fel.

### <a name="throttle-bandwidth"></a>Begränsa bandbredden

1. Öppna SNAPIN-MODULEN Azure Backup MMC på den dator som du använder som processserver. Som standard är en genväg för Säkerhetskopiering tillgänglig på skrivbordet eller i följande mapp: C:\Program\Microsoft Azure Recovery Services Agent\bin.
2. Välj **Ändra egenskaper**i snapin-modulen .

    ![Skärmbild av snapin-modulen Azure Backup MMC för att ändra egenskaper](./media/site-recovery-vmware-to-azure/throttle1.png)
3. På fliken **Begränsning** väljer du Aktivera begränsning av **internetbandbredd för säkerhetskopieringsåtgärder**. Ange gränser för arbete och icke-arbetstid. Giltiga intervall är från 512 kbit/s till 1 023 Mbit/s.

    ![Skärmbild av dialogrutan Egenskaper för Azure-säkerhetskopiering](./media/site-recovery-vmware-to-azure/throttle2.png)

Du kan också ange begränsningar med hjälp av cmdleten [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx). Här är ett exempel:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** anger att ingen begränsning krävs.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Ändra nätverksbandbredden för en virtuell dator

1. Gå till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**i den virtuella datorns register.
   * Om du vill ändra bandbreddstrafiken på en replikerande disk ändrar du värdet **för UploadThreadsPerVM**. Skapa nyckeln om den inte finns.
   * Om du vill ändra bandbredden för återställningstrafik från Azure ändrar du värdet **för DownloadThreadsPerVM**.
2. Standardvärdet för varje nyckel är **4**. I ett ”överetablerat” nätverk bör du ändra registernycklarnas standardvärden. Det maximala värdet du kan använda är **32**. Övervaka trafiken för att optimera värdet.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Konfigurera infrastrukturen för webbplatsåterställning för att skydda mer än 500 virtuella datorer

Innan du konfigurerar infrastrukturen för webbplatsåterställning, få tillgång till miljön för att mäta följande faktorer: kompatibla virtuella datorer, den dagliga dataändringshastigheten, den nätverksbandbredd som krävs för den RPO du vill uppnå, antalet site recovery komponenter som krävs och den tid det tar att slutföra den första replikeringen. Gör följande för att samla in den information som krävs:

1. Om du vill mäta dessa parametrar kör du Site Recovery Deployment Planner i din miljö. Användbara riktlinjer finns i [Om site recovery deployment planner för VMware till Azure](site-recovery-deployment-planner.md).
2. Distribuera en konfigurationsserver som uppfyller [storleksrekommendationerna för konfigurationsservern](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Om produktionsarbetsbelastningen överskrider 650 virtuella datorer distribuerar du en annan konfigurationsserver.
3. Baserat på den uppmätta dagliga dataförändringshastigheten distribuerar du [utskalningsprocessservrar](vmware-azure-set-up-process-server-scale.md#download-installation-file) med hjälp av [storleksriktlinjer](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Om du förväntar dig att dataändringshastigheten för en virtuell diskdator ska överstiga 2 MBps, se till att du använder premium hanterade diskar. Site Recovery Deployment Planner körs under en viss tidsperiod. Toppar i dataändringshastigheten vid andra tillfällen kanske inte fångas in i rapporten.
5. [Ställ in nätverksbandbredden](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) baserat på den RPO du vill uppnå.
6. När infrastrukturen har konfigurerats aktiverar du haveriberedskap för din arbetsbelastning. Mer information finns i [Konfigurera källmiljön för VMware till Azure replication](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Distribuera ytterligare processservrar

Om du skalar ut distributionen efter 200 källdatorer eller om du har en total daglig omsättningshastighet på mer än 2 TB måste du lägga till processservrar för att hantera trafikvolymen. Vi har förbättrat produkten i 9.24-versionen för att tillhandahålla [processservervarningar](vmware-physical-azure-monitor-process-server.md#process-server-alerts) om när du ska konfigurera en skalningsprocessserver. [Konfigurera processservern](vmware-azure-set-up-process-server-scale.md) för att skydda nya källdatorer eller [balansera belastningen](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrera datorer för att använda den nya processservern

1. Välj **Inställningar** > **Site Recovery servrar**. Välj konfigurationsservern och expandera sedan **Processservrar**.

    ![Skärmbild av dialogrutan Process server](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Högerklicka på den processserver som används och välj sedan **Växla**.

    ![Skärmbild av dialogrutan Konfigurationsserver](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Välj den nya processserver som du vill använda i **Välj målprocessserver.** Välj sedan de virtuella datorer som servern ska hantera. Om du vill ha information om servern väljer du informationsikonen. För att hjälpa dig att fatta belastningsbeslut visas det genomsnittliga utrymme som krävs för att replikera varje vald virtuell dator till den nya processservern. Markera kryssrutan om du vill börja replikera till den nya processservern.

## <a name="deploy-additional-master-target-servers"></a>Distribuera ytterligare huvudmålservrar

I följande scenarier krävs mer än en huvudmålserver:

*   Du vill skydda en Linux-baserad virtuell dator.
*   Huvudmålservern som är tillgänglig på konfigurationsservern har inte åtkomst till den virtuella datorns datalager.
*   Det totala antalet diskar på huvudmålservern (antalet lokala diskar på servern plus antalet diskar som ska skyddas) är större än 60 diskar.

Mer information om hur du lägger till en huvudmålserver för en Linux-baserad virtuell dator finns i [Installera en Målserver för Linux-huvudhanteraren för återställning efter fel](vmware-azure-install-linux-master-target.md).

Så här lägger du till en huvudmålserver för en Windows-baserad virtuell dator:

1. Gå till Recovery **Services Vault** > **Site Recovery Infrastructure** > **Configuration servers**.
2. Välj den konfigurationsserver som krävs och välj sedan **Huvudmålserver**.

    ![Skärmbild som visar knappen Lägg till huvudmålserver](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Hämta den enhetliga installationsfilen och kör sedan filen på den virtuella datorn för att konfigurera huvudmålservern.
4. Välj **Installera huvudmål** > **Nästa**.

    ![Skärmbild som visar att välja alternativet Installera huvudmål](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Välj standardinstallationsplatsen och välj sedan **Installera**.

     ![Skärmbild som visar standardinstallationsplatsen](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Om du vill registrera huvudmålet med konfigurationsservern väljer du **Fortsätt till konfiguration**.

    ![Skärmbild som visar knappen Fortsätt till konfiguration](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Ange konfigurationsserverns IP-adress och ange sedan lösenfrasen. Mer information om hur du genererar en lösenfras finns i [Generera en konfigurationsserver lösenfras](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Skärmbild som visar var IP-adressen och lösenfrasen ska anges för konfigurationsservern](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Välj **Registrera**. När registreringen är klar väljer du **Slutför**.

När registreringen har slutförts visas servern i Azure-portalen på Recovery **Services Vault** > **Site Recovery Infrastructure** > **Configuration servers**, i konfigurationsserverns huvudmålservrar.

 > [!NOTE]
 > Hämta den senaste versionen av [huvudmålserverns enhetliga installationsfil för Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Nästa steg

Hämta och kör [Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
