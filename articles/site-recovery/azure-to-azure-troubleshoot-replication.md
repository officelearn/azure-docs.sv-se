---
title: Azure Site Recovery felsökning för Azure till Azure-replikeringsproblem och fel | Microsoft Docs
description: Felsöka fel och problem vid replikering av virtuella datorer i Azure för haveriberedskap
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 4a18e009f7defc8d41846b867f9b7a65d2b853dd
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993339"
---
# <a name="troubleshoot-azure-to-azure-vm-ongoing-replication-issues"></a>Felsöka problem med pågående replikering för Azure till Azure VM

Den här artikeln beskriver vanliga problem i Azure Site Recovery när replikera och återställa virtuella Azure-datorer från en region till en annan region och förklarar hur du felsöker dem. Mer information om konfigurationer som stöds finns i den [stöd matrix för att replikera virtuella Azure-datorer](site-recovery-support-matrix-azure-to-azure.md).


## <a name="recovery-points-not-getting-generated"></a>Återställningspunkter som är inte en aviseringsstorm Generera

FELMEDDELANDE: Inga kraschkonsekventa återställningspunkten är tillgängliga för den virtuella datorn under de senaste 60 minuterna.</br>
FEL-ID: 153007 </br>

Azure Site Recovery replikerar data från källregionen till regionen disaster recovery konsekvent och skapar kraschkonsekvent återställningspunkt var femte minut. Om Site Recovery är det går inte att skapa återställningspunkter i 60 minuter, varnar användare. Här följer orsaker som kan resultera i det här felet:

**Orsak 1: [Hög dataändringshastigheten på den virtuella källdatorn](#high-data-change-rate-on-the-source-virtal-machine)**    
**Orsak 2: [Problem med nätverksanslutningen ](#Network-connectivity-issue)**

## <a name="causes-and-solutions"></a>Orsaker och lösningar

### <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Hög dataändringshastighet på käll-VM
Azure Site Recovery utlöser en händelse om förändringstakten för data på den virtuella källdatorn är högre än gränsvärden som stöds. Om du vill kontrollera om problemet är på grund av hög omsättning, gå till replikerat objekt > virtuell dator > Klicka på ”händelser – senaste 72 timmar”.
Du bör se den ”dataändring takten utöver de gränser som stöds” som visas i skärmbilden nedan

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Om du klickar på händelsen, bör du se exakt diskinformationen som visas i skärmbilden nedan

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


#### <a name="azure-site-recovery-limits"></a>Gränser för Azure Site Recovery
Följande tabell innehåller gränserna för Azure Site Recovery. Dessa gränser är baserade på våra tester, men de täcker inte alla möjliga kombinationer av program-I/O. De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program. Vi ska också Observera att det finns två gränser för hur per disk data churn och per virtuell datordata omsättning.
Till exempel, om vi tittar på P20 Premium disk i den tabellen, nedan kan Site Recovery för att hantera 5 MB/s dataomsättningen per disk med med max för fem dessa diskar per virtuell dator på grund av att gränsen på 25 MB/s totala dataomsättningen per virtuell dator.

**Replication Storage Target** (Lagringsmål för replikering) | **Average Source Disk I/O Size** (Genomsnittlig I/O-storlek för källdisk) |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total Source Disk Data Churn Per Day** (Total dataomsättning per dag för källdisk)
---|---|---|---
Standard Storage | 8 kB | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB  | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |  336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |10 MB/s | 842 GB per disk

### <a name="solution"></a>Lösning
Vi förstår att Azure Site Recovery har data ändrar hastighetsbegränsningar baserat på vilken typ av disk. Om du vill veta om det här problemet är återkommande eller under ett ögonblick, är det viktigt att hitta ändra data frekvensen mönstret för den berörda virtuella datorn.
Att hitta dataändringshastighet på den berörda virtuella datorn. Gå till den virtuella källdatorn > mått under övervakning och Lägg till mått som visas nedan.

![high_data_change_rate](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Klicka på ”Lägg till mått” och lägga till ”OS-Disk-skrivna byte/s” och ”Data-Disk-skrivna byte/s”.
2. Övervaka insamling som visas i skärmbilden.
3. Den visar det totala antalet skriver åtgärden som händer i OS-disk och alla datadiskar kombineras. Nu de här måtten kan inte visa per disk nivåinformation men är en bra indikator på omsättningsmönstret totala data.

I de här fallen ovan om det är en databearbetning burst och förändringstakten för data är större än 10 Mbit/s (för Premium) och 2 Mbit/s (för Standard) under en viss tid och kommer nedåt ska replikering fånga upp. Men om omsättningen är långt utöver gränsen som stöds i de flesta fall, sedan bör du överväga någon av de nedan alternativet om möjligt:

**Alternativ 1:** Undanta en disk som orsakar hög dataändringshastigheten: </br>
Du kan för närvarande undanta en disk med hjälp av [Site Recovery Powershell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)

**Alternativ 2:** Ändra disaster recovery lagringsnivå disk: </br>
Det här alternativet är endast möjligt om dataomsättningen för disken är mindre än 10 MB/s. Låt anta att en virtuell dator med P10 har disken en dataomsättning som är större än 8 MB/s men mindre än 10 MB/s. Om kunden kan använda P30-disk för mållagring vid skydd, kan problemet lösas.

### <a name="Network-connectivity-issue"></a>Problem med nätverksanslutningen

#### <a name="network-latency-to-cache-storage-account-"></a>Svarstid för nätverk till cachelagringskontot:
 Site Recovery skickar replikerade data till cachelagringskontot och problemet kan inträffa om dataöverföringen från den virtuella datorn till cachelagringskontot är långsammare den 4 MB i 3 sekunder. Att kontrollera om det finns några problem som rör svarstid Använd [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) att överföra data från den virtuella datorn till cachelagringskontot.<br>
Om svarstiden är hög, kontrollerar du om du använder en virtuell nätverksutrustning för att styra utgående nätverkstrafik från virtuella datorer. Installationen kan begränsas om all replikeringstrafik som passerar genom NVA. Vi rekommenderar att du skapar en tjänstslutpunkt för nätverk i ditt virtuella nätverk för ”Storage” så att replikeringstrafiken inte går att NVA. Se [nätverkskonfiguration för virtuell installation](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)

#### <a name="network-connectivity"></a>Nätverksanslutning
För Site Recovery-replikering till arbete, utgående anslutning till specifika URL: er eller IP-intervall krävs från den virtuella datorn. Om den virtuella datorn finns bakom en brandvägg eller använder regler för nätverkssäkerhetsgrupper (NSG) för att styra utgående anslutningar, kan du står inför ett av de här problemen.</br>
Referera till [utgående anslutning för Site Recovery-webbadresser](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) att kontrollera att alla URL: er är anslutna 