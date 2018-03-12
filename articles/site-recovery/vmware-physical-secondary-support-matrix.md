---
title: "Stöd matrix för replikering av virtuella VMware-datorer eller fysiska servrar till en sekundär VMware-plats med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattar stöd för VMware/fysisk server-replikering till en sekundär plats med Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: b51a4573ad7a8461b7261f08d94639d2030492d9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-replication-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Stöd matrix för replikering av virtuella VMware-datorer och fysiska servrar till en sekundär plats

Den här artikeln sammanfattar vad stöds när du använder den [Azure Site Recovery](site-recovery-overview.md) -tjänsten för att replikera virtuella VMware-datorer eller Windows-/ Linux fysiska servrar till en sekundär plats för VMware.

- Om du vill replikera virtuella VMware-datorer eller fysiska servrar till Azure, granska [stödmatrisen](vmware-physical-azure-support-matrix.md).
- Om du vill replikera virtuella Hyper-V-datorer till en sekundär plats, granskar [stödmatrisen](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikeringen av lokala virtuella VMware-datorer och fysiska servrar som InMage Scout. InMage Scout ingår i prenumeration på Azure Site Recovery-tjänsten.


## <a name="host-servers"></a>Värdservrar för fjärrskrivbordssessioner

**Operativsystem** | **Detaljer**
--- | ---
vCenter server | vCenter 5.5, 6.0 och 6.5<br/><br/> Om du kör 6.0 eller 6.5, Observera att endast 5.5 funktioner som stöds.


## <a name="replicated-vm-support"></a>Replikerade VM-support

I följande tabell sammanfattas operativsystem för datorer som replikeras med Site Recovery. Alla arbetsbelastningar som kan köras på operativsystem som stöds.

**Operativsystem** | **Detaljer**
--- | ---
Windows Server | 64-bitars Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med på minst SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6,9, 7.1, 7.2 <br/><br/> Centos 6.5 6.6, 6.7, 6.8, 6,9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 med Red Hat kompatibel kernel eller Unbreakable Enterprise Kernel version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
NFS | Ja 
SMB 3.0 | Gäller inte 
SAN (ISCSI) | Ja 
Multipath (MPIO) | Ja 

### <a name="guest-or-physical-server-storage"></a>Gästen eller fysisk serverlagring

**Konfiguration** | **Stöds** 
--- | --- 
VMDK | Ja 
VHD/VHDX | Gäller inte 
Generation 2 VM | Gäller inte 
Delad klusterdisk | Ja 
Krypterade disk | Nej 
UEFI| Ja 
NFS | Nej 
SMB 3.0 | Nej 
RDM | Ja 
Disk > 1 TB | Ja 
Volymen med stripe disk > 1 TB<br/><br/> LVM | Ja 
Lagringsutrymmen | Nej 
Varm Lägg till/ta bort disken | Ja 
Uteslut disk | Ja 
Multipath (MPIO) | Gäller inte 

## <a name="vaults"></a>Valv

**Åtgärd** | **Stöds** 
--- | --- 
Flytta valv mellan resursgrupper (inom eller mellan prenumerationer) | Nej 
Flytta lagring, nätverk, virtuella datorer i Azure över resursgrupper (inom eller mellan prenumerationer) | Nej 

## <a name="mobility-service-and-updates"></a>Mobilitetstjänsten och uppdateringar

Mobilitetstjänsten samordnar replikering mellan lokala VMware-servrar eller fysiska servrar och den sekundära platsen. När du konfigurerar replikering bör du kontrollera att du har den senaste versionen av mobilitetstjänsten och andra komponenter.

**Uppdatering** | **Detaljer** 
--- | --- 
Scout uppdateringar | [Lär dig mer om och hämta](/vmware-physical-secondary-disaster-recovery.md#updates) de senaste uppdateringarna för Scout | Scout uppdateringar är kumulativa.
Komponentuppdateringar | Scout innehåller uppdateringar för alla komponenter, inklusive RX server, konfigurationsservern, process och huvudmålservern servrar, vContinuum-servrar och källservrar som du vill skydda.<br/><br/> [Läs mer](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).


## <a name="next-steps"></a>Nästa steg

Hämta den [InMage Scout användarhandboken](https://aka.ms/asr-scout-user-guide)

- [Replikera virtuella Hyper-V-datorer i VMM-moln till en sekundär plats](tutorial-vmm-to-vmm.md)
- [Replikera virtuella VMware-datorer och fysiska servrar till en sekundär plats](tutorial-vmware-to-vmware.md)
