---
title: Vad är nytt i Azure Site Recovery | Microsoft Docs
description: Innehåller en översikt över nya funktioner som introduceras i Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: raynew
ms.openlocfilehash: e2145fbbb5fa09aa3321742ca8a786822f6f0641
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148653"
---
# <a name="whats-new-in-site-recovery"></a>Nyheter i Site Recovery

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten uppdateras och förbättras kontinuerligt. Som hjälper dig att hålla dig uppdaterad, den här artikeln innehåller information om de senaste versioner, nya funktioner och nytt innehåll. Den här sidan uppdateras regelbundet.

Om du har förslag på funktioner för Site Recovery kan vi vill gärna [höra dina synpunkter](https://feedback.azure.com/forums/256299-site-recovery).


## <a name="updates-march-2019"></a>Uppdateringar (mars 2019)

### <a name="update-rollup-35"></a>Samlad uppdatering 35

[Uppdatera samlad 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery-agenter och leverantörer (som beskrivs i samlade)
**Problemet korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i samlade)

#### <a name="vmwarephysical-server-disaster-recovery"></a>Haveriberedskap för VMware/fysisk server
Nya funktioner i uppdateringen.

**Funktion** | **Detaljer**
--- | ---
**Hanterade diskar** | Replikering av lokala virtuella VMware-datorer och fysiska servrar finns nu direkt till managed disks i Azure. Lokala data skickas till ett cachelagringskonto i Azure och återställningspunkter skapas i hanterade diskar på målplatsen. Detta säkerställer att du inte behöver hantera lagringskonton för flera mål.
**Konfigurationsserver** | Site Recovery stöder nu en konfigurationsservrar med flera nätverkskort. Du måste lägga till ytterligare nätverkskort konfigurationsservern VM innan du registrerar konfigurationsservern i valvet. Om du lägger till efteråt kan behöva du registrera servern i valvet.


## <a name="updates-february-2019"></a>Uppdateringar (februari 2019)

### <a name="update-rollup-34"></a>Samlad uppdatering 34 

[Uppdatera samlad 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery-agenter och leverantörer (som beskrivs i samlade).
**Problemet korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i samlade).


### <a name="update-rollup-33"></a>Samlad uppdatering 33 

[Uppdatera samlad 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery-agenter och leverantörer (som beskrivs i samlade).
**Problemet korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i samlade).


#### <a name="azure-vm-disaster-recovery"></a>Azure VM-katastrofåterställning 
Nya funktioner i uppdateringen.

**Funktion** | **Detaljer**
--- | ---
**Nätverksmappning** | För haveriberedskap för virtuella Azure-datorer, kan du nu använda alla tillgängliga målnätverket när du aktiverar replikering. 
**Standard SSD** | Du kan nu konfigurera haveriberedskap för virtuella Azure-datorer med hjälp av [Standard SSD-diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Lagringsdirigering** | Du kan konfigurera haveriberedskap för appar som körs på Virtuella Azure-appar med hjälp av [Lagringsdirigering](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) för hög tillgänglighet.  Med hjälp av Lagringsdirigering (S2D) tillsammans med Site Recovery ger heltäckande skydd för Azure VM-arbetsbelastningar. S2D kan du vara värd för ett gästkluster i Azure. Detta är särskilt användbart när en virtuell dator är värd för en kritiska program, till exempel SAP ASCS layer, SQL Server eller skalbar filserver.


#### <a name="vmwarephysical-server-disaster-recovery"></a>Haveriberedskap för VMware/fysisk server
Nya funktioner i uppdateringen.

**Funktion** | **Detaljer**
--- | ---
**Linux BRTFS filsystem** | Site Recovery har nu stöd för replikering av virtuella VMware-datorer med filsystemet BRTFS. Replikering stöds inte om:<br/><br/>-BTRFS underordnade filsystemvolymen har ändrats efter att ha aktiverat replikering.<br/><br/>-Filsystemet är utspridd över flera diskar.<br/><br/>-BTRFS filsystemet har stöd för RAID.
**Windows Server 2019** | Stöd för datorer som kör Windows Server 2019.


## <a name="updates-january-2019"></a>Uppdateringar (januari 2019)

### <a name="accelerated-networking-azure-vms"></a>Accelererat nätverk (Azure virtuella datorer)

Accelererat nätverk aktivera i/o-virtualisering (SR-IOV) till en virtuell dator, förbättra nätverkets prestanda. När du aktiverar replikering för en Azure-dator, identifierar Site Recovery om accelererat nätverk är aktiverad. Om det är, efter redundans Site Recovery automatiskt konfigurerar accelererat nätverk på målet repliken Azure-dator, för både [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) och [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Läs mer](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Samlad uppdatering 32 

[Uppdatera samlad 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery-agenter och leverantörer (som beskrivs i samlade).
**Problemet korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i samlade).

#### <a name="azure-vm-disaster-recovery"></a>Azure VM-katastrofåterställning

Nya funktioner i uppdateringen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd har lagts till för Red Hat-arbetsstation 6/7 och nya kernel-versioner för Ubuntu, Debian och SUSE.
**Lagringsdirigering** | Site Recovery stöder virtuella Azure-datorer med hjälp av Lagringsdirigering (S2D).

#### <a name="vmware-vmsphysical-servers-replication"></a>Replikering för VMware-datorer/fysiska servrar 
**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd har lagts till för 7.6 för Red Hat Enterprise Linux, Red Hat-arbetsstation 6/7, Oracle Linux 6.10/7.6 och nya kernel-versioner för Ubuntu, Debian och SUSE.


### <a name="update-rollup-31"></a>Samlad uppdatering 31 

[Uppdatera samlad 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery-agenter och leverantörer (som beskrivs i samlade).
**Problemet korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i samlade).

#### <a name="vmware-vmsphysical-servers-replication"></a>Replikering för VMware-datorer/fysiska servrar 
Nya funktioner i uppdateringen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd har lagts till för Oracle Linux 6.8 och 6,9/7.0 och för UEK5 kernel.
**LVM** | Stöd har lagts till för LVM och LVM2 volymer.<br/><br/> / Boot-katalogen på en diskpartition och LVM-volymer stöds nu.
**Kataloger** | Stöd har lagts till för dessa kataloger som separata partitioner eller filsystem som inte är på samma systemdisk:<br/><br/> / (rot), / Boot, usr, / usr/local, /var, / etc.
**Windows Server 2008** | Stöd för dynamiska diskar.
**Redundans** | Förbättrad redundanstiden för virtuella VMware-datorer där storvsc och vsbus inte startdrivrutiner.
**Stöd för UEFI** | Virtuella Azure-datorer stöder inte starttyp UEFI. Nu kan du migrera lokala fysiska servrar med UEFI till Azure med Site Recovery. Site Recovery migrerar servern genom att konvertera starttyp till BIOS innan migreringen. Site Recovery tidigare stöd för den här konverteringen för virtuella datorer bara. Support är tillgänglig för fysiska servrar som kör Windows Server 2012 eller senare.

#### <a name="azure-vm-disaster-recovery"></a>Azure VM-katastrofåterställning
Nya funktioner i uppdateringen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd har lagts till för Oracle Linux 6.8 och 6,9/7.0 och för UEK5 kernel.
**Linux BRTFS filsystem** | Stöd för virtuella Azure-datorer.
**Virtuella Azure-datorer i tillgänglighetszoner** | Du kan aktivera replikering till en annan region för Azure-datorer som distribuerats i tillgänglighetszoner. Du kan nu aktivera replikering för en Azure-dator och ange mål för redundansväxling till en enda VM-instans, en virtuell dator i en tillgänglighetsuppsättning eller en virtuell dator i en tillgänglighetszon. Inställningen påverkar inte replikering. [Läs](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) meddelandet.
**Brandväggen aktiverad storage (portal/PowerShell)** | Stöd för [brandväggen aktiverad lagringskonton](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Du kan replikera virtuella Azure-datorer med ohanterade diskar till brandväggen aktiverad lagringskonton till en annan Azure-region för haveriberedskap.<br/><br/> Du kan använda brandväggen aktiverad storage-konton som mållagringskonton för ohanterade diskar.<br/><br/> Stöds i portalen och med hjälp av PowerShell.

## <a name="updates-december-2018"></a>Uppdateringar (December 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Automatiska uppdateringar av mobilitetstjänsten (Azure virtuella datorer)

Site Recovery läggs ett alternativ för automatiska uppdateringar till mobilitetstjänsttillägget. Mobilitetstjänsttillägget installeras på varje Azure virtuell dator som replikeras av Site Recovery. När du aktiverar replikering kan välja du om du vill tillåta Site Recovery för att hantera uppdateringar av tillägget.

Uppdateringar kräver inte en omstart av virtuella datorer och påverkar inte replikering. [Läs mer](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Priskalkylator för Azure VM-katastrofåterställning

Disaster Recovery på Azure virtuella datorer medför VM licensieringskostnaderna och kostnader för lagring och nätverk. Azure tillhandahåller en [priskalkylatorn](https://aka.ms/a2a-cost-estimator) kan du ta reda på dessa kostnader. Site Recovery erbjuder nu en [Prisexempel uppskattning](https://aka.ms/a2a-cost-estimator) som priser för en exempeldistribution baserad på en trelagers-app med sex virtuella datorer med 12 Standard HDD-diskar och 6 Premium SSD-diskar.

- Exemplet förutsätter att en dataändring på 10 GB per dag för standard och 20 GB för premium.
- Du kan ändra variabler för att beräkna kostnaderna för din specifika distribution.
- Du kan ange hur många virtuella datorer, antal och typer av hanterade diskar och de förväntade totala dataändringshastighet förväntat över virtuella datorer.
- Du kan dessutom använda en komprimering faktor för att beräkna kostnader för bandbredd.

[Läs](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) meddelandet.


## <a name="updates-october-2018"></a>Uppdateringar (oktober 2018)

### <a name="update-rollup-30"></a>Samlad uppdatering 30 

[Uppdatera samlad 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery-agenter och leverantörer (som beskrivs i samlade).
**Problemet korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i samlade).

#### <a name="azure-vm-disaster-recovery"></a>Azure VM-katastrofåterställning
Nya funktioner i uppdateringen.

**Funktion** | **Detaljer**
--- | ---
**Regionsstöd** | Site Recovery-stöd har lagts till för Australien, centrala 1 och Australien centrala 2.
**Stöd för diskkryptering** | Stöd för haveriberedskap för Azure-datorer som krypterats med Azure Disk Encryption (ADE) med Azure AD-app. [Läs mer](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Disk-undantag** | Oinitierad diskar undantas nu automatiskt vid replikering av virtuella Azure-datorer.
**Brandväggen aktiverad storage (PowerShell)** | Stöd för [brandväggen aktiverad lagringskonton](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Du kan replikera virtuella Azure-datorer med ohanterade diskar till brandväggen aktiverad lagringskonton till en annan Azure-region för haveriberedskap.<br/><br/> Du kan använda brandväggen aktiverad storage-konton som mållagringskonton för ohanterade diskar.<br/><br/> Stöds endast med PowerShell.


### <a name="update-rollup-29"></a>Samlad uppdatering 29 

[Uppdatera samlad 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery-agenter och leverantörer (som beskrivs i samlade).
**Problemet korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i samlade).


## <a name="updates-august-2018"></a>Uppdateringar (augusti 2018)

### <a name="update-rollup-28"></a>Samlad uppdatering 28 

[Uppdatera samlad 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery-agenter och leverantörer (som beskrivs i samlade).
**Problemet korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i samlade).

#### <a name="azure-vms-disaster-recovery"></a>Haveriberedskap för Azure virtuella datorer 
Nya funktioner i uppdateringen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Lagt till stöd för Red Hat Enterprise Linux 6.10; CentOS 6.10.<br/><br/>
**Cloud-support** | Stöd för haveriberedskap för virtuella Azure-datorer i molnet för Tyskland.
**Haveriberedskap mellan prenumerationer** | Stöd för replikering av virtuella Azure-datorer i en region till en annan region i en annan prenumeration inom samma Azure Active Directory-klientorganisation. [Läs mer](https://aka.ms/cross-sub-blog).

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Haveriberedskap för virtuella VMware-datorer/fysiska servern 
Nya funktioner i uppdateringen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd för Red Hat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> Linux-baserade virtuella datorer att använda partitionstypen GUID partition table (GPT) i äldre BIOS-kompatibilitetsläge stöds nu. Granska den [Azure VM vanliga frågor och svar](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) för mer information. 
**Haveriberedskap för virtuella datorer efter migrering** | Stöd för att aktivera återställning till en sekundär region för en lokal VMware VM migrerade till Azure, utan att behöva avinstallera Mobilitetstjänsten-tjänsten på den virtuella datorn innan du aktiverar replikering.
**Windows Server 2008** | Stöd för att migrera datorer som kör Windows Server 2008 R2/2008 64-bitars och 32-bitars.<br/><br/> Migrering endast (replikering och redundans). Återställning efter fel stöds inte.

## <a name="updates-july-2018"></a>Uppdateringar (juli 2018)

### <a name="update-rollup-27-july-2018"></a>Samlad uppdatering 27 (juli 2018)

[Uppdatera samlad 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) innehåller följande uppdateringar.

**Uppdatering** | **Detaljer**
--- | ---
**Leverantörer och agenter** | En uppdatering av Site Recovery-agenter och leverantörer (som beskrivs i samlade).
**Problemet korrigeringar/förbättringar** | Ett antal korrigeringar och förbättringar (som beskrivs i samlade).

#### <a name="azure-vms-disaster-recovery"></a>Haveriberedskap för Azure virtuella datorer 

Nya funktioner i uppdateringen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd för Red Hat Enterprise Linux 7.5.

#### <a name="vmware-vmphysical-server-disaster-recovery"></a>Haveriberedskap för virtuella VMware-datorer/fysiska servern 

Nya funktioner i uppdateringen.

**Funktion** | **Detaljer**
--- | ---
**Linux-support** | Stöd för Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Nästa steg

Håll dig uppdaterad med vår uppdateringar på den [Azure uppdaterar](https://azure.microsoft.com/updates/?product=site-recovery) sidan.
