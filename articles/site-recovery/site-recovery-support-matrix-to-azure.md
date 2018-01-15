---
title: "Azure Site Recovery stöd matrix för att replikera till Azure | Microsoft Docs"
description: "Sammanfattning av de operativsystem som stöds och komponenter för Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: rajanaki
ms.openlocfilehash: 98f3b1fe5a0f1d7518e8f0ef6f2a478f59559139
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2018
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>Azure Site Recovery stöd matrix för replikering från lokal till Azure


Den här artikeln beskriver konfigurationer som stöds och komponenter för Azure Site Recovery när replikering och återställa till Azure. Mer information om krav för Azure Site Recovery finns i [krav](site-recovery-prereq.md).

> [!NOTE]
> Se till att du uppdaterar till den senaste versionen av Site Recovery-providern och agenten för att uppnå kompatibilitet med uppdateringar i supportmatrisen.


## <a name="support-for-deployment-options"></a>Stöd för distributionsalternativ

**Distribution** | **VMware/fysisk server** | **Hyper-V (med/utan Virtual Machine Manager)** |
--- | --- | ---
**Azure-portalen** | Lokala virtuella VMware-datorer till Azure-lagring med Azure Resource Manager eller klassiska lagring och nätverk.<br/><br/> Växling till Resource Manager-baserade eller klassiska virtuella datorer. | Lokal Hyper-V virtuella datorer till Azure-lagring med Resource Manager eller klassiska lagring och nätverk.<br/><br/> Växling till Resource Manager-baserade eller klassiska virtuella datorer.
**Klassisk portal** | Underhållsläge. Det går inte att skapa nya valv. | Underhållsläge.
**PowerShell** | Stöds | Stöds


## <a name="support-for-datacenter-management-servers"></a>Stöd för datacenter hanteringsservrar

### <a name="virtualization-management-entities"></a>Virtualisering hantering av enheter

**Distribution** | **Support**
--- | ---
**VMware-dator/fysisk server** | vCenter 6.5, 6.0 eller 5.5
**Hyper-V (med Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 och System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > Ett System Center Virtual Machine Manager 2016 moln med en blandning av Windows Server 2016 och 2012 R2-värdar stöds inte för närvarande.
  > Konfigurationer för exempelvis uppgradering av en befintlig SCVMM 2012 R2 till 2016 stöds inte för närvarande.
### <a name="host-servers"></a>Värdservrar för fjärrskrivbordssessioner

**Distribution** | **Support**
--- | ---
**VMware-dator/fysisk server** | vSphere 6.5, 6.0, 5.5
**Hyper-V (med/utan Virtual Machine Manager)** | Windows Server 2016, Windows Server 2012 R2 med de senaste uppdateringarna.<br></br>Om SCVMM används ska Windows Server 2016 värdar hanteras av SCVMM 2016.


  >[!Note]
  >En Hyper-V-plats som kombinerar värdar som kör Windows Server 2016 och 2012 R2 stöds inte för närvarande. Återställning till en alternativ plats för virtuella datorer på en Windows Server 2016-värden stöds inte för närvarande.

## <a name="support-for-replicated-machine-os-versions"></a>Stöd för replikerade datorn OS-versioner

Virtuella datorer som skyddas måste uppfylla [krav för Azure](#failed-over-azure-vm-requirements) när du replikerar till Azure.
I följande tabell sammanfattas replikerade operativsystem i olika distributionsscenarier när du använder Azure Site Recovery. Detta stöd gäller för alla arbetsbelastningar som körs på nämnda OS.

 **VMware/fysisk server** | **Hyper-V (med eller utan VMM)** |
--- | --- |
64-bitars Windows Server 2016 (Server Core, Server med Skrivbordsmiljö)\*, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med på minst SP1<br/><br/> Red Hat Enterprise Linux: 5.2 till 5.11, 6.1 6,9, 7.0 7.4<br/><br/>CentOS: 5.2 till 5.11, 6.1 6,9, 7.0 7.4 <br/><br/>Ubuntu 14.04 LTS server[ (kernel-versioner som stöds)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS server[ (kernel-versioner som stöds)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4, 6.5 Red Hat kompatibel kernel eller Unbreakable Enterprise Kernel version 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(Uppgradering av replikera datorer från SLES 11 SP3 till SLES 11 SP4 stöds inte. Om en replikerad dator har uppgraderats från SLES 11SP3 till SLES 11 SP4, måste du du inaktivera replikering och skydda datorn igen efter uppgraderingen.) | Alla gästoperativsystemet [stöds av Azure](https://technet.microsoft.com/library/cc794868.aspx)

>[!NOTE]
>
> \*Windows Server 2016 Nano Server stöds inte.
>
> På Linux-distributioner stöds bara lager kernlar som är en del av delversion versionen/uppdatering av distributionen.
>
> Uppgraderingar i större versioner av Linux-distribution på en Azure Site Recovery skyddad virtuell VMware-dator eller fysisk server stöds inte. Inaktivera replikering för datorn vid uppgradering av operativsystemet över viktiga versioner (till exempel CentOS 6.* till CentOS 7.*), uppgradera operativsystemet på datorn och sedan aktivera replikering igen.
> 


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>Ubuntu kernel-versioner som stöds för VMware/fysiska servrar

**Versionen** | **Mobilitetstjänstversionen** | **Kernel-version** |
--- | --- | --- |
14.04 LTS | 9.10 | 3.13.0-24-Generic till 3.13.0-121-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-Generic till 3.13.0-128-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-Generic till 3.13.0-132-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-Generic till 3.13.0-137-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-104-generic |
16.04 LTS | 9.10 | 4.4.0-21-Generic till 4.4.0-81-generic,<br/>4.8.0-34-Generic till 4.8.0-56-generic,<br/>4.10.0-14-Generic till 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-Generic till 4.4.0-91-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-Generic till 4.4.0-96-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-Generic till 4.4.0-104-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Filsystem som stöds och Gäst lagringskonfigurationer på Linux (VMware/fysiska servrar)

Följande filsystem och program för konfiguration av lagring stöds på Linux-servrar som körs på VMware eller fysiska servrar:
* Filsystem: ext3 ext4, ReiserFS (Suse Linux Enterprise Server bara), XFS
* Volymhanterare: LVM2
* Programvara för flera sökvägar: enheten Mapper

Paravirtualized lagringsenheter (exporteras av paravirtualized drivrutiner) stöds inte.<br/>
Flera kön blockera-i/o-enheter stöds inte.<br/>
Fysiska servrar med lagringsstyrenheten HP CCISS stöds inte.<br/>

>[!Note]
> På Linux-servrar till följande kataloger (om konfigurerad som separata partitioner /-filsystem) måste vara på samma disk (OS-disk) på källservern: / (rot), / Boot/usr, /usr/local, /var, / etc; / Boot bör och på en diskpartition inte vara en LVM volym<br/><br/>
>


## <a name="support-for-network-configuration"></a>Stöd för konfigurering av nätverk
Följande tabeller sammanfattar stöd för konfiguration av nätverk i olika distributionsscenarier som använder Azure Site Recovery replikera till Azure.

### <a name="host-network-configuration"></a>Nätverkskonfiguration för värden

**Konfiguration** | **VMware/fysisk server** | **Hyper-V (med/utan Virtual Machine Manager)**
--- | --- | ---
NIC-teamindelning | Ja<br/><br/>Stöds inte när fysiska datorer replikeras| Ja
VLAN | Ja | Ja
IPv4 | Ja | Ja
IPv6 | Nej | Nej

### <a name="guest-vm-network-configuration"></a>Konfiguration för gäst-VM-nätverk

**Konfiguration** | **VMware/fysisk server** | **Hyper-V (med/utan Virtual Machine Manager)**
--- | --- | ---
NIC-teamindelning | Nej | Nej
IPv4 | Ja | Ja
IPv6 | Nej | Nej
Statisk IP-adress (Windows) | Ja | Ja
Statisk IP-adress (Linux) | Ja <br/><br/>Virtuella datorer är konfigurerade för att använda DHCP på återställning efter fel  | Nej
Flera nätverkskort | Ja | Ja

### <a name="failed-over-azure-vm-network-configuration"></a>Det gick inte över Azure VM nätverkskonfigurationen

**Azure-nätverk** | **VMware/fysisk server** | **Hyper-V (med/utan Virtual Machine Manager)**
--- | --- | ---
Express Route | Ja | Ja
ILB | Ja | Ja
ELB | Ja | Ja
Traffic Manager | Ja | Ja
Flera nätverkskort | Ja | Ja
Reserverad IP | Ja | Ja
IPv4 | Ja | Ja
Behåll käll-IP | Ja | Ja
Virtuella nätverksslutpunkter (Azure Storage brandväggar och virtuella nätverk) | Nej | Nej


## <a name="support-for-storage"></a>Stöd för lagring
Följande tabeller sammanfattar stöd för konfiguration av lagringsutrymme i olika distributionsscenarier som använder Azure Site Recovery replikera till Azure.

### <a name="host-storage-configuration"></a>Värdkonfiguration för lagring

**Konfiguration** | **VMware/fysisk server** | **Hyper-V (med/utan Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Ja för VMware<br/><br/> Nej för fysiska servrar | Gäller inte
SMB 3.0 | Gäller inte | Ja
SAN (ISCSI) | Ja | Ja
Multipath (MPIO)<br></br>Testats med: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM för CLARiiON | Ja | Ja

### <a name="guest-or-physical-server-storage-configuration"></a>Gäst eller fysisk server lagringskonfigurationen

**Konfiguration** | **VMware/fysisk server** | **Hyper-V (med/utan Virtual Machine Manager)**
--- | --- | ---
VMDK | Ja | Gäller inte
VHD-ELLER VHDX | Gäller inte | Ja
Generation 2 VM | Gäller inte | Ja
EFI/UEFI| Nej | Ja
Delad klusterdisk | Nej | Nej
Krypterade disk | Nej | Nej
NFS | Nej | Gäller inte
SMB 3.0 | Nej | Nej
RDM | Ja<br/><br/> Ej tillämpligt för fysiska servrar | Gäller inte
Disk > 1 TB | Ja<br/><br/>Upp till 4095 GB | Ja<br/><br/>Upp till 4095 GB
Disk med 4K logisk och 4 k fysisk sektorstorlek | Ja | Stöds inte för Generation 1 virtuella datorer<br/><br/>Stöds inte för Generation 2 virtuella datorer.
Disk med 4K logisk och fysisk sektorstorlek för 512 byte | Ja |  Ja
Volymen med stripe disk > 1 TB<br/><br/> Hantering av LVM logiska volymer | Ja | Ja
Lagringsutrymmen | Nej | Ja
Varm Lägg till/ta bort disken | Nej | Nej
Uteslut disk | Ja | Ja
Multipath (MPIO) | Gäller inte | Ja

**Azure Storage** | **VMware/fysisk server** | **Hyper-V (med/utan Virtual Machine Manager)**
--- | --- | ---
LRS | Ja | Ja
GRS | Ja | Ja
RA-GRS | Ja | Ja
Lågfrekvent | Nej | Nej
Frekvent| Nej | Nej
Blockblob-objekt | Nej | Nej
Kryptering i rest(SSE)| Ja | Ja
Premium Storage | Ja | Ja
Import/export service | Nej | Nej
Virtuella nätverksslutpunkter (Azure Storage brandväggar och virtuella nätverk) konfigurerats på mål-lagringskontot konto eller cachelagra storage-konto som används för att lagra data för replikering | Nej | Nej
Generella V2 storage-konton (både frekvent och lågfrekvent nivå) | Nej | Nej


## <a name="support-for-azure-compute-configuration"></a>Stöd för Azure compute-konfiguration

**Compute-funktion** | **VMware/fysisk server** | **Hyper-V (med/utan Virtual Machine Manager)**
--- | --- | ---
Tillgänglighetsuppsättningar | Ja | Ja
HUBBEN | Ja | Ja  
Hanterade diskar | Ja | Ja<br/><br/>Återställning till det lokala från Azure VM med hanterade diskar stöds inte för närvarande.

## <a name="failed-over-azure-vm-requirements"></a>Det gick inte över Azure VM krav

Du kan distribuera Site Recovery för att replikera virtuella datorer och fysiska servrar som kör ett operativsystem som stöds av Azure. Detta omfattar de flesta versioner av Windows och Linux. Lokala virtuella datorer som du vill replikera måste överensstämma med följande krav för Azure vid replikering till Azure.

**Entitet** | **Krav** | **Detaljer**
--- | --- | ---
**Gästoperativsystemet** | Hyper-V till Azure-replikering: Site Recovery har stöd för alla operativsystem som är [stöds av Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> För VMware och fysiska servrar replikering: Kontrollera Windows- och Linux [krav](site-recovery-vmware-to-azure-classic.md) | Kravkontrollen misslyckas om stöds inte.
**Gästen operativsystemets arkitektur** | 64-bitars | Kravkontrollen misslyckas om stöds inte
**Operativsystemdisken** | Upp till 2 048 GB om du replikerar **virtuella VMware-datorer eller fysiska servrar till Azure**.<br/><br/>Upp till 2048 GB för **Hyper-V Generation 1** virtuella datorer.<br/><br/>Upp till 300 GB för **Hyper-V Generation 2 virtuella datorer**.  | Kravkontrollen misslyckas om stöds inte
**Operativsystemet disk antal** | 1 | Kravkontrollen misslyckas om stöds inte.
**Datadiskar** | 64 eller mindre om du replikerar **virtuella VMware-datorer till Azure**; 16 eller mindre om du replikerar **Hyper-V virtuella datorer till Azure** | Kravkontrollen misslyckas om stöds inte
**Datadisken för virtuell Hårddisk** | Upp till 4095 GB | Kravkontrollen misslyckas om stöds inte
**Nätverkskort** | Flera nätverkskort som stöds |
**Delad virtuell Hårddisk** | Stöds inte | Kravkontrollen misslyckas om stöds inte
**FC-disk** | Stöds inte | Kravkontrollen misslyckas om stöds inte
**Format för hårddisk** | VIRTUELL HÅRDDISK <br/><br/> VHDX | Även om VHDX inte stöds för närvarande i Azure, konverterar Site Recovery automatiskt VHDX till virtuell Hårddisk när du redundansväxlar till Azure. När du växlar tillbaka till lokala virtuella datorer att fortsätta att använda VHDX-format.
**BitLocker** | Stöds inte | BitLocker måste inaktiveras innan du skyddar en virtuell dator.
**Namn på virtuell dator** | Mellan 1 och 63 tecken. Begränsat till bokstäver, siffror och bindestreck. VM-namn måste börja och sluta med en bokstav eller siffra. | Uppdatera värdet i egenskaperna för virtuella datorn i Site Recovery.
**VM-typ** | Generation 1<br/><br/> Generation 2 – Windows | Generation 2 virtuella datorer med en OS-disktyp av grundläggande (som innehåller en eller två datavolymer som formaterats som VHDX) och mindre än 300 GB diskutrymme stöds.<br></br>Linux Generation 2 virtuella datorer stöds inte. [Läs mer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Stöd för åtgärder som Recovery Services-valvet

**Åtgärd** | **VMware/fysisk server** | **Hyper-V (ingen Virtual Machine Manager)** | **Hyper-V (med Virtual Machine Manager)**
--- | --- | --- | ---
Flytta valvet mellan resursgrupper<br/><br/> Inom och över prenumerationer | Nej | Nej | Nej
Flytta lagring, nätverk, virtuella datorer i Azure över resursgrupper<br/><br/> Inom och över prenumerationer | Nej | Nej | Nej


## <a name="support-for-provider-and-agent"></a>Stöd för providern och agenten

**Namn** | **Beskrivning** | **Senaste versionen** | **Detaljer**
--- | --- | --- | --- | ---
**Azure Site Recovery-providern** | Samordnar kommunikationen mellan lokala servrar och Azure <br/><br/> Installerad på lokala Virtual Machine Manager-servrar eller på Hyper-V-servrar om det finns ingen Virtual Machine Manager-server | 5.1.2700.1 (tillgänglig från portalen) | [Senaste funktionerna och korrigeringarna](https://aka.ms/latest_asr_updates)
**Azure Site Recovery Unified installationsprogram (VMware till Azure)** | Samordnar kommunikationen mellan lokala VMware-servrar och Azure <br/><br/> Installerad på lokal VMware-servrar | 9.12.4653.1 (tillgänglig från portalen) | [Senaste funktionerna och korrigeringarna](https://aka.ms/latest_asr_updates)
**Mobilitetstjänsten** | Samordnar replikering mellan lokala VMware-servrar/fysiska servrar och Azure/sekundär plats<br/><br/> Installerad på VMware VM eller fysiska servrar som du vill replikera  | 9.12.4653.1 (tillgänglig från portalen) | [Senaste funktionerna och korrigeringarna](https://aka.ms/latest_asr_updates)
**Microsoft Azure Recovery Services MARS-agenten** | Samordnar replikering mellan Hyper-V virtuella datorer och Azure<br/><br/> Installerad på lokala Hyper-V-servrar (med eller utan en Virtual Machine Manager-server) | Senaste agenten (tillgänglig från portalen) |






## <a name="next-steps"></a>Nästa steg
[Kontrollera krav](site-recovery-prereq.md)
