---
title: Nyheter i Azure Site Recovery
description: Innehåller en sammanfattning av nya funktioner och de senaste uppdateringarna i Azure Site Recovery-tjänsten.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: abb1592bcacf025e9a052d7a9222f6fb3d2b72d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257438"
---
# <a name="whats-new-in-site-recovery"></a>Nyheter i Site Recovery

[Azure Site Recovery-tjänsten](site-recovery-overview.md) uppdateras och förbättras löpande. Den här artikeln innehåller information om de senaste versionerna, nya funktioner och nytt innehåll för att hjälpa dig att hålla dig uppdaterad. Denna sida uppdateras regelbundet.

Du kan följa och prenumerera på meddelanden om uppdatering av webbplatsåterställning i [Azure-uppdateringskanalen.](https://azure.microsoft.com/updates/?product=site-recovery)

## <a name="supported-updates"></a>Uppdateringar som stöds

För Site Recovery-komponenter stöder vi N-4-versioner, där N är den senaste versionen. Dessa sammanfattas i tabellen nedan.

**Uppdatering** |  **Enhetlig installation** | **Konfigurationsserverova** | **Agent för mobilitetstjänst** | **Leverantör av webbplatsåterställning** | **Agent för återställningstjänster**
--- | --- | --- | --- | --- | ---
[Sammanslagning 45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0
[Sammanslagning 43](https://support.microsoft.com/help/4537047/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[Sammanslagning 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[Sammanslagning 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[Sammanslagning 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0

[Läs mer](service-updates-how-to.md) om installation och support för uppdateringar.

> [!NOTE]
> Samlad uppdatering 44 visas inte i tabellen eftersom den inte innehöll uppdateringar för webbplatsåterställningsleverantörerna och agenter.

## <a name="updates-march-2020"></a>Uppdateringar (mars 2020)

### <a name="update-rollup-45"></a>Samlad uppdatering 45

[Samlad uppdatering 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) innehåller följande uppdateringar:

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | Uppdateringar av site recovery agenter och leverantörer som beskrivs i den samlade.
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i sammanslagningen.

## <a name="updates-january-2020"></a>Uppdateringar (januari 2020)

### <a name="update-rollup-44"></a>Samlad uppdatering 44

[Samlad uppdatering 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | Det fanns inga uppdateringar för webbplatsåterställningsleverantörerna och agenter.
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar som beskrivs i sammanslagningen.

### <a name="azure-vmware-disaster-recovery"></a>Azure VMware haveriberedskap

Virtuella Azure-datorer stöder nu virtuella datorer som möjliggör kryptering i vila med kundhanterade nycklar. [Läs mer](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Samlad uppdatering 43

[Samlad uppdatering 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | Uppdateringar av site recovery agenter och leverantörer (som beskrivs i den samlade)
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen)


## <a name="updates-november-2019"></a>Uppdateringar (november 2019)

### <a name="update-rollup-42"></a>Samlad uppdatering 42

[Samlad uppdatering 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | Uppdateringar av site recovery agenter och leverantörer (som beskrivs i den samlade)
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen)


### <a name="azure-vm-disaster-recovery"></a>Azure VM-haveriberedskap

Nya funktioner för azure vm-haveriberedskap sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Uefi** | Site Recovery stöder nu haveriberedskap för virtuella Azure-datorer med UEFI-baserad startarkitektur.
**Linux** | Site Recovery stöder nu virtuella Azure-datorer som kör Linux med Azure Disk Encryption (ADE).
**Generation 2** | Alla virtuella azure-datorer för generation 2 stöds nu för haveriberedskap.
**Regioner** | Du kan nu aktivera haveriberedskap för virtuella Azure-datorer i Norge geo.

### <a name="vmware-to-azure-disaster-recovery"></a>Haveriberedskap för VMware till Azure

Nya funktioner för VMware till Azure-haveriberedskap sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Uefi** | Site Recovery stöder nu haveriberedskap för virtuella datorer med UEFI-baserad startarkitektur.<br/><br/> Operativsystem som stöds omfattar Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 SP4, RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Uppdatering för underhåll av stackuppdatering/SHA-2

För haveriberedskap av virtuella Azure-datorer till en sekundär region, eller lokala virtuella datorer med VMware eller fysiska servrar till Azure, observera följande:

- Från version 9.30.5407.1 av tillägget mobilitetstjänst (för virtuella Azure-datorer) och Mobilitetsserviceagent (för VMware/fysiska datorer) måste vissa datoroperativsystem köra underhållsstackenuppdateringen och SHA-2. Detaljer visas i tabellen nedan.
- Installera uppdateringen och SHA-2 i enlighet med den länkade KB. SHA-1 stöds inte från september 2019, och om SHA-2-kodsignering inte är aktiverat installeras/uppgraderas inte agenttillägget som förväntat.
- Läs mer om [SHA-2-uppgradering och krav](https://aka.ms/SHA-2KB).

**Operativsystem** | **Azure VM** | **VMware VM/fysisk maskin**
--- | --- | ---
**Windows 2008 R2 SP1** | [Uppdatering av underhållsstapel](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Uppdatering av underhållsstapel](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Uppdatering av underhållsstapel](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Uppdatering av underhållsstapel](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Uppdatering av underhållsstapel](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Uppdatering av underhållsstapel](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Uppdateringar (oktober 2019)

### <a name="update-rollup-41"></a>Samlad uppdatering 41

[Samlad uppdatering 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | Uppdateringar av site recovery agenter och leverantörer (som beskrivs i den samlade)
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen)



### <a name="azure-vm-disaster-recovery"></a>Azure VM-haveriberedskap

Nya funktioner för azure vm-haveriberedskap sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Testa redundansinställningar** | När du konfigurerar en testväxling kan du nu konfigurera inställningar för test failover-vm och nätverk, inklusive IP-adress, NSG, intern belastningsbalans och den offentliga IP-adressen för varje dator-nätverkskort. Dessa inställningar är valfria och ändrar inte aktuellt beteende. Om du inte konfigurerar dessa inställningar kan du välja ett Azure VNet vid tidpunkten för test redundans. [Läs mer](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Återställningsplaner** | Återställningsplaner är nu begränsade till 100 virtuella datorer för att säkerställa redundanstillförlitlighet.

### <a name="vmware-to-azure-disaster-recovery"></a>Haveriberedskap för VMware till Azure

Nya funktioner för VMware till Azure-haveriberedskap sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Återställningsplaner** | Återställningsplaner är nu begränsade till 100 virtuella datorer för att säkerställa redundanstillförlitlighet.


## <a name="updates-september-2019"></a>Uppdateringar (september 2019)

### <a name="update-rollup-40"></a>Samlad uppdatering 40

[Samlad uppdatering 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | Uppdateringar av site recovery agenter och leverantörer (som beskrivs i den samlade)
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen)




### <a name="azure-vm-disaster-recovery"></a>Azure VM-haveriberedskap

Nya funktioner för azure vm-haveriberedskap sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Rensning efter återställning efter återställning** | När den har misslyckats med den sekundära Azure och sedan misslyckats tillbaka till den primära regionen rensar site recovery automatiskt upp datorer i den sekundära regionen. Du behöver inte ta bort virtuella datorer och nätverkskort manuellt.
**Test failover behåller IP-adressen** | Du kan nu behålla IP-adressen för källdatorn under en haveriberedskapsövning och välja en statisk IP-adress för en testundanställning.

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fysisk serverkatastrofåterställning

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
Nya processservervarningar | Vi har lagt till nya processserveraviseringar. [Läs mer](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Hyper-V katastrofåterställning

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
Lagringskonto | Site Recovery stöder nu användningen av lagringskonton med brandvägg aktiverad för Hyper-V till Azure-haveriberedskap.  Du kan välja brandväggsaktiverade lagringskonton som ett målkonto eller för cachelagring. Om du använder ett brandväggsaktiverat konto kontrollerar du att du aktiverar alternativet för att tillåta betrodda Microsoft-tjänster.<br/><br/> Detta stöds för virtuella hyper-virtuella datorer med eller utan System Center VMM.


## <a name="updates-august-2019"></a>Uppdateringar (augusti 2019)

### <a name="update-rollup-39"></a>Samlad uppdatering 39

[Samlad uppdatering 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | Uppdateringar av site recovery agenter och leverantörer (som beskrivs i den samlade)
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen)


### <a name="azure-vm-disaster-recovery"></a>Azure VM-haveriberedskap

Nya funktioner för azure vm-haveriberedskap sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Kryptering utan Azure AD** | Kryptering utan en Azure AD-app stöds nu för Azure VM-replikering till hanterade diskar som kör Windows.
**Nätverksresurser för redundans** | När du misslyckas med en annan region kan du nu koppla nätverksresursinställningar (NSG, belastningsutjämning, offentlig IP-adress) till en virtuell dator.

## <a name="updates-july-2019"></a>Uppdateringar (juli 2019)

### <a name="update-rollup-38"></a>Samlad uppdatering 38

[Samlad uppdatering 38](https://support.microsoft.com/help/4513507/) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | Uppdateringar av site recovery agenter och leverantörer (som beskrivs i den samlade)
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen)


### <a name="general"></a>Allmänt

Site Recovery stöder nu användningen av allmänna v2-lagringskonton för cachelagring eller mållagring. Tidigare stöddes endast v1.

### <a name="vmware-to-azure-disaster-recovery"></a>Haveriberedskap för VMware till Azure

Du kan nu replikera diskar upp till 8 TB när du replikerar till en Azure VM med hanterade diskar.


## <a name="updates-june-2019"></a>Uppdateringar (juni 2019)

### <a name="update-rollup-37"></a>Samlad uppdatering 37

[Samlad uppdatering 37](https://support.microsoft.com/help/4508614/) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | Uppdateringar av site recovery agenter och leverantörer (som beskrivs i den samlade)
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fysisk serverkatastrofåterställning

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**GPT-partitioner** | Från Samlad uppdatering 37 och framåt (Mobilitetstjänstversion 9.25.5241.1) stöds upp till fem GPT-partitioner i UEFI. Före den här uppdateringen stöddes fyra.



## <a name="updates-may-2019"></a>Uppdateringar (maj 2019)

### <a name="update-rollup-36"></a>Samlad uppdatering 36

[Samlad uppdatering 36](https://support.microsoft.com/help/4503156) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av site recovery agenter och leverantörer (som beskrivs i den samlade)
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen)

### <a name="azure-vm-disaster-recovery"></a>Azure VM-haveriberedskap

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Replikera tillagda diskar** | Aktivera replikering för datadiskar som lagts till i en Azure-virtuell dator som redan är aktiverad för haveriberedskap. [Läs mer](azure-to-azure-enable-replication-added-disk.md).
**Automatiska uppdateringar** | När du konfigurerar automatiska uppdateringar för tillägget Mobilitetstjänst som körs på virtuella Azure-datorer som är aktiverade för haveriberedskap kan du nu välja ett befintligt automatiseringskonto som ska användas, i stället för att använda standardkontot som skapats av Site Recovery. [Läs mer](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fysisk serverkatastrofåterställning

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Övervakning av processserver** | För haveriberedskap av lokala virtuella datorer med VMware och fysiska servrar kan du övervaka och felsöka processserverproblem med förbättrad rapportering och aviseringar för serverhälsa. [Läs mer](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Uppdateringar (mars 2019)

### <a name="update-rollup-35"></a>Samlad uppdatering 35

[Samlad uppdatering 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av site recovery agenter och leverantörer (som beskrivs i den samlade)
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen)

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fysisk serverkatastrofåterställning

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Hanterade diskar** | Replikering av lokala virtuella datorer och fysiska servrar är nu direkt till hanterade diskar i Azure. Lokala data skickas till ett cachelagringskonto i Azure och återställningspunkter skapas i hanterade diskar på målplatsen. Detta säkerställer att du inte behöver hantera flera mållagringskonton.
**Konfigurationsserver** | Site Recovery stöder nu konfigurationsservrar med flera nätverkskort. Lägg till ytterligare kort till konfigurationsserverns virtuella dator innan du registrerar konfigurationsservern i valvet. Om du lägger till efteråt måste du registrera servern igen i valvet.


## <a name="updates-february-2019"></a>Uppdateringar (februari 2019)

### <a name="update-rollup-34"></a>Samlad uppdatering 34

[Samlad uppdatering 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av site recovery agenter och leverantörer (som beskrivs i den samlade).
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen).


### <a name="update-rollup-33"></a>Samlad uppdatering 33

[Samlad uppdatering 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av site recovery agenter och leverantörer (som beskrivs i den samlade).
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen).


### <a name="azure-vm-disaster-recovery"></a>Azure VM-haveriberedskap

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Nätverksmappning** | För Azure VM-haveriberedskap kan du nu använda alla tillgängliga målnätverk när du aktiverar replikering.
**Standard SSD** | Du kan nu ställa in haveriberedskap för virtuella Azure-datorer med [standard SSD-diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Lagringsutrymmen direkt** | Du kan ställa in haveriberedskap för appar som körs på Azure VM-appar med hjälp av [Storage Spaces Direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) för hög tillgänglighet.  Använda Storage Spaces Direct (S2D) tillsammans med Site Recovery ger omfattande skydd av Azure VM-arbetsbelastningar. Med S2D kan du vara värd för ett gästkluster i Azure. Detta är särskilt användbart när en virtuell dator är värd för ett kritiskt program, till exempel SAP ASCS-lager, SQL Server eller skalningsfilserver.


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fysisk serverkatastrofåterställning

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Linux BRTFS filsystem** | Site Recovery stöder nu replikering av virtuella datorer med BRTFS-filsystemet. Replikering stöds inte om:<br/><br/>- Undervolymen btrfs-filsystem ändras efter att replikering har aktiverats.<br/><br/>- Filsystemet är uppspänt över flera diskar.<br/><br/>- BTRFS filsystem stöder RAID.
**Windows Server 2019** | Stöd tillagd för datorer som kör Windows Server 2019.


## <a name="updates-january-2019"></a>Uppdateringar (januari 2019)


### <a name="accelerated-networking-azure-vms"></a>Accelererat nätverk (virtuella Azure-datorer)

Accelererad nätverk möjliggör enkel rot-I/O-virtualisering (SR-IOV) till en virtuell dator, vilket förbättrar nätverksprestanda. När du aktiverar replikering för en virtuell Azure-dator identifierar Site Recovery om accelererade nätverk är aktiverat. Om det är, efter redundans Site Recovery automatiskt konfigurerar accelererade nätverk på målet repliken Azure VM, för både [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) och [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Läs mer](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Samlad uppdatering 32

[Samlad uppdatering 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av site recovery agenter och leverantörer (som beskrivs i den samlade).
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen).

### <a name="azure-vm-disaster-recovery"></a>Azure VM-haveriberedskap

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Support har lagts till för RedHat Workstation 6/7 och nya kärnversioner för Ubuntu, Debian och SUSE.
**Lagringsutrymmen direkt** | Site Recovery stöder virtuella Azure-datorer med Hjälp av Storage Spaces Direct (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware virtuella datorer/fysiska servrar haveriberedskap

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd har lagts till för Redhat Enterprise Linux 7.6, RedHat Workstation 6/7, Oracle Linux 6.10 och Oracle Linux 7.6 och nya kärnversioner för Ubuntu, Debian och SUSE.


### <a name="update-rollup-31"></a>Samlad uppdatering 31

[Samlad uppdatering 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av site recovery agenter och leverantörer (som beskrivs i den samlade).
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen).

### <a name="vmware-vmsphysical-servers-replication"></a>VMware virtuella datorer/fysiska servrar replikering

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** |  Stöd har lagts till för Oracle Linux 6.8, Oracle Linux 6.9 och Oracle Linux 7.0 med Red Hat Compatible Kernel och för Unbreakable Enterprise Kernel (UEK) Release 5.
**Lvm** | Stöd tillagd för LVM- och LVM2-volymer.<br/><br/> Katalogen /boot på en diskpartition och på LVM-volymer stöds nu.
**Kataloger** | Stöd har lagts till för dessa kataloger som konfigurerats som separata partitioner eller filsystem som inte finns på samma systemdisk:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Stöd tillagd för dynamiska diskar.
**Redundans** | Förbättrad redundanstid för virtuella datorer med VMware där storvsc och vsbus inte är startdrivrutiner.
**Support för UEFI** | Virtuella Azure-datorer stöder inte starttyp UEFI. Du kan nu migrera lokala fysiska servrar med UEFI till Azure med Site Recovery. Site Recovery migrerar servern genom att konvertera starttypen till BIOS före migreringen. Site Recovery tidigare stödde den här konverteringen endast för virtuella datorer. Support är tillgänglig för fysiska servrar som kör Windows Server 2012 eller senare.

### <a name="azure-vm-disaster-recovery"></a>Azure VM-haveriberedskap

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd har lagts till för Oracle Linux 6.8, Oracle Linux 6.9 och Oracle Linux 7.0 med Red Hat Compatible Kernel och för Unbreakable Enterprise Kernel (UEK) Release 5.
**Linux BRTFS filsystem** | Stöds för virtuella Azure-datorer.
**Virtuella Azure-datorer i tillgänglighetszoner** | Du kan aktivera replikering till en annan region för virtuella Azure-datorer som distribueras i tillgänglighetszoner. Du kan nu aktivera replikering för en Virtuell Azure-dator och ange målet för redundans till en enda VM-instans, en virtuell dator i en tillgänglighetsuppsättning eller en virtuell dator i en tillgänglighetszon. Inställningen påverkar inte replikering. [Läs](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) meddelandet.
**Brandväggsaktiverad lagring (portal/PowerShell)** | Stöd tillagd för [brandväggsaktiverade lagringskonton](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Du kan replikera virtuella Azure-datorer med ohanterade diskar på brandväggsaktiverade lagringskonton till en annan Azure-region för haveriberedskap.<br/><br/> Du kan använda brandväggsaktiverade lagringskonton som mållagringskonton för ohanterade diskar.<br/><br/> Stöds i portalen och med PowerShell.

## <a name="updates-december-2018"></a>Uppdateringar (december 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Automatiska uppdateringar för mobilitetstjänsten (virtuella Azure-datorer)

Site Recovery har lagt till ett alternativ för automatiska uppdateringar av tillägget mobilitetstjänsten. Tillägget för mobilitetstjänsten installeras på varje Azure-virtuell dator som replikeras av Site Recovery. När du aktiverar replikering väljer du om webbplatsåterställning ska tillåtas att hantera uppdateringar av tillägget.

Uppdateringar kräver inte en omstart av den virtuella datorn och påverkar inte replikeringen. [Läs mer](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Priskalkylator för azure vm-haveriberedskap

Haveriberedskap för virtuella Azure-datorer medför vm-licenskostnader och nätverks- och lagringskostnader. Azure tillhandahåller en [priskalkylator](https://aka.ms/a2a-cost-estimator) som hjälper dig att räkna ut dessa kostnader. Site Recovery ger nu ett [exempel pris uppskattning](https://aka.ms/a2a-cost-estimator) som priser ett exempel distribution baserat på en tre-tier app med sex virtuella datorer med 12 Standard HDD-diskar och 6 Premium SSD-diskar.

- Exemplet förutsätter en dataändring på 10 GB per dag för standard och 20 GB för premium.
- För just din distribution kan du ändra variablerna för att uppskatta kostnader.
- Du kan ange antalet virtuella datorer, antalet och typen av hanterade diskar och den förväntade totala dataförändringshastigheten som förväntas mellan de virtuella datorerna.
- Dessutom kan du använda en komprimeringsfaktor för att uppskatta bandbreddskostnader.

[Läs](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) meddelandet.


## <a name="updates-october-2018"></a>Uppdateringar (oktober 2018)

### <a name="update-rollup-30"></a>Samlad uppdatering 30

[Samlad uppdatering 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av site recovery agenter och leverantörer (som beskrivs i den samlade).
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen).

### <a name="azure-vm-disaster-recovery"></a>Azure VM-haveriberedskap
Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Stöd för regionen** | Site Recovery-stöd har lagts till för Australia Central 1 och Australia Central 2.
**Stöd för diskkryptering** | Stöd tillagd för haveriberedskap av virtuella Azure-datorer krypterade med Azure Disk Encryption (ADE) med Azure AD-appen. [Läs mer](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Uteslutning av disk** | Oinitierade diskar utesluts nu automatiskt under Azure VM-replikering.
**Brandväggsaktiverad lagring (PowerShell)** | Stöd tillagd för [brandväggsaktiverade lagringskonton](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Du kan replikera virtuella Azure-datorer med ohanterade diskar på brandväggsaktiverade lagringskonton till en annan Azure-region för haveriberedskap.<br/><br/> Du kan använda brandväggsaktiverade lagringskonton som mållagringskonton för ohanterade diskar.<br/><br/> Stöds endast med PowerShell.


### <a name="update-rollup-29"></a>Samlad uppdatering 29

[Samlad uppdatering 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av site recovery agenter och leverantörer (som beskrivs i den samlade).
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen).


## <a name="updates-august-2018"></a>Uppdateringar (augusti 2018)

### <a name="update-rollup-28"></a>Samlad uppdatering 28

[Samlad uppdatering 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av site recovery agenter och leverantörer (som beskrivs i den samlade).
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen).

### <a name="azure-vm-disaster-recovery"></a>Azure VM-haveriberedskap
Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Lade till stöd för RedHat Enterprise Linux 6.10; CentOS 6,10.<br/><br/>
**Molnstöd** | Katastrofåterställning som stöds för virtuella Azure-datorer i molnet i Tyskland.
**Katastrofåterställning mellan prenumerationer** | Stöd för att replikera virtuella Azure-datorer i en region till en annan region i en annan prenumeration, inom samma Azure Active Directory-klientorganisation. [Läs mer](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/fysisk serverkatastrofåterställning
Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd tillagd för RedHat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> Linux-baserade virtuella datorer som använder GPT-partitionsstil (GUID partition) (GPT) i äldre BIOS-kompatibilitetsläge stöds nu. Läs [vanliga frågor](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) och svar om Azure VM för mer information.
**Haveriberedskap för virtuella datorer efter migreringen** | Stöd för att aktivera haveriberedskap till en sekundär region för en lokal virtuell dator för VMware som migrerats till Azure, utan att behöva avinstallera mobilitetstjänsten på den virtuella datorn innan replikering aktiveras.
**Windows Server 2008** | Stöd för migreringsmaskiner som kör Windows Server 2008 R2/2008 64-bitars och 32-bitars.<br/><br/> Endast migrering och redundansväxling. Återställning efter fel stöds inte.

## <a name="updates-july-2018"></a>Uppdateringar (juli 2018)

### <a name="update-rollup-27-july-2018"></a>Samlad uppdatering 27 (juli 2018)

[Samlad uppdatering 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av site recovery agenter och leverantörer (som beskrivs i den samlade).
**Problemfixar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i sammanslagningen).

### <a name="azure-vm-disaster-recovery"></a>Azure VM-haveriberedskap

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd tillagd för Red Hat Enterprise Linux 7.5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/fysisk serverkatastrofåterställning

Funktioner som läggs till den här månaden sammanfattas i tabellen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd tillagd för Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Nästa steg

Håll dig uppdaterad med våra uppdateringar på sidan [Azure-uppdateringar.](https://azure.microsoft.com/updates/?product=site-recovery)
