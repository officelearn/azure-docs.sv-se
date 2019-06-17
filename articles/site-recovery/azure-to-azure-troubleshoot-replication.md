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
ms.openlocfilehash: bf24b2d1395e128dc73361670ea93ac938574146
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258787"
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

* **Undanta en disk som orsakar en hög dataändringshastighet**: Du kan undanta en disk med hjälp av [PowerShell](./azure-to-azure-exclude-disks.md). Du måste inaktivera replikering först om du vill undanta en disk. 
* **Ändra nivå för disaster recovery Lagringsdisken**: Det här alternativet är bara möjlig om dataomsättningen för disken är mindre än 10 MB/s. Vi antar att en virtuell dator med en P10-disk som har en dataomsättning som är större än 8 MB/s men mindre än 10 MB/s. Om kunden kan använda en P30-disk för mållagring vid skydd, kan problemet lösas.

## <a name="Network-connectivity-problem"></a>Problem med nätverksanslutningen

### <a name="network-latency-to-a-cache-storage-account"></a>Svarstid för nätverk till ett cachelagringskonto
Site Recovery skickar replikerade data till cachelagringskontot. Du kan se Nätverksfördröjningen om överför data från en virtuell dator till cachelagringskontot är långsammare än 4 MB i 3 sekunder. 

Om du vill söka efter ett problem som rör svarstid, använda [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) att överföra data från den virtuella datorn till cachelagringskontot. Om svarstiden är hög, kontrollerar du om du använder en virtuell nätverksinstallation (NVA) för att styra utgående nätverkstrafik från virtuella datorer. Installationen kan begränsas om all replikeringstrafik som passerar genom NVA. 

Vi rekommenderar att du skapar en tjänstslutpunkt för ett nätverk i ditt virtuella nätverk för ”Storage” så att replikeringstrafiken inte gå till NVA. Mer information finns i [nätverkskonfiguration för virtuell installation](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration).

### <a name="network-connectivity"></a>Nätverksanslutning
För Site Recovery-replikering till arbete, utgående anslutning till specifika URL: er eller IP-intervall krävs från den virtuella datorn. Om den virtuella datorn finns bakom en brandvägg eller använder regler för nätverkssäkerhetsgrupper (NSG) för att styra utgående anslutningar, kan du står inför ett av de här problemen. Om du vill kontrollera att alla URL: er är anslutna, se [utgående anslutning för Site Recovery-webbadresser](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Fel-ID 153006 – ingen appkonsekvent återställningspunkt är tillgänglig för den virtuella datorn under de senaste ”XXX” minuterna

Vissa av de vanligaste problemen visas nedan

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Orsak 1: Kända problem i SQLServer 2008/2008 R2 
**Hur du löser** : Det finns ett känt problem med SQLServer 2008/2008 R2. Se den här KB-artikeln [Azure Site Recovery Agent eller andra icke-komponenten VSS säkerhetskopiering misslyckas för en server som är värd för SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-autoclose-dbs"></a>Orsak 2: Azure Site Recovery-jobb misslyckas på servrar som är värd för någon version av SQL Server-instanser med AUTO_CLOSE databaser 
**Hur du löser** : Se Kb [artikel](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Orsak 3: Kända problem i SQL Server 2016 och 2017
**Hur du löser** : Se Kb [artikel](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>Orsak 4: Du använder Storage spaces direct-konfiguration
**Hur du löser** : Azure Site Recovery kan inte skapa programkonsekvent återställningspunkt för Storage spaces direct-konfiguration. Se artikeln för att korrekt [konfigurera replikeringsprincipen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)

### <a name="more-causes-due-to-vss-related-issues"></a>Fler orsaker hittar på grund av VSS-relaterade problem:

Felsök ytterligare genom att kontrollera filerna på källdatorn för att hämta den exakta felkoden för fel:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Så här söker du upp felen i filen?
Sök efter strängen ”vacpError” genom att öppna filen vacp.log i en textredigerare
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

I exemplet ovan **2147754994** är den felkod som meddelar dig om felet som visas nedan

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS-skrivaren har inte installerats - fel 2147221164 

*Hur du löser*: Azure Site Recovery använder för att generera konsekvenstagg för programmet Microsoft Volume Shadow copy Service (VSS). En VSS-providern för dess drift för att ta ögonblicksbilder av app konsekvens installeras. Den här VSS-providern har installerats som en tjänst. Om VSS-Provider-tjänsten inte är installerad, misslyckas programmet konsekvens ögonblicksbild med felet id 0x80040154 ”klassen har inte registrerats”. </br>
Se [artikeln för felsökning av VSS writer installation](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS-skrivaren är inaktiverad - fel 2147943458

**Hur du löser**: Azure Site Recovery använder för att generera konsekvenstagg för programmet Microsoft Volume Shadow copy Service (VSS). En VSS-providern för dess drift för att ta ögonblicksbilder av app konsekvens installeras. Den här VSS-providern har installerats som en tjänst. Om tjänsten VSS-Provider har inaktiverats kan misslyckas programmet konsekvent ögonblicksbild med fel-id ”den angivna tjänsten är inaktiverad och kan inte vara started(0x80070422)”. </br>

- Om VSS är inaktiverad
    - Kontrollera att starttypen för tjänsten VSS-Provider har angetts till **automatisk**.
    - Starta om följande tjänster:
        - VSS-tjänsten
        - Azure Site Recovery VSS Provider
        - VDS-tjänsten

####  <a name="vss-provider-notregistered---error-2147754756"></a>VSS-PROVIDERN NOT_REGISTERED - fel 2147754756

**Hur du löser**: Azure Site Recovery använder för att generera konsekvenstagg för programmet Microsoft Volume Shadow copy Service (VSS). Kontrollera om tjänsten Azure Site Recovery VSS Provider är installerat eller inte. </br>

- Gör om Provider-installationen med hjälp av följande kommandon:
- Avinstallera befintliga providern: C:\Program filer (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Installera om: C:\Program filer (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Kontrollera att starttypen för tjänsten VSS-Provider har angetts till **automatisk**.
    - Starta om följande tjänster:
        - VSS-tjänsten
        - Azure Site Recovery VSS Provider
        - VDS-tjänsten
