---
title: Azure Site Recovery-felsökning för pågående problem med replikering från Azure till Azure | Microsoft Docs
description: Felsöka problem och fel vid replikering av virtuella datorer i Azure för haveriberedskap
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9ff756270c368d39b7ef78d7c1046f7c91169668
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56299607"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Felsökning av pågående problem i Azure till Azure VM-replikering

Den här artikeln beskrivs vanliga problem i Azure Site Recovery när du replikera och återställa virtuella Azure-datorer från en region till en annan region. Den förklarar också hur du felsöker dem. Mer information om konfigurationer som stöds finns i den [stöd matrix för att replikera virtuella Azure-datorer](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery replikerar data från regionen som källa till regionen disaster recovery konsekvent och skapar en kraschkonsekvent återställningspunkt var femte minut. Om Site Recovery inte kan skapa återställningspunkter i 60 minuter, varnar dig med den här informationen:

Felmeddelande: ”Inga kraschkonsekventa återställningspunkten är tillgängliga för den virtuella datorn under de senaste 60 minuterna”.</br>
Fel-ID: 153007 </br>

I följande avsnitt beskrivs orsaker och lösningar.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Hög dataändringshastigheten på den virtuella källdatorn
Azure Site Recovery utlöser en händelse om förändringstakten för data på den virtuella källdatorn är högre än gränsvärden som stöds. Du kan kontrollera om problemet är på grund av hög omsättning, välja **replikerade objekt** > **VM** > **händelser – senaste 72 timmarna**.
Du bör se händelsen ”dataändringshastighet utöver de gränser som stöds”:

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Om du väljer händelsen, bör du se exakt diskinformationen:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Gränser för Azure Site Recovery
Följande tabell innehåller gränserna för Azure Site Recovery. Dessa gränser är baserade på våra tester, men de täcker inte alla möjliga i/o kombinationer av program. De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program. 

Det finns två gränser att tänka på, dataomsättningen per disk och dataomsättningen per virtuell dator. Till exempel du nu ska vi titta på Premium P20-disk i följande tabell. Site Recovery kan hantera 5 MB/s om dataomsättningen per disk med högst fem dessa diskar per virtuell dator, på grund av att gränsen på 25 MB/s om den totala dataomsättningen per virtuell dator.

**Replication Storage Target** (Lagringsmål för replikering) | **Genomsnittlig i/o-storlek för källdisk** |**Genomsnittlig dataomsättning för källdisk** | **Total dataomsättning per dag för källdisk data**
---|---|---|---
Standard Storage | 8 kB | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB  | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |  336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |10 MB/s | 842 GB per disk

### <a name="solution"></a>Lösning
Azure Site Recovery har en gräns på förändringstakten för data, beroende på vilken typ av disk. Om du vill veta om det här problemet är återkommande eller tillfälligt, hitta dataändringshastighet på den berörda virtuella datorn. Gå till den virtuella källdatorn, hitta mått under **övervakning**, och Lägg till mått som visas i den här skärmbilden:

![Process i tre steg för att hitta förändringstakten för data](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Välj **Lägg till mått**, och Lägg till **OS Disk-skrivna byte/s** och **Data Disk-skrivna byte/s**.
2. Övervaka insamling som visas i skärmbilden.
3. Visa det totala antalet skrivåtgärder som händer i OS-diskar och alla datadiskar kombineras. De här måtten kanske inte att ge dig information på nivån per disk, men de visar den totala mönstret på dataomsättning.

Om en topp är från en databearbetning burst och data ändrar frekvensen är större än 10 MB/s (för Premium) och 2 MB/s (för Standard) för vissa tid och handlar om, replikering ska komma ifatt. Men om omsättningen är långt utöver stöds begränsa de flesta fall bör du överväga att något av följande alternativ om möjligt:

* **Undanta en disk som orsakar en hög dataändringshastighet**: Du kan undanta en disk med hjälp av [PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine).
* **Ändra nivå för disaster recovery Lagringsdisken**: Det här alternativet är bara möjlig om dataomsättningen för disken är mindre än 10 MB/s. Vi antar att en virtuell dator med en P10-disk som har en dataomsättning som är större än 8 MB/s men mindre än 10 MB/s. Om kunden kan använda en P30-disk för mållagring vid skydd, kan problemet lösas.

## <a name="Network-connectivity-problem"></a>Problem med nätverksanslutningen

### <a name="network-latency-to-a-cache-storage-account"></a>Svarstid för nätverk till ett cachelagringskonto
Site Recovery skickar replikerade data till cachelagringskontot. Du kan se Nätverksfördröjningen om överför data från en virtuell dator till cachelagringskontot är långsammare än 4 MB i 3 sekunder. 

Om du vill söka efter ett problem som rör svarstid, använda [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) att överföra data från den virtuella datorn till cachelagringskontot. Om svarstiden är hög, kontrollerar du om du använder en virtuell nätverksinstallation (NVA) för att styra utgående nätverkstrafik från virtuella datorer. Installationen kan begränsas om all replikeringstrafik som passerar genom NVA. 

Vi rekommenderar att du skapar en tjänstslutpunkt för ett nätverk i ditt virtuella nätverk för ”Storage” så att replikeringstrafiken inte gå till NVA. Mer information finns i [nätverkskonfiguration för virtuell installation](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Nätverksanslutning
För Site Recovery-replikering till arbete, utgående anslutning till specifika URL: er eller IP-intervall krävs från den virtuella datorn. Om den virtuella datorn finns bakom en brandvägg eller använder regler för nätverkssäkerhetsgrupper (NSG) för att styra utgående anslutningar, kan du står inför ett av de här problemen. Om du vill kontrollera att alla URL: er är anslutna, se [utgående anslutning för Site Recovery-webbadresser](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 