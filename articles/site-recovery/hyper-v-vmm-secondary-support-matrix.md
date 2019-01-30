---
title: Stöd matrix för haveriberedskap för Hyper-V-datorer i VMM-moln till en sekundär plats med Azure Site Recovery | Microsoft Docs
description: Sammanfattar stödet för replikering av virtuella Hyper-V-datorer i VMM-moln till en sekundär plats med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 4fa8e28d9ccc16482ca937a220eae83e282da7e9
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210045"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Stöd matrix för haveriberedskap för Hyper-V-datorer till en sekundär plats

Den här artikeln sammanfattar vad som stöds när du använder den [Azure Site Recovery](site-recovery-overview.md) -tjänsten för att replikera Hyper-V-datorer hanteras i System Center Virtual Machine Manager (VMM)-moln till en sekundär plats. Om du vill replikera Hyper-V-datorer till Azure kan du läsa [supportmatris för den här](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Du kan bara replikera till en sekundär plats när Hyper-V-värdar hanteras i VMM-moln.

  

## <a name="host-servers"></a>Värdservrar

**Operativsystem** | **Detaljer**
--- | ---
Windows Server 2012 R2 | Servrar måste köra de senaste uppdateringarna.
Windows Server 2016 |  VMM 2016 moln med en blandning av Windows Server 2016 och 2012 R2-värdar stöds inte för närvarande.<br/><br/> Distributioner som uppgraderats från System Center 2012 R2 VMM 2012 R2 till System Center 2016 stöds inte för närvarande.


## <a name="replicated-vm-support"></a>Replikerade support för virtuella datorer

I följande tabell sammanfattas operativsystem för datorer som replikeras med Site Recovery. Alla arbetsbelastningar kan köras på operativsystemet som stöds.

**Windows-version** | **Hyper-V (med VMM)**
--- | ---
Windows Server 2016 | Någon gästen operativsystemet [stöds av Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) på Windows Server 2016 
Windows Server 2012 R2 | Någon gästen operativsystemet [stöds av Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) på Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Lagring för Linux-dator

Endast Linux-datorer med följande storage kan replikeras:

- File system (EXT3, ETX4, ReiserFS, XFS).
- Upptäcka programvara-enheter med mappning.
- Volymhanterare (LVM2).
- Fysiska servrar med HP CCISS controller lagring stöds inte.
- Filsystemet ReiserFS stöds endast på SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Nätverkskonfigurationen - värd/gäst VM

**Konfiguration** | **Stöds**  
--- | --- 
Värd - NIC-teamindelning | Ja 
Host - VLAN | Ja 
Host - IPv4 | Ja 
Host - IPv6 | Nej 
Gäst VM - NIC-teamindelning | Nej
Guest VM - IPv4 | Ja
Gäst VM - IPv6 | Nej
Gäst VM-Windows / Linux - statisk IP-adress | Ja
Gäst VM - med flera nätverkskort | Ja


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Värdlagring

**Lagring (värd)** | **Stöds**
--- | --- 
NFS | Saknas
SMB 3.0 |  Ja
SAN (ISCSI) | Ja
Flera sökvägar (MPIO) | Ja

### <a name="guest-or-physical-server-storage"></a>Gäst eller fysisk lagring

**Konfiguration** | **Stöds**
--- | --- | 
VMDK |  Saknas
VHD/VHDX | Ja (upp till 16 diskar)
Gen 2 VM | Ja
Delad klusterdisk | Nej
Krypterad disk | Nej
UEFI| Saknas
NFS | Nej
SMB 3.0 | Nej
RDM | Saknas
Disk > 1 TB | Ja
Volymen med stripe-disk > 1 TB<br/><br/> LVM | Ja
Lagringsutrymmen | Ja
Frekvent Lägg till/ta bort disk | Nej
Uteslut disk | Ja
Flera sökvägar (MPIO) | Ja

## <a name="vaults"></a>Valv

**Åtgärd** | **Stöds**
--- | --- 
Flytta valv mellan resursgrupper (inom eller mellan prenumerationer) |  Nej
Flytta lagring, nätverk, virtuella Azure-datorer mellan resursgrupper (inom eller mellan prenumerationer) | Nej

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery Provider

Providern samordnar kommunikationen mellan VMM-servrar. 

**senaste** | **Uppdateringar**
--- | --- | --- | --- | ---
5.1.19 ([tillgängliga från portal](https://aka.ms/downloaddra) | [Senaste funktionerna och korrigeringarna](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Nästa steg

[Replikera Hyper-V-datorer i VMM-moln till en sekundär plats](tutorial-vmm-to-vmm.md)

