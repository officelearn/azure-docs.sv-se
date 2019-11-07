---
title: Felsök kontinuerlig replikering av virtuella Azrue-datorer med Azure Site Recovery
description: Fel sökning av fel och problem vid replikering av virtuella Azure-datorer för katastrof återställning
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.author: asgang
ms.openlocfilehash: 7b9da202704b20e5770343f857c044ea19ae696a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620891"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Felsöka pågående problem i Azure-till-Azure VM-replikering

I den här artikeln beskrivs vanliga problem i Azure Site Recovery när du replikerar och återställer virtuella Azure-datorer från en region till en annan region. Det beskriver också hur du felsöker dem. Mer information om konfigurationer som stöds finns i [support mat ris för replikering av virtuella Azure-datorer](site-recovery-support-matrix-azure-to-azure.md).

Azure Site Recovery replikerar konsekvent data från käll regionen till katastrof återställnings regionen och skapar en kraschad återställnings punkt var 5: e minut. Om Site Recovery inte kan skapa återställnings punkter i 60 minuter, aviseras du med den här informationen:

Fel meddelande: "ingen krasch konsekvens återställnings punkt är tillgänglig för den virtuella datorn under de senaste 60 minuterna."</br>
Fel-ID: 153007 </br>

I följande avsnitt beskrivs orsaker och lösningar.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Hög data ändrings takt på den virtuella käll datorn
Azure Site Recovery utlöser en händelse om data ändrings hastigheten på den virtuella käll datorn är högre än de gränser som stöds. Du kan kontrol lera om problemet beror på hög omsättning genom att gå till **replikerade objekt** > **VM** > **händelser-senaste 72 timmarna**.
Du bör se händelsen "data ändrings takt utöver stödda gränser":

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Om du väljer händelsen bör du se exakt disk information:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Gränser för Azure Site Recovery
Följande tabell innehåller gränserna för Azure Site Recovery. Dessa gränser baseras på våra tester, men de kan inte användas för alla möjliga kombinationer av program I/O. De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program. 

Det finns två gränser att överväga, data omsättning per disk och data omsättning per virtuell dator. Låt oss till exempel titta på Premium P20-disken i följande tabell. Site Recovery kan hantera 5 MB/s av omsättning per disk med högst fem sådana diskar per virtuell dator, på grund av gränsen på 25 MB/s av total omsättning per virtuell dator.

**Replication Storage Target** (Lagringsmål för replikering) | **Genomsnittlig I/O-storlek för käll disk** |**Genomsnittlig data omsättning för käll disk** | **Total data omsättning per dag för käll data disk**
---|---|---|---
Standard Storage | 8 kB | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB  | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |  336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |10 MB/s | 842 GB per disk

### <a name="solution"></a>Lösning
Azure Site Recovery har gränser för dataändringshastighet, baserat på typen av disk. Om du vill veta om det här problemet är återkommande eller snart kan du söka efter data ändrings takten för den berörda virtuella datorn. Gå till den virtuella käll datorn, hitta måtten under **övervakning**och Lägg till måtten som visas på den här skärm bilden:

![Tre stegs process för att hitta data ändrings takten](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Välj **Lägg till mått**och Lägg till **OS disk-skrivna byte/s** och **data disk-skrivna byte/s**.
2. Övervaka insamling som visas i skärm bilden.
3. Visa de totala Skriv åtgärder som sker mellan OS-diskar och alla data diskar tillsammans. Dessa mått kanske inte ger dig information på nivån per disk, men de visar det totala mönstret för data omsättning.

Om en insamling är från en tillfällig data burst och data ändrings takten är större än 10 MB/s (för Premium) och 2 MB/s (för standard) under en viss tid och är i drift, kommer replikeringen att bli infångad. Men om omsättningen är väl bortom den gräns som stöds mest av tiden, bör du överväga något av följande alternativ om möjligt:

* **Undanta disken som orsakar en hög data ändrings hastighet**: du kan utesluta disken med hjälp av [PowerShell](./azure-to-azure-exclude-disks.md). Om du vill utesluta disken måste du först inaktivera replikeringen. 
* **Ändra nivån på lagrings disken för haveri beredskap**: det här alternativet är endast möjligt om disk data omsättningen är mindre än 20 MB/s. Anta att en virtuell dator med en P10-disk har en data omsättning på mer än 8 MB/s men mindre än 10 MB/s. Om kunden kan använda en P30-disk för mål lagring under skyddet kan problemet lösas. Observera att den här lösningen endast är möjlig för datorer som använder Premium-Managed Disks. Följ stegen nedan:
    - Gå till bladet diskar på den berörda replikerade datorn och kopiera replik diskens namn
    - Navigera till den här replik hanterade disken
    - Du kan se en banderoll på bladet översikt som anger att en SAS-webbadress har skapats. Klicka på den här banderollen och Avbryt exporten. Ignorera det här steget om du inte ser banderollen.
    - Så snart SAS-URL: en har återkallats går du till konfigurations bladet för den hanterade disken och ökar storleken så att ASR stöder den observerade omsättnings takten på käll disken

## <a name="Network-connectivity-problem"></a>Problem med nätverks anslutningen

### <a name="network-latency-to-a-cache-storage-account"></a>Nätverks fördröjning till ett cache Storage-konto
Site Recovery skickar replikerade data till cache Storage-kontot. Du kan se nätverks fördröjningen om överföring av data från en virtuell dator till cache Storage-kontot är långsammare än 4 MB i 3 sekunder. 

Om du vill söka efter ett problem som rör svars tiden använder du [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) för att ladda upp data från den virtuella datorn till cache Storage-kontot. Om svars tiden är hög kontrollerar du om du använder en virtuell nätverks installation (NVA) för att styra utgående nätverks trafik från virtuella datorer. Installationen kan bli begränsad om all replikeringstrafik passerar genom NVA. 

Vi rekommenderar att du skapar en nätverks tjänst slut punkt i ditt virtuella nätverk för "lagring" så att replikeringstrafiken inte går till NVA. Mer information finns i [konfiguration av virtuell nätverks](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)installation.

### <a name="network-connectivity"></a>Nätverks anslutning
För att Site Recovery replikering ska fungera krävs utgående anslutning till vissa URL-adresser eller IP-intervall från den virtuella datorn. Om den virtuella datorn ligger bakom en brand vägg eller använder regler för nätverks säkerhets grupper (NSG) för att kontrol lera utgående anslutningar kan det hända att du stöter på något av dessa problem. För att se till att alla webb adresser är anslutna, se [utgående anslutning för Site Recovery-URL: er](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Fel-ID 153006 – ingen programkonsekvent återställnings punkt är tillgänglig för den virtuella datorn under de senaste XXX minuterna

Några av de vanligaste problemen visas nedan

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Orsak 1: kända problem i SQL Server 2008/2008 R2 
**Så här åtgärdar du** : det finns ett känt problem med SQL Server 2008/2008 R2. Det går inte att säkerhetskopiera den här KB-artikeln [Azure Site Recovery agent eller annan VSS-säkerhetskopiering som inte är en komponent för en server som är värd för SQL Server 2008](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Orsak 2: Azure Site Recovery jobb fungerar inte på servrar som är värdar för någon version av SQL Server-instanser med AUTO_CLOSE databaser 
**Så här åtgärdar du** : se KB- [artikel](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Orsak 3: kända problem i SQL Server 2016 och 2017
**Så här åtgärdar du** : se KB- [artikel](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>Orsak 4: du använder konfiguration av lagrings dirigering
**Så här åtgärdar du** : Azure Site Recovery kan inte skapa programkonsekvent återställnings punkt för konfiguration av lagrings dirigering. Se artikeln för att [Konfigurera replikeringsprincipen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms) korrekt

### <a name="more-causes-due-to-vss-related-issues"></a>Fler orsaker till följd av VSS-relaterade problem:

Om du vill felsöka ytterligare kan du kontrol lera filerna på käll datorn för att få en exakt felkod för felet:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Hur hittar du felen i filen?
Sök efter strängen "vacpError" genom att öppna filen vacp. log i en redigerare
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

I ovanstående exempel **2147754994** är felkoden som visar dig om felet som visas nedan

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS-skrivaren är inte installerad-fel 2147221164 

*Så här åtgärdar*du: om du vill skapa en program konsekvenss etikett använder Azure Site Recovery Microsoft Volume Shadow Copy Service (VSS). Den installerar en VSS-Provider för åtgärden att ta ögonblicks bilder av program konsekvens. Den här VSS-providern installeras som en tjänst. Om tjänsten VSS Provider inte är installerad, Miss lyckas skapandet av program konsekvensen med fel-ID 0x80040154 "-klassen har inte registrerats". </br>
Se [artikeln om fel sökning av VSS Writer-installation](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS-skrivaren är inaktive rad-fel 2147943458

**Så här åtgärdar**du: om du vill skapa en program konsekvenss etikett använder Azure Site Recovery Microsoft Volume Shadow Copy Service (VSS). Den installerar en VSS-Provider för åtgärden att ta ögonblicks bilder av program konsekvens. Den här VSS-providern installeras som en tjänst. Om tjänsten VSS Provider är inaktive rad kan inte skapandet av program konsekvens skapas med fel-ID: t "den angivna tjänsten är inaktive rad och kan inte startas (0x80070422)". </br>

- Om VSS är inaktiverat
    - Kontrol lera att start typen för tjänsten VSS Provider är inställd på **Automatisk**.
    - Starta om följande tjänster:
        - VSS-tjänst
        - Azure Site Recovery VSS-Provider
        - VDS-tjänst

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS-PROVIDERns NOT_REGISTERED – fel 2147754756

**Så här åtgärdar**du: om du vill skapa en program konsekvenss etikett använder Azure Site Recovery Microsoft Volume Shadow Copy Service (VSS). Kontrol lera om tjänsten Azure Site Recovery VSS Provider är installerad eller inte. </br>

- Försök att installera providern med följande kommandon:
- Avinstallera befintlig provider: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Installera om: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Kontrol lera att start typen för tjänsten VSS Provider är inställd på **Automatisk**.
    - Starta om följande tjänster:
        - VSS-tjänst
        - Azure Site Recovery VSS-Provider
        - VDS-tjänst
