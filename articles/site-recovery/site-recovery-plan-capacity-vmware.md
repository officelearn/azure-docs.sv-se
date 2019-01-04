---
title: Planera kapacitet och skalning för VMware-haveriberedskap till Azure med Azure Site Recovery | Microsoft Docs
description: Använd den här artikeln att planera kapacitet och skala när du konfigurerar haveriberedskap för virtuella VMware-datorer till Azure med Azure Site Recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 6f644416a9e56009aadd0f8e1b217402d625af84
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788743"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planera kapacitet och skalning för VMware-haveriberedskap till Azure

Använd den här artikeln för att räkna ut planera för kapacitet och skalning, när du replikerar lokala virtuella VMware-datorer och fysiska servrar till Azure med [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Hur börjar jag planera för kapaciteten?

Om du vill veta krav för Azure Site Recovery-infrastruktur, samla in information om replikeringsmiljön genom att köra den [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner-doc) för VMware-replikering. [Läs mer](site-recovery-deployment-planner.md) om det här verktyget. Det här verktyget innehåller en rapport med fullständig information om kompatibla och inkompatibla virtuella datorer, diskar per virtuell dator, och dataomsättning per disk. Verktyget sammanfattas också kraven på nätverksbandbredd att uppfylla RPO-mål, och Azure-infrastrukturen behövs för lyckad replikering och testning av redundans.

## <a name="capacity-considerations"></a>Överväganden för kapacitet

**Komponent** | **Detaljer** |
--- | --- | ---
**Replikering** | **Maximal dagliga förändringstakten:** En skyddad dator kan bara använda en processerver och en enda processerver kan hantera en daglig ändringen Betygsätt upp till 2 TB. 2 TB är därför de maximala daglig dataändringshastighet som stöds för en skyddad dator.<br/><br/> **Maximalt dataflöde:** En replikerad dator kan höra till ett lagringskonto i Azure. Ett standardlagringskonto kan hantera upp till 20 000 begäranden per sekund och vi rekommenderar att du behåller antal indata/utdataåtgärder per sekund (IOPS) över en källdator till 20 000. Till exempel om du har en källdator med 5 diskar och varje disk genererar 120 IOPS (8K storlek) på källdatorn, blir sedan i Azure per disk-IOPS-gränsen på 500. (Antal lagringskonton som krävs är lika med total källdatorn IOPS, dividerat med 20 000.)
**Konfigurationsserver** | Konfigurationsservern bör kunna hantera dagliga ändra frekvensen kapaciteten i alla arbetsbelastningar som körs på skyddade datorer och måste tillräckligt mycket bandbredd för att kontinuerligt replikera data till Azure Storage.<br/><br/> Ett bra tips är att leta upp konfigurationsservern på samma nätverk och LAN-segment som de datorer som du vill skydda. Det kan finnas på ett annat nätverk, men datorer som du vill skydda måste ha 3 nätverk skiktsynlighet till den.<br/><br/> Storleksrekommendationer för konfigurationsservern sammanfattas i tabellen i avsnittet nedan.
**Processervern** | Den första processervern installeras som standard på konfigurationsservern. Du kan distribuera ytterligare processervrar för att skala din miljö. <br/><br/> Processervern tar emot replikeringsdata från skyddade datorer och optimerar dem med cachelagring, komprimering och kryptering. Sedan skickar den data till Azure. Process server-datorn ha tillräckligt med resurser för att utföra dessa uppgifter.<br/><br/> Processervern använder ett diskbaserad cacheminne. Använd en separat cachedisk 600 GB eller mer för att hantera dataändringar som lagras i händelse av en flaskhalsar i nätverket eller avbrott.

## <a name="size-recommendations-for-the-configuration-serverin-built-process-server"></a>Storleksrekommendationer för konfigurationsservern för server/inbyggd process

Varje konfigurationsservern distribueras via [OVF-mall](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) har en inbyggd processerver. Resurser för konfigurationsservern, t.ex. CPU, minne, ledigt utrymme används med en annan hastighet när inbyggd processerver som används för att skydda virtuella datorer. Kraven varierar därför när inbyggd processerver används.
En konfigurationsserver där en inbyggd processerver används för att skydda arbetsbelastning kan hantera upp till 200 virtuella datorer baserat på följande konfigurationer

**CPU** | **Minne** | **Cachestorleken för disk** | **Dataändringshastigheten** | **Skyddade datorer**
--- | --- | --- | --- | ---
8 virtuella processorer (2 platser * 4 kärnor \@ 2,5 gigahertz (GHz)) | 16 GB | 300 GB | 500 GB eller mindre | Replikera färre än 100 virtuella datorer.
12 virtuella processorer (2 platser * 6 kärnor \@ 2,5 GHz) | 18 GB | 600 GB | 500 GB till 1 TB | Replikera mellan 100-150 datorer.
16 virtuella processorer (2 platser * 8 kärnor \@ 2,5 GHz) | 32 GB | 1 TB | 1 TB till 2 TB | Replikera mellan 150 – 200 datorer.
Distribuera en annan konfigurationsserver via [OVF-mall](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) | | | | Distribuera nya konfigurationsservern om du replikerar mer än 200 datorer.
Distribuera en annan [processervern](vmware-azure-set-up-process-server-scale.md#download-installation-file) | | | &GT; 2 TB| Distribuera nya skalbara processerver om den totala dagliga förändringstakten för data överskrider 2 TB.

Där:

* Varje källdatorn har konfigurerats med 3 diskar på 100 GB vardera.
* Vi använde benchmarking lagring av 8 SAS-enheter på 10 K RPM, med RAID 10 för mätningar av cache-disk.

## <a name="size-recommendations-for-the-configuration-server"></a>Storleksrekommendationer för konfigurationsservern

När du inte planerar att använda konfigurationsservern som en processerver, följer du den nedan får du konfigurationen för att hantera upp till 650 virtuella datorer.

**CPU** | **RAM-MINNE** | **OS-diskstorlekar på** | **Dataändringshastigheten** | **Skyddade datorer**
--- | --- | --- | --- | ---
24 virtuella processorer (2 platser * 12 kärnor \@ 2,5 gigahertz (GHz))| 32GB | 80 GB | Inte tillämpligt | Upp till 650 virtuella datorer

Om varje källdatorn har konfigurerats med 3 diskar på 100 GB vardera.

Sedan, process server-funktioner används inte, dataändringshastigheten är inte tillämplig. Om du vill behålla ovan kapacitet du kan växla din arbetsbelastning från inbyggd processerver till en annan skalbara process genom att följa riktlinjerna [här](vmware-azure-manage-process-server.md#balance-the-load-on-process-server).

## <a name="size-recommendations-for-the-process-server"></a>Storleksrekommendationer för processervern

Processervern är den komponent som hanterar replikeringen data i Azure Site Recovery. Om den dagliga förändringstakten är större än 2 TB, som du behöver lägga till en skalbar processerver för att hantera belastningen för replikering. Om du vill skala ut kan du:

* Öka antalet konfigurationsservrar genom att distribuera via [OVF-mall](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). Du kan skydda upp till 400 datorer med två konfigurationsservrar.
* Lägg till [skala ut processervrar](vmware-azure-set-up-process-server-scale.md#download-installation-file), och använda dessa för att hantera replikeringstrafik i stället för (eller förutom) konfigurationsservern.

I följande tabell beskrivs ett scenario där:

* Du har konfigurerat en skalbar processerver.
* Du har konfigurerat den skyddade virtuella datorer för att använda skalbara processerver.
* Varje skyddad källdatorn är konfigurerad med tre diskar på 100 GB vardera.

**Kompletterande processervern** | **Cachestorleken för disk** | **Dataändringshastigheten** | **Skyddade datorer**
--- | --- | --- | ---
4 virtuella processorer (2 platser * 2 kärnor \@ 2,5 GHz), 8 GB minne | 300 GB | 250 GB eller mindre | Replikera 85 eller färre datorer.
8 virtuella processorer (2 platser * 4 kärnor \@ 2,5 GHz), 12 GB minne | 600 GB | 250 GB till 1 TB | Replikera mellan 85 150 datorer.
12 virtuella processorer (2 platser * 6 kärnor \@ 2,5 GHz) 24 GB minne | 1 TB | 1 TB till 2 TB | Replikera mellan 150 225 datorer.

Hur där du skalar dina servrar beror på dina inställningar för en modell med skala upp eller skala ut.  Du skala upp genom att distribuera ett par avancerade konfigurationen och processervrar eller skala ut genom att distribuera fler servrar med färre resurser. Till exempel om du vill skydda 200 datorer med förändringstakten för övergripande data varje dag vid 1,5 TB kan göra du något av följande:

* Konfigurera enda processerver med 16 vCPU, 24 GB RAM-minne.
* Ställa in två processervrar (2 x 8 vCPU, 2 * 12 GB RAM-minne).

## <a name="control-network-bandwidth"></a>Kontrollera nätverksbandbredd

När du har använt den [planeringsverktyget](site-recovery-deployment-planner.md) för att beräkna den bandbredd som du behöver för replikering (inledande replikering och sedan delta), kan du styra mängden bandbredd som används för replikering med hjälp av ett par alternativ:

* **Begränsa bandbredden**: VMware-trafik som replikeras till Azure går igenom en särskild processerver. Du kan begränsa bandbredden på de datorer som körs som processervrar.
* **Påverka bandbredd**: Du kan påverka den bandbredd som används för replikering med hjälp av några registernycklar:
  * Den **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** registervärdet anger antalet trådar som används för att överföra data (inledande replikering eller delta) för en disk. Ett högre värde ökar nätverksbandbredden som används för replikering.
  * Den **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** anger antalet trådar som används för dataöverföring vid återställning efter fel.

### <a name="throttle-bandwidth"></a>Begränsa bandbredden

1. Öppna snapin-modulen för Azure Backup MMC på den datorn som fungerar som processervern. Som standard är en genväg till Backup på skrivbordet eller i följande mapp: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. Klicka på **Ändra egenskaper** i snapin-modulen.

    ![Skärmbild av Azure Backup MMC snapin-alternativet för att ändra egenskaper](./media/site-recovery-vmware-to-azure/throttle1.png)
3. På den **begränsning** fliken **aktivera Användningsbegränsning för internetbandbredd för säkerhetskopieringsåtgärder**. Ange begränsningarna för arbetstid och icke-tid. Giltiga intervall är från 512 kbit/s till 1023 Mbit/s per sekund.

    ![Skärmbild av Azure Backup egenskapsdialogrutan](./media/site-recovery-vmware-to-azure/throttle2.png)

Du kan också ange begränsningar med hjälp av cmdleten [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx). Här är ett exempel:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** anger att ingen begränsning krävs.

### <a name="influence-network-bandwidth-for-a-vm"></a>Påverka nätverkets bandbredd för en virtuell dator

1. I den Virtuella datorns register, går du till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Om du vill påverka bandbreddstrafiken på en replikeringsdisk, ändrar du värdet för **UploadThreadsPerVM**, eller skapa nyckeln om det inte finns.
   * Om du vill påverka bandbredden för redundanstrafik från Azure, ändrar du värdet för **DownloadThreadsPerVM**.
2. Standardvärdet är 4. I ett ”överetablerat” nätverk bör du ändra registernycklarnas standardvärden. Det högsta antalet är 32. Övervaka trafiken för att optimera värdet.

## <a name="setup-azure-site-recovery-infrastructure-to-protect-more-than-500-virtual-machines"></a>Konfigurera Azure Site Recovery-infrastruktur för att skydda fler än 500 virtuella datorer

Innan du konfigurerar av Azure Site Recovery-infrastruktur du behöver komma åt miljön för att mäta följande faktorer: kompatibla virtuella datorer, information om daglig ändra kurs nätverksbandbredd som krävs för rpo-MÅLET, antal Azure site recovery komponenter som krävs, åtgången tid för att slutföra den inledande replikeringen osv.,

1. För att mäta dessa parametrar, se till att köra Distributionshanteraren på din miljö med hjälp av riktlinjerna som delas [här](site-recovery-deployment-planner.md).
2. Distribuera en konfigurationsserver med krav som nämns [här](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server). Om din produktionsarbetsbelastning överskrider 650 virtuella datorer kan du distribuera en annan konfigurationsserver.
3. Baserat på de uppmätta dagliga dataändringshastigheten, distribuera [skala ut processervrar](vmware-azure-set-up-process-server-scale.md#download-installation-file) med hjälp av riktlinjerna för storlek anges [här](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).
4. Om du förväntar dig dataändringshastighet för en disk virtuell dator skulle överskrida 2 Mbit/s, se till att [ställa in ett premiumlagringskonto](tutorial-prepare-azure.md#create-a-storage-account). Eftersom kapacitetsplaneraren körs för en viss tidsperiod, ändra toppar i data rate under andra tiden inte kanske kan avbildas punkter i rapporten.
5. Enligt önskat RPO [ange nätverksbandbredden](site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
6. Efter installationen av infrastrukturen, följer du instruktionerna som publiceras under [How-to avsnittet](vmware-azure-set-up-source.md) att aktivera katastrofåterställning på din arbetsbelastning.

## <a name="deploy-additional-process-servers"></a>Distribuera ytterligare processervrar

Om du har för att skala ut din distribution utöver 200 källdatorer eller har du totalt dagligen dataomsättningsfrekvensen på mer än 2 TB, du behöver ytterligare processervrar att hantera trafik. Följ instruktionerna på [i den här artikeln](vmware-azure-set-up-process-server-scale.md) att ställa in processervern. När du har installerat servern, kan du migrera källdatorer för att använda den.

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrera datorer om du vill använda den nya processervern

1. I **inställningar** > **Site Recovery-servrar**, klickar du på konfigurationsservern och expandera sedan **bearbeta servrar**.

    ![Skärmbild av Processervern dialogrutan](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Högerklicka på processervern som används och på **växel**.

    ![Skärmbild av Configuration server-dialogrutan](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. I **Välj målprocesserver**, Välj den nya för processervern som du vill använda och välj sedan de virtuella datorerna som servern ska hantera. Klicka på informationsikonen för att få information om servern. Som hjälper dig att läsa in beslut, visas det genomsnittliga utrymme som behövs för att replikera alla valda virtuella datorn till den nya processervern. Klicka på kryssmarkeringen för att börja replikera till den nya processervern.

## <a name="deploy-additional-master-target-servers"></a>Distribuera ytterligare huvudmålservrar

Du behöver ytterligare huvudmålservern under de följande scenarierna

1. Om du vill skydda en Linux-baserad virtuell dator.
2. Om huvudmålservern på konfigurationsservern inte har åtkomst till databasen för virtuell dator.
3. Om det totala antalet diskar på master målserver (inte. lokala diskar på servern) + diskar som ska skyddas är fler än 60 diskar.

Att lägga till en ny huvudmålserver för **Linux-baserad virtuell dator**, [Klicka här](vmware-azure-install-linux-master-target.md).

För **Windows-baserad virtuell dator**, följer du de instruktioner nedan.

1. Gå till **Recovery Services-valv** > **Site Recovery-infrastruktur** > **konfigurationsservrar**.
2. Klicka på nödvändiga configuration server > **+ Huvudmålservern**.![ Lägg till-master-target-server.png](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Hämta enhetlig konfiguration och kör den på den virtuella datorn för att konfigurera huvudmålservern.
4. Välj **installera huvudmålservern** > **nästa**. ![Välj MT.PNG](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Välj standardplatsen för installation > klickar du på **installera**. ![MT-installationen](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Klicka på **fortsätta konfigurationen** att registrera huvudmålservern med konfigurationsservern. ![MT fortsätta configuration.PNG](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Ange IP-adressen för konfigurationsservern & lösenfras. [Klicka här](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) och lär dig att generera lösenfras.![ CS-ip-lösenfras](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Klicka på **registrera** och efter registreringen på **Slutför**.
9. Vid lyckad registrering, den här servern visas på portalen under **Recovery Services-valv** > **Site Recovery-infrastruktur** > **konfiguration servrar** > master-målservrar av relevanta konfigurationsservern.

 >[!NOTE]
 >Du kan också hämta den senaste versionen av Master target-serverinstallation enhetlig för Windows [här](https://aka.ms/latestmobsvc).

## <a name="next-steps"></a>Nästa steg

Ladda ned och kör den [Distributionshanteraren för Azure Site Recovery](https://aka.ms/asr-deployment-planner)
