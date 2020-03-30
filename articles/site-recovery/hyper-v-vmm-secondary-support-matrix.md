---
title: Stöd för matris-Hyper-V-haveriberedskap till en sekundär VMM-plats med Azure Site Recovery
description: Sammanfattar stöd för Hyper-V VM-replikering i VMM-moln till en sekundär plats med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 1126a85ed22ee17879767a93ca75dc76dd04b747
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132958"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Supportmatris för haveriberedskap av virtuella Hyper-V-datorer till en sekundär webbplats

Den här artikeln sammanfattar vad som stöds när du använder [Azure Site Recovery-tjänsten](site-recovery-overview.md) för att replikera virtuella datorer med hyper-vpm som hanteras i VMM-moln (System Center Virtual Machine Manager) till en sekundär plats. Om du vill replikera virtuella hyper-virtuella datorer till Azure läser du [den här supportmatrisen](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Du kan bara replikera till en sekundär plats när dina Hyper-V-värdar hanteras i VMM-moln.


## <a name="host-servers"></a>Värdservrar

**Operativsystem** | **Detaljer**
--- | ---
Windows Server 2012 R2 | Servrarna måste köra de senaste uppdateringarna.
Windows Server 2016 |  VMM 2016-moln med en blandning av Windows Server 2016- och 2012 R2-värdar stöds för närvarande inte.<br/><br/> Distributioner som uppgraderats från System Center 2012 R2 VMM 2012 R2 till System Center 2016 stöds för närvarande inte.


## <a name="replicated-vm-support"></a>Stöd för replikerad virtuell dator

I följande tabell sammanfattas stöd för operativsystem för datorer som replikeras med Platsåterställning. Alla arbetsbelastningar kan köras på det operativsystem som stöds.

**Windows-version** | **Hyper-V (med VMM)**
--- | ---
Windows Server 2016 | Alla gästoperativsystem [som stöds av Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) på Windows Server 2016 
Windows Server 2012 R2 | Alla gästoperativsystem [som stöds av Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) på Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Lagring av Linux-maskiner

Endast Linux-datorer med följande lagring kan replikeras:

- Filsystem (EXT3, ETX4, ReiserFS, XFS).
- Multipath programvara-enhet Mapper.
- Volymhanterare (LVM2).
- Fysiska servrar med HP CCISS-styrenhetslagring stöds inte.
- Filsystemet ReiserFS stöds endast på SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Nätverkskonfiguration - värd/gäst-VM

**Konfiguration** | **Stöds**  
--- | --- 
Värd - NIC-teaming | Ja 
Värd - VLAN | Ja 
Värd - IPv4 | Ja 
Värd - IPv6 | Inga 
Gäst-VM - NIC-teaming | Inga
Gäst VM - IPv4 | Ja
Gäst VM - IPv6 | Inga
Gäst-VM - Windows/Linux - Statisk IP-adress | Ja
Gäst VM - Multi-NIC | Ja


## <a name="storage"></a>Lagring

### <a name="host-storage"></a>Värdlagring

**Lagring (värd)** | **Stöds**
--- | --- 
NFS | Ej tillämpligt
SMB 3.0 |  Ja
SAN (ISCSI) | Ja
Flera sökvägar (MPIO) | Ja

### <a name="guest-or-physical-server-storage"></a>Gäst- eller fysiskserverlagring

**Konfiguration** | **Stöds**
--- | --- | 
Vmdk |  Ej tillämpligt
VHD/VHDX | Ja (upp till 16 diskar)
Gen 2 VM | Ja
Delad klusterdisk | Inga
Krypterad disk | Inga
Uefi| Ej tillämpligt
NFS | Inga
SMB 3.0 | Inga
Rdm | Ej tillämpligt
Disk > 1 TB | Ja
Volym med randig disk > 1 TB<br/><br/> Lvm | Ja
Lagringsutrymmen | Ja
Snabb lägg till/ta bort disk | Inga
Uteslut disk | Ja
Flera sökvägar (MPIO) | Ja

## <a name="vaults"></a>Valv

**Åtgärd** | **Stöds**
--- | --- 
Flytta valv mellan resursgrupper (inom eller mellan prenumerationer) |  Inga
Flytta lagring, nätverk, virtuella Azure-datorer mellan resursgrupper (inom eller mellan prenumerationer) | Inga

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery Provider

Providern samordnar kommunikationen mellan VMM-servrar. 

**Senaste** | **Uppdateringar**
--- | --- 
5.1.19[(tillgänglig från portalen](https://aka.ms/downloaddra) | [Senaste funktionerna och korrigeringar](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Nästa steg

[Replikera virtuella hyper-v-datorer i VMM-moln till en sekundär plats](tutorial-vmm-to-vmm.md)

