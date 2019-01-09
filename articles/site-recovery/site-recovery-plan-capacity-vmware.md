---
title: Planera kapacitet och skalning för VMware-haveriberedskap till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln kan hjälpa dig att planera kapacitet och skalning när du konfigurerar haveriberedskap för virtuella VMware-datorer till Azure med hjälp av Azure Site Recovery.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 29e01177d4b096449cd906a22b47223078c6493e
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107828"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planera kapacitet och skalning för VMware-haveriberedskap till Azure

Använd den här artikeln för att planera för kapacitet och skalning när du replikerar lokala virtuella VMware-datorer och fysiska servrar till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Hur börjar jag planera för kapaciteten?

Mer information om kraven för Azure Site Recovery-infrastruktur, samla in information om replikeringsmiljön genom att köra [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) för VMware-replikering. Mer information finns i [om Site Recovery Deployment Planner för VMware till Azure](site-recovery-deployment-planner.md). 

Distributionshanteraren för site Recovery tillhandahåller en rapport som har fullständig information om kompatibla och inkompatibla virtuella datorer, diskar per virtuell dator, och dataomsättning per disk. Verktyget sammanfattas också kraven på nätverksbandbredd att uppfylla RPO-mål och Azure-infrastrukturen som krävs för lyckad replikering och testning av redundans.

## <a name="capacity-considerations"></a>Överväganden för kapacitet

Komponent | Information
--- | ---
**Replikering** | **Maximal dagliga förändringstakten**: En skyddad dator kan använda endast en processerver. En enda processerver kan hantera en daglig ändringen Betygsätt upp till 2 TB. 2 TB är därför de maximala daglig dataändringshastighet som stöds för en skyddad dator.<br /><br /> **Maximalt dataflöde**: En replikerad dator kan höra till ett lagringskonto i Azure. Ett Azure Storage-konto av standardtyp kan hantera upp till 20 000 begäranden per sekund. Vi rekommenderar att du begränsar antalet indata/utdataåtgärder per sekund (IOPS) över en källdator till 20 000. Om du har en källdator som har fem diskar och varje disk genererar 120 IOPS (8 kB) på källdatorn, exempelvis ligger källdatorn inom Azure per disk-IOPS-gränsen på 500. (Antal lagringskonton som krävs är lika med den totala källdatorn dividerat med 20 000 IOPS.)
**Konfigurationsserver** | Configuration server måste kunna hantera dagliga ändra frekvensen kapaciteten i alla arbetsbelastningar som körs på skyddade datorer. Konfiguration av datorn måste ha tillräckligt mycket bandbredd för att kontinuerligt replikera data till Azure Storage.<br /><br /> Ett bra tips är att placera konfigurationsservern på samma nätverk och LAN-segment som de datorer som du vill skydda. Du kan placera konfigurationsservern på ett annat nätverk, men datorer som du vill skydda måste ha 3 nätverk skiktsynlighet.<br /><br /> Storleksrekommendationer för konfigurationsservern sammanfattas i tabellen i avsnittet nedan.
**Processervern** | Den första processervern installeras som standard på konfigurationsservern. Du kan distribuera ytterligare processervrar för att skala din miljö. <br /><br /> Processervern tar emot replikeringsdata från skyddade datorer. Processervern optimerar data med hjälp av cachelagring, komprimering och kryptering. Processervern skickar sedan data till Azure. Den process server-datorn måste ha tillräckligt med resurser för att utföra dessa uppgifter.<br /><br /> Processervern använder ett diskbaserad cacheminne. Använd en separat cachedisk 600 GB eller mer för att hantera dataförändringar av som lagras om ett flaskhalsar i nätverket eller avbrott inträffar.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Storleksrekommendationer för konfigurationsservern och inbyggda processervern

En konfigurationsserver som använder en inbyggd processerver för att skydda arbetsbelastningen kan hantera upp till 200 virtuella datorer baserat på följande konfigurationer:

Processor | Minne | Cachestorleken för disk | Dataändringshastigheten | Skyddade datorer
--- | --- | --- | --- | ---
8 virtuella processorer (2 platser * 4 kärnor \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB eller mindre | Använd för att replikera färre än 100 virtuella datorer.
12 virtuella processorer (2 platser * 6 kärnor \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB till 1 TB | Använd för att replikera 100-150 datorer.
16 virtuella processorer (2 platser * 8 kärnor \@ 2,5 GHz) | 32 GB | 1 TB | > 1 TB till 2 TB | Använd för att replikera 151 till 200 virtuella datorer.
Distribuera en annan konfigurationsserver med hjälp av en [OVF-mall](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). | | | | Distribuera en ny configuration server om du replikerar mer än 200 datorer.
Distribuera en annan [processervern](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | &GT; 2 TB| Distribuera en ny skalbar processerver om den totala dagliga förändringstakten för data är större än 2 TB.

I de här konfigurationerna:

* Varje källdatorn har tre diskar på 100 GB vardera.
* Vi använde benchmarking lagring av åtta signatur för delad åtkomst-enheter på 10 K RPM med RAID 10 för mätningar av cache-disk.

## <a name="size-recommendations-for-the-process-server"></a>Storleksrekommendationer för processervern

Processervern är den komponent som hanterar replikering av data i Azure Site Recovery. Om den dagliga förändringstakten är större än 2 TB, måste du lägga till skala ut processervrar för att hantera belastningen för replikering. Om du vill skala ut kan du:

* Öka antalet konfigurationsservrar genom att distribuera med hjälp av en [OVF-mall](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). Du kan till exempel skydda upp till 400 datorer med hjälp av två konfigurationsservrar.
* Lägg till [skala ut processervrar](vmware-azure-set-up-process-server-scale.md#download-installation-file). Använd skala ut processervrar för att hantera replikeringstrafik i stället för (eller förutom) konfigurationsservern.

I följande tabell beskrivs i det här scenariot:

* Du kan konfigurera en skalbar processerver.
* Du har konfigurerat den skyddade virtuella datorer för att använda skalbara processerver.
* Varje skyddad källdatorn har tre diskar på 100 GB vardera.

Kompletterande processervern | Cachestorleken för disk | Dataändringshastigheten | Skyddade datorer
--- | --- | --- | ---
4 virtuella processorer (2 platser * 2 kärnor \@ 2,5 GHz), 8 GB minne | 300 GB | 250 GB eller mindre | Använd för att replikera 85 eller färre virtuella datorer.
8 virtuella processorer (2 platser * 4 kärnor \@ 2,5 GHz), 12 GB minne | 600 GB | 251 GB till 1 TB | Använd för att replikera 86 150 datorer.
12 virtuella processorer (2 platser * 6 kärnor \@ 2,5 GHz) 24 GB minne | 1 TB | > 1 TB till 2 TB | Använd för att replikera 151 225 datorer.

Hur du skalar dina servrar beror på dina inställningar för en modell med skala upp eller skala ut. Att skala upp, distribuera några avancerade konfigurationsservrar och bearbeta servrar. Distribuera fler servrar som har färre resurser för att skala ut. Om du vill skydda 200 datorer med en övergripande daglig dataändringshastighet på 1,5 TB, kan du göra något av följande åtgärder:

* Konfigurera en enda processerver (16 vCPU, 24 GB RAM-minne).
* Ställa in två processervrar (2 x 8 vCPU, 2 * 12 GB RAM-minne).

## <a name="control-network-bandwidth"></a>Kontrollera nätverksbandbredd

När du använder [Site Recovery Deployment Planner](site-recovery-deployment-planner.md) för att beräkna den bandbredd som du behöver för replikering (inledande replikering och sedan delta), du har ett par alternativ för att styra mängden bandbredd som används för replikering:

* **Begränsa bandbredden**: VMware-trafik som replikeras till Azure går igenom en särskild processerver. Du kan begränsa bandbredden på de datorer som körs som processervrar.
* **Påverka bandbredd**: Du kan påverka den bandbredd som används för replikering med hjälp av några registernycklar:
  * Den **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** registervärdet anger antalet trådar som används för att överföra data (inledande replikering eller delta) för en disk. Ett högre värde ökar nätverksbandbredden som används för replikering.
  * Den **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** registervärdet anger antalet trådar som används för att överföra data under återställning efter fel.

### <a name="throttle-bandwidth"></a>Begränsa bandbredden

1. Öppna snapin-modulen för Azure Backup MMC på den dator som du använder som processervern. Som standard är en genväg till Backup på skrivbordet eller i följande mapp: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. I snapin-modulen, väljer **ändra egenskaper för**.

    ![Skärmbild av Azure Backup MMC snapin-modulen möjlighet att ändra egenskaper](./media/site-recovery-vmware-to-azure/throttle1.png)
3. På den **begränsning** fliken **aktivera Användningsbegränsning för internetbandbredd för säkerhetskopieringsåtgärder**. Ange begränsningarna för arbetstid och icke-tid. Giltiga intervall är från 512 kbit/s till 1,023 Mbit/s.

    ![Skärmbild av dialogrutan Egenskaper för Azure-säkerhetskopiering](./media/site-recovery-vmware-to-azure/throttle2.png)

Du kan också ange begränsningar med hjälp av cmdleten [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx). Här är ett exempel:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** anger att ingen begränsning krävs.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Ändra nätverkets bandbredd för en virtuell dator

1. I den Virtuella datorns register, går du till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Om du vill ändra bandbreddstrafiken på en replikeringsdisk, ändrar du värdet för **UploadThreadsPerVM**. Skapa nyckeln om det inte finns.
   * Om du vill ändra bandbredd för redundanstrafik från Azure, ändrar du värdet för **DownloadThreadsPerVM**.
2. Standardvärdet för varje nyckel är **4**. I ett ”överetablerat” nätverk bör du ändra registernycklarnas standardvärden. Det maximala värdet som du kan använda **32**. Övervaka trafiken för att optimera värdet.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Ställa in Site Recovery-infrastruktur för att skydda fler än 500 virtuella datorer

Innan du konfigurerar Site Recovery-infrastruktur kan du komma åt miljön för att mäta följande faktorer: kompatibla virtuella datorer i daglig dataändringshastighet, nödvändig nätverksbandbredd för rpo-mål som du vill uppnå, hur många Site Recovery komponenter som krävs och den tid det tar för att slutföra den inledande replikeringen. Utför följande steg för att samla in informationen som krävs:

1. Kör Distributionshanteraren för Site Recovery på din miljö för att mäta dessa parametrar. Användbara riktlinjer finns i [om Site Recovery Deployment Planner för VMware till Azure](site-recovery-deployment-planner.md).
2. Distribuera en konfigurationsserver som uppfyller den [storleksrekommendationer för konfigurationsservern](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Om din produktionsarbetsbelastning överskrider 650 virtuella datorer kan du distribuera en annan konfigurationsserver.
3. Baserat på de uppmätta dagliga dataändringshastigheten, distribuera [skala ut processervrar](vmware-azure-set-up-process-server-scale.md#download-installation-file) med hjälp av [storlek riktlinjer](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Om du förväntar dig dataändringshastighet för en virtuell dator för disken överskrider 2 Mbit/s [ställa in ett premiumlagringskonto](tutorial-prepare-azure.md#create-a-storage-account). Site Recovery Deployment Planner körs för en viss tidsperiod. Toppar i förändringstakten för data vid andra tillfällen kan inte finnas i rapporten.
5. [Ange nätverksbandbredden](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) baserat på det RPO som du vill uppnå.
6. När infrastrukturen har ställts in, aktivera haveriberedskap för din arbetsbelastning. Läs hur genom att läsa [konfigurera källmiljön för VMware till Azure-replikering](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Distribuera ytterligare processervrar

Om du skala upp distributionen utöver 200 källdatorer, eller om du har en total daglig dataomsättningsfrekvensen på mer än 2 TB, måste du lägga till processervrar för att hantera trafik. Läs hur du ställer in processervern i [skala för återställning efter fel med hjälp av ytterligare processervrar](vmware-azure-set-up-process-server-scale.md). När du har konfigurerat processervern kan migrera du källdatorer för att använda den.

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrera datorer om du vill använda den nya processervern

1. Välj **inställningar** > **Site Recovery-servrar**. Välj configuration server och expandera sedan **bearbeta servrar**.

    ![Skärmbild av dialogrutan Process Server](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Högerklicka på processervern som används och välj sedan **växel**.

    ![Skärmbild av dialogrutan Configuration server](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. I **Välj målprocesserver**, Välj den nya för processervern som du vill använda. Välj de virtuella datorerna som servern ska hantera. Välj information för att få information om servern. Som hjälper dig att läsa in beslut, visas det genomsnittliga utrymmet som krävs för att replikera alla valda virtuella datorn till den nya processervern. Välj bockmarkeringen för att börja replikera till den nya processervern.

## <a name="deploy-additional-master-target-servers"></a>Distribuera ytterligare huvudmålservrar

I följande scenarier krävs mer än en huvudmålserver:

*   Du vill skydda en Linux-baserad virtuell dator.
*   Huvudmålservern på konfigurationsservern har inte åtkomst till databasen för den virtuella datorn.
*   Det totala antalet diskar på huvudmålservern (antal lokala diskar på servern) plus antalet diskar som ska skyddas är större än 60 diskar.

Läs hur du lägger till en huvudmålserver för en Linux-baserad virtuell dator i [installera en Linux-huvudmålserver för återställning efter fel](vmware-azure-install-linux-master-target.md).

Lägga till en huvudmålserver för en Windows-baserad virtuell dator:

1. Gå till **Recovery Services-valv** > **Site Recovery-infrastruktur** > **konfigurationsservrar**.
2. Välj obligatoriska konfigurationsservern och välj sedan **Huvudmålservern**.

    ![Skärmbild som visar knappen Lägg till Huvudmålservern](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Ladda ned enhetlig installationsfilen och kör sedan filen på den virtuella datorn för att konfigurera huvudmålservern.
4. Välj **installera huvudmålservern** > **nästa**.

    ![Skärmbild som visar att välja alternativet Installera huvudmålservern](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Välj standardplatsen för installation och välj sedan **installera**.

     ![Skärmbild som visar standardplatsen för installation](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Om du vill registrera Huvudmålet med konfigurationsservern, Välj **gå vidare till konfiguration**.

    ![Skärmbild som visar Fortsätt till konfiguration](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Ange IP-adressen för konfigurationsservern och sedan ange lösenfrasen. Läs hur du skapar en lösenfras i [generera ett lösenord för konfigurationsservern](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Skärmbild som visar var du anger IP-adressen och lösenfras för konfigurationsservern](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Välj **Registrera**. När registreringen är klar, Välj **Slutför**.

När registreringen har slutförts visas servern i Azure-portalen på **Recovery Services-valv** > **Site Recovery-infrastruktur**  >   **Konfigurationsservrar**, i huvudmålservrarna för konfigurationsservern.

 > [!NOTE]
 > Ladda ned den senaste versionen av den [huvudmålservern server enhetlig installationsfilen för Windows](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Nästa steg

Ladda ned och kör [Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner).
