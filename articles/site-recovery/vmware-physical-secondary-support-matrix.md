---
title: Stöd matrix för haveriberedskap för virtuella VMware-datorer eller fysiska servrar till en sekundär VMware-plats med Azure Site Recovery | Microsoft Docs
description: Sammanfattar stödet för haveriberedskap för virtuella VMware-datorer och fysiska servrar till en sekundär plats med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: b03ee4ebf1113fd422d34ebf219461e75c4a5a45
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970771"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Stöd matrix för haveriberedskap för virtuella VMware-datorer och fysiska servrar till en sekundär plats

Den här artikeln sammanfattar vad som stöds när du använder den [Azure Site Recovery](site-recovery-overview.md) -tjänsten för haveriberedskap för virtuella VMware-datorer eller Windows-/ Linux fysiska servrar till en sekundär VMware-plats.

- Om du vill replikera virtuella VMware-datorer eller fysiska servrar till Azure kan du läsa [supportmatris för den här](vmware-physical-azure-support-matrix.md).
- Om du vill replikera Hyper-V-datorer till en sekundär plats kan du läsa [supportmatris för den här](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikering av lokala virtuella VMware-datorer och fysiska servrar tillhandahåller InMage Scout. InMage Scout ingår i prenumeration för Azure Site Recovery-tjänsten.


## <a name="host-servers"></a>Värdservrar

**Operativsystem** | **Detaljer**
--- | ---
vCenter Server | vCenter 5.5, 6.0 och 6.5<br/><br/> Om du kör 6.0 eller 6.5, Observera att endast 5.5 funktioner som stöds.


## <a name="replicated-vm-support"></a>Replikerade support för virtuella datorer

I följande tabell sammanfattas operativsystem för datorer som replikeras med Site Recovery. Alla arbetsbelastningar kan köras på operativsystemet som stöds.

**Operativsystem** | **Detaljer**
--- | ---
Windows Server | 64-bitars Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med på minst SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6,9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6,9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 som kör Red Hat kompatibla kernel eller Unbreakable Enterprise Kernel version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Lagring för Linux-dator

Endast Linux-datorer med följande storage kan replikeras:

- Filsystem (EXT3, ETX4, ReiserFS, XFS).
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
Gäst VM - IPv4 | Ja
Gäst VM - IPv6 | Nej
Gues VM-Windows / Linux - statisk IP-adress | Ja
Gäst VM - med flera nätverkskort | Ja


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Värdlagring

**Lagring (värd)** | **Stöds** 
--- | --- 
NFS | Ja 
SMB 3.0 | Gäller inte 
SAN (ISCSI) | Ja 
Flera sökvägar (MPIO) | Ja 

### <a name="guest-or-physical-server-storage"></a>Gäst eller fysisk lagring

**Konfiguration** | **Stöds** 
--- | --- 
VMDK | Ja 
VHD-/ VHDX | Gäller inte 
Gen 2 VM | Gäller inte 
Delad klusterdisk | Ja 
Krypterad disk | Nej 
UEFI| Ja 
NFS | Nej 
SMB 3.0 | Nej 
RDM | Ja 
Disk-> 1 TB | Ja 
Volymen med stripe-disk > 1 TB<br/><br/> LVM | Ja 
Lagringsutrymmen | Nej 
Frekvent Lägg till/ta bort disk | Ja 
Uteslut disk | Ja 
Flera sökvägar (MPIO) | Gäller inte 

## <a name="vaults"></a>Valv

**Åtgärd** | **Stöds** 
--- | --- 
Flytta valv mellan resursgrupper (inom eller mellan prenumerationer) | Nej 
Flytta lagring, nätverk, virtuella Azure-datorer mellan resursgrupper (inom eller mellan prenumerationer) | Nej 

## <a name="mobility-service-and-updates"></a>Mobilitetstjänsten och uppdateringar

Mobilitetstjänsten samordnar replikering mellan lokala VMware-servrar eller fysiska servrar och den sekundära platsen. När du konfigurerar replikering ska du kontrollera att du har den senaste versionen av mobilitetstjänsten och andra komponenter.

**Uppdatering** | **Detaljer** 
--- | --- 
Scout uppdateringar | [Lär dig och ladda ned](vmware-physical-secondary-disaster-recovery.md#updates) de senaste uppdateringarna för Scout | Scout uppdateringar är kumulativa.
Komponentuppdateringar | Scout uppdateringar innehåller uppdateringar för alla komponenter, inklusive RX server, konfigurationsservern, process och huvudmålservrar, vContinuum servrar och källservrar som du vill skydda.<br/><br/> [Läs mer](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).


## <a name="next-steps"></a>Nästa steg

Ladda ned den [InMage Scout-användarhandboken](https://aka.ms/asr-scout-user-guide)

- [Replikera Hyper-V-datorer i VMM-moln till en sekundär plats](tutorial-vmm-to-vmm.md)
- [Replikera virtuella VMware-datorer och fysiska servrar till en sekundär plats](tutorial-vmware-to-vmware.md)
