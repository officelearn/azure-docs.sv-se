---
title: Planera kapacitet och skalning för VMware-replikering till Azure med Azure Site Recovery | Microsoft Docs
description: Använd den här artikeln för att planera kapaciteten och skala vid replikering av virtuella VMware-datorer till Azure med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: rayne
ms.openlocfilehash: 30e4534fbc235a228ac887ddc3336f09909b4fa6
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287362"
---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>Planera kapacitet och skalning för VMware-replikering med Azure Site Recovery

Använd den här artikeln för att räkna ut planera för kapacitet och skalning när replikera lokala virtuella VMware-datorer och fysiska servrar till Azure med [Azure Site Recovery](site-recovery-overview.md).

## <a name="how-do-i-start-capacity-planning"></a>Hur börjar jag kapacitetsplanering?

Samla in information om replikeringsmiljön genom att köra den [Azure Site Recovery-distribution Planner](https://aka.ms/asr-deployment-planner-doc) för VMware-replikering. [Läs mer](site-recovery-deployment-planner.md) om det här verktyget. Du kan samla in information om kompatibla och inkompatibla virtuella datorer, diskar per virtuell dator och data omsättningsuppdateringar per disk. Verktyget kontrollerar även kraven på nätverksbandbredd och vilken Azure-infrastruktur som krävs för en lyckad replikering och testning av redundans.

## <a name="capacity-considerations"></a>Överväganden för kapacitetsplanering

**Komponent** | **Detaljer** |
--- | --- | ---
**Replikering** | **Maximal dagliga förändringstakten:** en skyddad dator kan bara använda en processerver och en enda process-server kan hantera ett dagligt ändra Betygsätt upp till 2 TB. Därför är 2 TB maximala dagliga data ändra hastigheten som stöds för en skyddad dator.<br/><br/> **Maximalt dataflöde:** en replikerad dator kan tillhöra ett lagringskonto i Azure. Ett standardlagringskonto kan hantera upp till 20 000 begäranden per sekund och vi rekommenderar att du behåller antalet i/o-åtgärder per sekund (IOPS) över en källdator till 20 000. Till exempel om du har en källdator med 5 diskar och varje disk genererar 120 IOPS (om 8K storlek) på källdatorn, blir sedan i Azure per disk IOPS högst 500. (Antal storage-konton som krävs är lika med totala källdatorn IOPS, dividerat med 20 000.)
**Konfigurationsserver** | Konfigurationsservern bör kunna hantera dagliga ändra frekvensen kapacitet över alla arbetsbelastningar som körs på skyddade datorer och måste tillräckligt med bandbredd för att kontinuerligt replikera data till Azure Storage.<br/><br/> Ett bra tips är att hitta konfigurationsservern på samma nätverk och LAN-segment som de datorer som du vill skydda. Det kan finnas i ett annat nätverk, men datorer som du vill skydda måste ha synlighet för lager 3 nätverket till den.<br/><br/> Storlek rekommendationer för konfigurationsservern sammanfattas i tabellen nedan.
**Processervern** | Den första processervern installeras som standard på konfigurationsservern. Du kan distribuera ytterligare servrar för att skala din miljö. <br/><br/> Processervern tar emot replikeringsdata från skyddade datorer och optimerar dem med cachelagring, komprimering och kryptering. Sedan skickar den data till Azure. Process-serverdatorn bör ha tillräckligt med resurser för att utföra dessa uppgifter.<br/><br/> Processervern använder ett diskbaserad cacheminne. Använd en separat cache 600 GB eller mer för att hantera dataändringarna lagras om ett flaskhalsar i nätverket eller avbrott.

## <a name="size-recommendations-for-the-configuration-server"></a>Storlek rekommendationer för konfigurationsservern

**CPU** | **Minne** | **Cachestorleken för disk** | **Dataändringshastighet** | **Skyddade datorer**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 kärnor @ 2,5 gigahertz (GHz)) | 16 GB | 300 GB | 500 GB eller mindre | Replikera färre än 100 datorer.
12 vCPUs (2 sockets * @ 2,5 GHz-6 kärnor) | 18 GB | 600 GB | 500 GB till 1 TB | Replikera mellan 100 150 datorer.
16 vCPUs (2 sockets * 8 kärnor @ 2,5 GHz) | 32 GB | 1 TB | 1 TB till 2 TB | Replikera mellan 150 200 datorer.
Distribuera en annan processerver | | | &GT; 2 TB | Distribuera ytterligare servrar om du replikerar mer än 200 datorer eller ändrar dagliga data frekvensen överskrider 2 TB.

Där:

* Varje källdatorn har konfigurerats med 3 diskar på 100 GB vardera.
* Vi använde benchmarking lagring av 8 SAS-enheter på 10 K RPM, med RAID 10 för cache disk mått.

## <a name="size-recommendations-for-the-process-server"></a>Storlek rekommendationer för processervern

Om du behöver skydda fler än 200 datorer eller dagliga förändringstakten är större än 2 TB, kan du lägga till servrar för att hantera replikering belastningen. Om du vill skala ut kan du:

* Öka antalet configuration-servrar. Du kan skydda upp till 400 datorer med två konfigurationsservrar.
* Lägga till flera servrar och använda dessa för att hantera nätverkstrafik i stället för (eller förutom) konfigurationsservern.

Följande tabell beskriver ett scenario där:

* Du planerar inte att använda konfigurationsservern som en processerver.
* Du har konfigurerat en ytterligare processervern.
* Du har konfigurerat den skyddade virtuella datorer om du vill använda ytterligare processervern.
* Varje skyddade källdatorn har konfigurerats med tre diskar på 100 GB vardera.

**Konfigurationsserver** | **Ytterligare processervern** | **Cachestorleken för disk** | **Dataändringshastighet** | **Skyddade datorer**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 kärnor @ 2,5 GHz), 16 GB minne | 4 vCPUs (2 sockets * 2 kärnor @ 2,5 GHz), 8 GB minne | 300 GB | 250 GB eller mindre | Replikera 85 eller färre datorer.
8 vCPUs (2 sockets * 4 kärnor @ 2,5 GHz), 16 GB minne | 8 vCPUs (2 sockets * 4 kärnor @ 2,5 GHz), 12 GB minne | 600 GB | 250 GB till 1 TB | Replikera mellan 85 150 datorer.
12 vCPUs (2 sockets * 6 kärnor @ 2,5 GHz), 18 GB minne | 12 vCPUs (2 sockets * 6 kärnor @ 2,5 GHz) 24 GB minne | 1 TB | 1 TB till 2 TB | Replikera mellan 150 225 datorer.

Hur där du skala dina servrar beror på dina inställningar för en modell skala upp eller skala ut.  Du skala upp genom att distribuera några avancerad konfiguration och servrar eller skala ut genom att distribuera flera servrar med färre resurser. Exempelvis kan du göra något av följande om du behöver skydda 220 datorer:

* Ställ in konfigurationsservern med 12 vCPU, 18 GB minne, och en ytterligare processervern med 12 vCPU, 24 GB minne. Konfigurera skyddade datorer om du vill använda ytterligare processer-servern.
* Ställa in två configuration-servrar (2 x 8 vCPU, 16 GB RAM-minne) och två ytterligare servrar (1 x 8 vCPU och 4 vCPU x 1 att hantera 135 + 85 [220] datorer). Konfigurera skyddade datorer om du vill använda de ytterligare processer servrarna.


## <a name="control-network-bandwidth"></a>Kontrollera nätverksbandbredd

När du har använt den [verktyget distribution Planner](site-recovery-deployment-planner.md) för att beräkna den bandbredd som du behöver för replikering (inledande replikering och sedan delta) du kan styra hur mycket bandbredd som används för replikering med flera olika alternativ:

* **Begränsa bandbredden**: VMware-trafik som replikeras till Azure går igenom en specifik process-server. Du kan begränsa bandbredden på de datorer som körs som servrar.
* **Påverka bandbredd**: du kan påverka den bandbredd som används för replikering med hjälp av några registernycklar:
  * Den **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** registervärdet anger antalet trådar som används för att överföra data (inledande replikering eller delta) för en disk. Ett högre värde ökar nätverksbandbredden som används för replikering.
  * Den **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** anger antalet trådar som används för att överföra data under återställning efter fel.

### <a name="throttle-bandwidth"></a>Begränsa bandbredden

1. Öppna snapin-modulen för Azure Backup MMC på datorn som fungerar som processervern. Som standard en genväg för säkerhetskopiering är tillgänglig på skrivbordet eller i följande mapp: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Klicka på **Ändra egenskaper** i snapin-modulen.

    ![Skärmbild av Azure Backup MMC snapin-modulen alternativet för att ändra egenskaper](./media/site-recovery-vmware-to-azure/throttle1.png)
3. På den **begränsning** väljer **aktivera Användningsbegränsning för internetbandbredd för säkerhetskopieringsåtgärder**. Ange begränsningar för arbete och fritid timmar. Giltigt intervall är från 512 kbit/s och 1023 Mbit/s per sekund.

    ![Skärmbild av Azure Backup egenskapsdialogrutan](./media/site-recovery-vmware-to-azure/throttle2.png)

Du kan också ange begränsningar med hjälp av cmdleten [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx). Här är ett exempel:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** anger att ingen begränsning krävs.

### <a name="influence-network-bandwidth-for-a-vm"></a>Påverka nätverkets bandbredd för en virtuell dator

1. I registret för den virtuella datorn, gå till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Om du vill påverka bandbredd trafiken på en replikering disk ändrar du värdet för **UploadThreadsPerVM**, eller skapa nyckeln om den inte finns.
   * Om du vill påverka bandbredden för redundanstrafik från Azure ändrar du värdet för **DownloadThreadsPerVM**.
2. Standardvärdet är 4. I ett ”överetablerat” nätverk bör du ändra registernycklarnas standardvärden. Det högsta antalet är 32. Övervaka trafiken för att optimera värdet.


## <a name="deploy-additional-process-servers"></a>Distribuera ytterligare servrar

Om du har för att skala ut distributionen utöver 200 källdatorer eller du har totalt dagligen omsättningsuppdateringar av mer än 2 TB, behöver du ytterligare servrar att hantera trafikvolymen. Följ dessa instruktioner för att ställa in processervern. När du har installerat på servern, kan du migrera källdatorer för att använda den.

1. I **Site Recovery servrar**, klicka på konfigurationsservern och klicka sedan på **Processervern**.

    ![Skärmbild av Site Recovery servrar alternativet att lägga till en processerver](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. I **servertyp**, klickar du på **processervern (lokalt)**.

    ![Skärmbild av Processervern dialogrutan](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. Hämta filen Unified installationsprogram för Site Recovery och kör det att installera processervern. Detta även registrerar den i valvet.
4. I **Before you begin** (Innan du börjar) väljer du **Add additional process servers to scale out deployment** (Lägg till ytterligare processervrar till utskalningsdistribution).
5. Slutför guiden på samma sätt som du gjorde när du [konfigurera](#step-2-set-up-the-source-environment) konfigurationsservern.

    ![Skärmbild av Azure Site Recovery Unified installationsguiden](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. I **Server konfigurationsinformation**, ange IP-adress för konfigurationsservern och lösenfrasen. Om du vill hämta lösenfrasen kör **[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe – n** på konfigurationsservern.

    ![Skärmbild av Server konfigurationsinformation sida](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrera datorer om du vill använda den nya processervern
1. I **inställningar** > **Site Recovery servrar**, klicka på konfigurationsservern och expandera sedan **bearbeta servrar**.

    ![Skärmbild av Processervern dialogrutan](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Högerklicka på processervern som används och på **växel**.

    ![Dialogrutan Skärmbild av konfiguration av servern](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. I **Välj målprocesserver**, Välj den nya processervern som du vill använda och välj sedan de virtuella datorerna som hanterar servern. Klicka på informationsikonen för att få information om servern. För att hjälpa dig att fatta beslut om att läsa in visas den genomsnittliga utrymme som behövs för att replikera alla valda virtuella datorn till den nya processervern. Klicka på kryssmarkeringen för att starta replikering till den nya processervern.


## <a name="next-steps"></a>Nästa steg

Hämta och kör den [Azure Site Recovery-distribution Planner](https://aka.ms/asr-deployment-planner)
