---
title: "Planera kapacitet och skalning för fysisk serverreplikering till Azure med Azure Site Recovery | Microsoft Docs"
description: "Använd den här artikeln för att planera kapaciteten och skala vid replikering av Windows-/ Linux fysiska servrar till Azure med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 554f59ee-0b49-4779-9737-90cb601ef6fe
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: rayne
ms.openlocfilehash: 971ad6dd39f94aa7944f6ed3b31bc3acc605d9a7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="step-3-plan-capacity-and-scaling-for-physical-server-to-azure-replication"></a>Steg 3: Planera kapacitet och skalning för fysisk server till Azure-replikering

Använd den här artikeln för att ta reda på kapaciteten och skalning, när du replikera lokala Windows-/ Linux fysiska servrar till Azure med [Azure Site Recovery](site-recovery-overview.md).

Publicera kommentarer och frågor längst ned i den här artikeln eller i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="plan-deployment-capacity"></a>Planera kapacitet för distribution

1. Läs [artikel](site-recovery-plan-capacity-vmware.md) att lära dig om att uppskatta replikeringskrav och riktlinjer för att ändra storlek på Site Recovery-komponenter.
2. Läs nedan för att lära dig om att skala komponentservrarna överväganden och kontrollerar replikerade datorn bandbredd.

## <a name="replication-considerations"></a>Överväganden för datareplikering

Använd dessa överväganden för att ta reda på replikerade server-krav.

**Komponent** | **Detaljer** 
--- | --- 
**Replikering** | **Maximal dagliga förändringstakten:** en skyddad dator kan bara använda en processerver och en enda process-server kan hantera ett dagligt ändra Betygsätt upp till 2 TB. Därför är 2 TB maximala dagliga data ändra hastigheten som stöds för en skyddad dator.<br/><br/> **Maximalt dataflöde:** en replikerad dator kan tillhöra ett lagringskonto i Azure. Ett standardlagringskonto kan hantera upp till 20 000 begäranden per sekund och vi rekommenderar att du behåller antalet i/o-åtgärder per sekund (IOPS) över en källdator till 20 000. Till exempel om du har en källdator med 5 diskar och varje disk genererar 120 IOPS (om 8K storlek) på källdatorn, blir sedan i Azure per disk IOPS högst 500. (Antal storage-konton som krävs är lika med totala källdatorn IOPS, dividerat med 20 000.)

## <a name="configuration-server-capacity"></a>Konfiguration av serverkapacitet

Konfigurationsservern bör kunna hantera dagliga ändra frekvensen kapacitet över alla arbetsbelastningar som körs på skyddade datorer och måste tillräckligt med bandbredd för att kontinuerligt replikera data till Azure Storage.

Ett bra tips är att hitta konfigurationsservern på samma nätverk och LAN-segment som de datorer som du vill skydda. Det kan finnas i ett annat nätverk, men datorer som du vill skydda måste ha synlighet för lager 3 nätverket till den.

## <a name="sizing-recommendations"></a>Ändra storlek på rekommendationer

Tabellen sammanfattar sizing rekommendationer baserat på CPU.

**CPU** | **Minne** | **Cachestorleken för disk** | **Dataändringshastighet** | **Skyddade datorer**
--- | --- | --- | --- | ---
8 vCPUs (2 sockets * 4 kärnor @ 2,5 gigahertz (GHz)) | 16 GB | 300 GB | 500 GB eller mindre | Replikera färre än 100 datorer.
12 vCPUs (2 sockets * @ 2,5 GHz-6 kärnor) | 18 GB | 600 GB | 500 GB till 1 TB | Replikera mellan 100 150 datorer.
16 vCPUs (2 sockets * 8 kärnor @ 2,5 GHz) | 32 GB | 1 TB | 1 TB till 2 TB | Replikera mellan 150 200 datorer.
Distribuera en annan processerver | | | > 2 TB | Distribuera ytterligare servrar om du replikerar mer än 200 datorer eller ändrar dagliga data frekvensen överskrider 2 TB.

Där:

* Varje källdatorn har konfigurerats med 3 diskar på 100 GB vardera.
* Vi använde benchmarking lagring av 8 SAS-enheter på 10 K RPM, med RAID 10 för cache disk mått.

## <a name="process-server-capacity"></a>Bearbeta serverkapacitet


Processervern tar emot replikeringsdata från skyddade datorer och optimerar dem med cachelagring, komprimering och kryptering. Sedan skickar den data till Azure.

- Process-serverdatorn bör ha tillräckligt med resurser för att utföra dessa uppgifter.
- Den första processervern installeras som standard på konfigurationsservern. Du kan distribuera ytterligare servrar för att skala din miljö.
- Processervern använder ett diskbaserad cacheminne. Använd en separat cache 600 GB eller mer för att hantera dataändringarna lagras om ett flaskhalsar i nätverket eller avbrott.
- Om du behöver skydda fler än 200 datorer eller dagliga förändringstakten är större än 2 TB, kan du lägga till servrar för att hantera replikering belastningen. Om du vill skala ut kan du:
    - Öka antalet configuration-servrar. Du kan skydda upp till 400 datorer med två konfigurationsservrar.
    - Lägga till flera servrar och använda dessa för att hantera nätverkstrafik i stället för (eller förutom) konfigurationsservern.


### <a name="example-process-server-scaling"></a>Exempel processen serverskalning

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

## <a name="deploy-additional-process-servers"></a>Distribuera ytterligare servrar

1. Följ [instruktionerna](site-recovery-vmware-setup-azure-ps-resource-manager.md) att ställa in en ytterligare processervern.
2. Om du inte har lösenfrasen kör **[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe – n** på konfigurationsservern att hämta den.
3. När du har installerat processervern, kan du migrera källdatorer för att använda den.

    1. I **inställningar** > **Site Recovery servrar**, klicka på konfigurationsservern > **bearbeta servrar**.
    2. Högerklicka på processervern används för tillfället > **växel**.
    3. I **Välj målprocesserver**, väljer du vill använda processervern och väljer de virtuella datorerna som hanterar servern.
    4. Klicka på informationsikonen. För att hjälpa dig att fatta beslut om att läsa in visas den genomsnittliga utrymme som behövs för att replikera alla valda virtuella datorn till den nya processervern.
    5. Klicka på kryssmarkeringen för att börja replikering till den nya processervern.

## <a name="control-network-bandwidth"></a>Kontrollera nätverksbandbredd

När du har kört [verktyget distribution Planner](site-recovery-deployment-planner.md) för att beräkna den bandbredd som du behöver för replikering (inledande replikering och sedan delta) du kan styra hur mycket bandbredd som används för replikering med flera olika alternativ:

* **Begränsa bandbredden**: VMware-trafik som replikeras till Azure går igenom en specifik process-server. Du kan begränsa bandbredden på de datorer som körs som servrar.
* **Påverka bandbredd**: du kan påverka den bandbredd som används för replikering med hjälp av några registernycklar:
  * Den **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** registervärdet anger antalet trådar som används för att överföra data (inledande replikering eller delta) för en disk. Ett högre värde ökar nätverksbandbredden som används för replikering.
  * Den **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM** anger antalet trådar som används för att överföra data under återställning efter fel.

### <a name="throttle-bandwidth"></a>Begränsa bandbredden

1. Öppna snapin-modulen för Azure Backup MMC på datorn som fungerar som processervern. Som standard en genväg för säkerhetskopiering är tillgänglig på skrivbordet eller i följande mapp: C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Klicka på **Ändra egenskaper** i snapin-modulen.
3. På den **begränsning** väljer **aktivera Användningsbegränsning för internetbandbredd för säkerhetskopieringsåtgärder**.
4. Ange begränsningar för arbete och fritid timmar. Giltiga intervall är från 512 kbit/s till 102 Mbit/s.

    ![Begränsning](./media/physical-walkthrough-capacity/throttle2.png)

Du kan också ange begränsningar med hjälp av cmdleten [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx). Här är ett exempel:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** anger att ingen begränsning krävs.

### <a name="influence-network-bandwidth-for-a-vm"></a>Påverka nätverkets bandbredd för en virtuell dator

1. I registret på den virtuella datorn går du till **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Om du vill påverka bandbredd trafiken på en replikering disk ändrar du värdet för **UploadThreadsPerVM**, eller skapa nyckeln om den inte finns.
   * Om du vill påverka bandbredden för redundanstrafik från Azure ändrar du värdet för **DownloadThreadsPerVM**.
2. Standardvärdet är 4. I en överetablerad nätverk ska registernycklarna ändras. Det högsta antalet är 32. Övervaka trafiken för att optimera värdet.




## <a name="next-steps"></a>Nästa steg

Gå till [steg 4: Planera nätverk](physical-walkthrough-network.md).
