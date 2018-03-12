---
title: "Stöd matrix för replikering av Hyper-V virtuella datorer i VMM-moln till en sekundär plats med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattar stöd för replikering av Hyper-V-dator i VMM-moln till en sekundär plats med Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: 767b0e76b73c082ddb75374f51700b85272f713e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-replication-of-hyper-v-vms-to-a-secondary-site"></a>Stöd matrix för replikering av Hyper-V virtuella datorer till en sekundär plats

Den här artikeln sammanfattar vad stöds när du använder den [Azure Site Recovery](site-recovery-overview.md) -tjänsten för att replikera virtuella Hyper-V-datorer hanteras i System Center Virtual Machine Manager (VMM)-moln till en sekundär plats. Om du vill replikera virtuella Hyper-V-datorer till Azure, granska [stödmatrisen](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Du kan bara replikera till en sekundär plats när Hyper-V-värdar hanteras i VMM-moln.

  

## <a name="host-servers"></a>Värdservrar för fjärrskrivbordssessioner

**Operativsystem** | **Detaljer**
--- | ---
Windows Server 2012 R2 | Servrar måste köra de senaste uppdateringarna.
Windows Server 2016 |  VMM 2016 moln med en blandning av Windows Server 2016 och 2012 R2-värdar stöds inte för närvarande.<br/><br/> Distributioner som uppgraderats från System Center 2012 R2 VMM 2012 R2 till System Center 2016 stöds inte för närvarande.


## <a name="replicated-vm-support"></a>Replikerade VM-support

I följande tabell sammanfattas operativsystem för datorer som replikeras med Site Recovery. Alla arbetsbelastningar som kan köras på operativsystem som stöds.

**Windows-version** | **Hyper-V (med VMM)**
--- | ---
Windows Server 2016 | Någon gästen operativsystemet [stöds av Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) på Windows Server 2016 
Windows Server 2012 R2 | Någon gästen operativsystemet [stöds av Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) på Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Lagring av Linux-dator

Linux-baserade datorer med följande lagringen kan replikeras:

- Filsystem (EXT3, ETX4, ReiserFS, XFS).
- Mappning för flera sökvägar programvara-enhet.
- Volymhanterare (LVM2).
- Fysiska servrar med HP CCISS domänkontrollant lagring stöds inte.
- Filsystemet ReiserFS stöds endast för SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Nätverkskonfigurationen - värd/gäst VM

**Konfiguration** | **Stöds**  
--- | --- 
Värd - NIC-teamindelning | Ja 
Värd - VLAN | Ja 
Värd - IPv4 | Ja 
Värd - IPv6 | Nej 
Gäst VM - NIC-teamindelning | Nej
Gäst-VM - IPv4 | Ja
Gäst-VM - IPv6 | Nej
Gues VM-Windows / Linux - statisk IP-adress | Ja
Gäst VM - flera nätverkskort | Ja


## <a name="storage"></a>Lagring

### <a name="host-storage"></a>Värd storage

**Lagring (värd)** | **Stöds**
--- | --- 
NFS | Gäller inte
SMB 3.0 |  Ja
SAN (ISCSI) | Ja
Multipath (MPIO) | Ja

### <a name="guest-or-physical-server-storage"></a>Gästen eller fysisk serverlagring

**Konfiguration** | **Stöds**
--- | --- | 
VMDK |  Gäller inte
VHD/VHDX | Ja (upp till 16 diskar)
Generation 2 VM | Ja
Delad klusterdisk | Nej
Krypterade disk | Nej
UEFI| Gäller inte
NFS | Nej
SMB 3.0 | Nej
RDM | Gäller inte
Disk > 1 TB | Ja
Volymen med stripe disk > 1 TB<br/><br/> LVM | Ja
Lagringsutrymmen | Ja
Varm Lägg till/ta bort disken | Nej
Uteslut disk | Ja
Multipath (MPIO) | Ja

## <a name="vaults"></a>Valv

**Åtgärd** | **Stöds**
--- | --- 
Flytta valv mellan resursgrupper (inom eller mellan prenumerationer) |  Nej
Flytta lagring, nätverk, virtuella datorer i Azure över resursgrupper (inom eller mellan prenumerationer) | Nej

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery Provider

Providern samordnar kommunikationen mellan VMM-servrar. 

**senaste** | **Uppdateringar**
--- | --- | --- | --- | ---
5.1.19 ([tillgänglig från portalen](http://aka.ms/downloaddra) | [Senaste funktionerna och korrigeringarna](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Nästa steg

[Replikera virtuella Hyper-V-datorer i VMM-moln till en sekundär plats](tutorial-vmm-to-vmm.md)

