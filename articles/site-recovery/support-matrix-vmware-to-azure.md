---
title: "Azure Site Recovery stöd matrix för att replikera virtuella VMware-datorer och fysiska servrar till Azure | Microsoft Docs"
description: "Sammanfattar de operativsystem som stöds och komponenter för att replikera virtuella VMware-datorer till Azure med Azure Site Recovery-tjänsten."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 01/11/2018
ms.author: raynew
ms.openlocfilehash: 857bbd42fda4abddd9a7551f4de016cecae03868
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Stöd matrix för VMware och fysiska servrar replikering till Azure


Den här artikeln sammanfattar stöds komponenter och inställningar för katastrofåterställning av virtuella VMware-datorer till Azure med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.


## <a name="supported-scenarios"></a>Scenarier som stöds

**Scenario** | **Detaljer** 
--- | --- 
**Virtuella VMware-datorer** | Du kan utföra katastrofåterställning till Azure för lokala virtuella VMware-datorer. Du kan distribuera det här scenariot i Azure-portalen eller med hjälp av PowerShell.
**Fysiska servrar** | Du kan utföra katastrofåterställning till Azure för lokala Windows-/ Linux fysiska servrar. Du kan distribuera det här scenariot i Azure-portalen.

## <a name="on-premises-virtualization-servers"></a>Lokala virtualiseringsservrar

**Server** | **Krav** | **Detaljer**
--- | --- | ---
**VMware** | vCenter Server 6.5 6.0 eller 5.5 eller vSphere 6.5, 6.0, 5.5 | Vi rekommenderar att du använder en vCenter-server
**Fysiska servrar** | Ej tillämpligt


## <a name="replicated-machines"></a>Replikerade datorer

I följande tabell sammanfattas replication stöd för datorer. Site Recovery har stöd för replikering av alla arbetsbelastningar som körs på en dator med ett operativsystem som stöds.

**Komponent** | **Detaljer**
--- | ---
Konfigurationen av datorn | Datorer som replikeras till Azure måste uppfylla [krav för Azure](#failed-over-azure-vm-requirements).
Datorn operativsystem (Windows) | 64-bitars Windows Server 2016 (Server Core, Server med Skrivbordsmiljö)\*, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med på minst SP1
Operativsystemets (Linux) | Red Hat Enterprise Linux: 5.2 till 5.11, 6.1 6,9, 7.0 7.3 <br/><br/>CentOS: 5.2 till 5.11, 6.1 6,9, 7.0 7.3 <br/><br/>Ubuntu 14.04 LTS server[ (kernel-versioner som stöds)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS server[ (kernel-versioner som stöds)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4, 6.5 Red Hat kompatibel kernel eller Unbreakable Enterprise Kernel version 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(Uppgradering av replikera datorer från SLES 11 SP3 till SLES 11 SP4 stöds inte. Om en replikerad dator har uppgraderats från SLES 11SP3 till SLES 11 SP4, måste du du inaktivera replikering och skydda datorn igen efter uppgraderingen.)
Linux kernel-version | Red Hat Enterprise Linux Server 7 + och CentOS 7 + servrar stöds kernel version 3.10.0-514 på version 9.8 av Azure Site Recovery mobilitetstjänsten från.<br/><br/> Kunder med en version av mobilitetstjänsten som är lägre än versionen 9,8 3.10.0-514 kernel krävs för att inaktivera replikering, uppdatera versionen av mobilitetstjänsten till version 9.8 och sedan aktivera replikering igen.


### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel versions


**Versionen som stöds** | **Mobilitetstjänstversionen** | **Kernel-version** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-Generic till 3.13.0-117-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-Generic till 3.13.0-121-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-Generic till 3.13.0-128-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-Generic till 3.13.0-132-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-96-generic |
16.04 LTS | 9.10 | 4.4.0-21-Generic till 4.4.0-81-generic,<br/>4.8.0-34-Generic till 4.8.0-56-generic,<br/>4.10.0-14-Generic till 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-Generic till 4.4.0-91-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-Generic till 4.4.0-96-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-35-generic |

## <a name="linux-file-systemsguest-storage-configurations"></a>Linux-filen System/gäst lagringskonfigurationer

**Komponent** | **Stöds**
--- | ---
Filsystem | ext3, ext4 ReiserFS (endast Suse Linux Enterprise Server), XFS
Volymhanterare | LVM2
Programvara för flera sökvägar | Enheten Mapper
Paravirtualized lagringsenheter | Enheter som exporteras av paravirtualized drivrutiner som inte stöds.
Flera kön blockera-i/o-enheter | Stöds ej.
Fysiska servrar med lagringsstyrenheten HP CCISS | Stöds ej.
Kataloger | Dessa kataloger (om konfigurerad som separata partitioner /-filsystem) måste vara på samma OS-disk på källservern: / (rot)-/ Boot/usr, /usr/local, /var, / etc
XFSv5 | XFSv5 funktioner på XFS filsystem, till exempel kontrollsumma metadata stöds från version 9.10 mobilitetstjänsten och senare. Använda verktyget xfs_info för att kontrollera XFS superblock för partitionen. Om ftype har angetts till 1, är funktioner för XFSv5 används.



## <a name="network"></a>Nätverk

**Komponent** | **Stöds** 
--- | --- 
Värden nätverket NIC-teamindelning | Stöd för virtuella VMware-datorer <br/><br/>Stöds inte för replikeringen av den fysiska datorn
Värd för nätverk VLAN | Ja 
Värd för nätverket IPv4 | Ja 
Värd för nätverket IPv6 | Nej 
Gästen/server network NIC-teamindelning | Nej 
Gäst-server-nätverk IPv4 | Ja 
Gäst-server-nätverk IPv6 | Nej 
Gästen/server network statiska IP-Adressen (Windows) | Ja 
Gästen/server network statiska IP-Adressen (Linux) | Ja <br/><br/>Virtuella datorer är konfigurerade för att använda DHCP på återställning efter fel  
Gästen/server network flera nätverkskort | Ja 


## <a name="azure-vm-network-after-failover"></a>Azure VM-nätverk (efter växling vid fel)

**Komponent** | **Stöds** 
--- | --- 
Express Route | Ja 
ILB | Ja 
ELB | Ja 
Traffic Manager | Ja 
Flera nätverkskort | Ja 
Reserverad IP-adress | Ja 
IPv4 | Ja 
Behåll källans IP-adress | Ja 
Slutpunkter för virtuellt nätverk<br/><br/> (Azure storage brandväggar och Vnet) | Nej 


## <a name="storage"></a>Lagring


**Komponent** | **Stöds** 
--- | --- 
Värden NFS | Ja för VMware<br/><br/> Nej för fysiska servrar 
SAN-nätverk (ISCSI) värd | Ja
Värden Multipath (MPIO) | Ja - testats med: Microsoft DSM, EMC PowerPath 5.7 SP4 EMC PowerPath DSM för CLARiiON
Gästen/server VMDK | Ja 
Gästen/server EFI/UEFI| Nej
Delad klusterdisk för gäst-server | Nej 
Krypterade disk i gäst-server | Nej 
Gästen/server NFS | Nej 
Gästen/server SMB 3.0 | Nej
Gästen/server RDM | Ja<br/><br/> Ej tillämpligt för fysiska servrar 
Gästen/server disk > 1 TB | Ja<br/><br/>Upp till 4095 GB 
Gästen/server disk med 4K logisk och 4 k fysisk sektorstorlek | Ja
Gästen/server-disk med 4K logisk och fysisk sektorstorlek för 512 byte | Ja 
Gästen/server-volym med stripe disk > 1 TB<br/><br/> Hantering av LVM logiska volymer gäst/server - lagringsutrymmen | Ingen disk har varm Lägg till/ta bort gäst-server | Ingen gäst/server - utesluta disk | Ja Multipath för gäst-server (MPIO) | EJ TILLÄMPLIGT 

## <a name="azure-storage"></a>Azure-lagring

**Komponent** | **Stöds** 
--- | --- 
LRS | Ja 
GRS | Ja 
RA-GRS | Ja 
Lågfrekvent | Nej 
Frekvent| Nej 
Blockblob-objekt | Nej 
Kryptering i rest(SSE)| Ja 
Premium Storage | Ja 
Import/export service | Nej 
Slutpunkter för virtuellt nätverk<br/><br/> Azure storage brandväggar och Vnet som konfigurerats på mål-storage-cache lagringskontot (används för att lagra data för replikering) | Nej 
Generella V2 storage-konton (både frekvent och lågfrekvent nivå) | Nej 


## <a name="azure-compute"></a>Azure-beräkning

**Featuree** | **Stöds** 
--- | --- 
Tillgänglighetsuppsättningar | Ja 
HUB | Ja   
Hanterade diskar | Ja 

## <a name="azure-vm-requirements"></a>Krav för Azure VM

Lokala virtuella datorer som du replikerar till Azure måste uppfylla kraven för Azure VM som sammanfattas i den här tabellen.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
**Gästoperativsystemet** | Kontrollera [operativsystem som stöds](#replicated machines) | Kravkontroll misslyckas om stöds inte.
**Gästen operativsystemets arkitektur** | 64-bitars | Kravkontroll misslyckas om stöds inte
**Operativsystemdisken** | Upp till 2 048 GB | Kravkontroll misslyckas om stöds inte
**Operativsystemet disk antal** | 1 | Kravkontrollen misslyckas om stöds inte.
**Datadiskar** | 64 eller mindre om du replikerar **virtuella VMware-datorer till Azure**; 16 eller mindre om du replikerar **Hyper-V virtuella datorer till Azure** | Kravkontrollen misslyckas om stöds inte
**Datadisken för virtuell Hårddisk** | Upp till 4095 GB | Kravkontroll misslyckas om stöds inte
**Nätverkskort** | Flera nätverkskort som stöds | 
**Delad virtuell Hårddisk** | Stöds inte | Kravkontroll misslyckas om stöds inte
**FC-disk** | Stöds inte | Kravkontroll misslyckas om stöds inte
**Format för hårddisk** | VHD <br/><br/> VHDX | Även om VHDX inte stöds för närvarande i Azure, konverterar Site Recovery automatiskt VHDX till virtuell Hårddisk när du redundansväxlar till Azure. När du växlar tillbaka till lokala virtuella datorer att fortsätta att använda VHDX-format.
**Bitlocker** | Stöds inte | BitLocker måste inaktiveras innan du aktiverar replikering för en dator.
**Namn på virtuell dator** | 1 - 63 tecken.<br/><br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Datornamnet måste börja och sluta med en bokstav eller siffra. | Uppdatera värdet i datoregenskaperna i Site Recovery.
**VM-typ** | Generation 1<br/><br/> Generation 2 – Windows | Generation 2 virtuella datorer med en OS-disktyp av grundläggande (som innehåller en eller två datavolymer som formaterats som VHDX) och mindre än 300 GB diskutrymme stöds.<br></br>Linux Generation 2 virtuella datorer stöds inte. [Läs mer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="vault-tasks"></a>Valvet uppgifter

**Åtgärd** | **Stöds** 
--- | --- 
Flytta valvet mellan resursgrupper<br/><br/> Inom och över prenumerationer | Nej 
Flytta lagring, nätverk, virtuella datorer i Azure över resursgrupper<br/><br/> Inom och över prenumerationer | Nej 


## <a name="mobility-service"></a>Mobilitetstjänsten

**Namn** | **Beskrivning** | **Senaste versionen** | **Detaljer**
--- | --- | --- | --- | ---
** Azure Site Recovery enhetlig installation ** | Samordnar kommunikationen mellan lokala VMware-servrar och Azure <br/><br/> Installerad på lokal VMware-servrar | 9.12.4653.1 (tillgänglig från portalen) | [Senaste funktionerna och korrigeringarna](https://aka.ms/latest_asr_updates)
**Mobilitetstjänsten** | Samordnar replikering mellan lokala VMware-servrar/fysiska servrar och Azure/sekundär plats<br/><br/> Installerad på VMware VM eller fysiska servrar som du vill replikera  | 9.12.4653.1 (tillgänglig från portalen) | [Senaste funktionerna och korrigeringarna](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>Nästa steg
[Lär dig hur](tutorial-prepare-azure.md) att förbereda Azure för återställning av virtuella VMware-datorer.
