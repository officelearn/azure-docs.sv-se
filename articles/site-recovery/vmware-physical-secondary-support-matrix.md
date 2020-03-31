---
title: Stöd för VMware/fysisk haveriberedskap till en sekundär plats med Azure Site Recovery
description: Sammanfattar stödet för haveriberedskap av virtuella datorer och fysiska servrar till en sekundär plats med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cabd3f7693c6b6b86bf0324bdafdfe1377d1ece8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256801"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Stödmatris för haveriberedskap av virtuella datorer och fysiska servrar till en sekundär plats

Den här artikeln sammanfattar vad som stöds när du använder [Azure Site Recovery-tjänsten](site-recovery-overview.md) för haveriberedskap av virtuella datorer eller Windows/Linux fysiska servrar till en sekundär VMware-plats.

- Om du vill replikera virtuella datorer med VMware eller fysiska servrar till Azure läser du [den här supportmatrisen](vmware-physical-azure-support-matrix.md).
- Om du vill replikera virtuella hyper-virtuella datorer till en sekundär plats läser du [den här supportmatrisen](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Replikering av lokala virtuella datorer och fysiska servrar tillhandahålls av InMage Scout. InMage Scout ingår i azure site recovery-tjänstprenumerationen.

## <a name="end-of-support-announcement"></a>Meddelande om upphörande support
Scenariot För platsåterställning för replikering mellan lokala VMware- eller fysiska datacenter når supportens.

- Från och med augusti 2018 kan scenariot inte konfigureras i Recovery Services-valvet och InMage Scout-programvaran kan inte hämtas från valvet. Befintliga distributioner kommer att stödjas.
- - Från och med den 31 december 2020 stöds inte scenariot.
Befintliga partner kan gå ombord på nya kunder till scenariot tills supporten upphör.
- Under 2018 och 2019 kommer två uppdateringar att släppas:

    - Uppdatering 7: Åtgärdar problem med nätverkskonfiguration och efterlevnad och ger stöd för TLS 1.2.
    - Uppdatering 8: Lägger till stöd för Linux-operativsystem RHEL/CentOS 7.3/7.4/7.5 och för SUSE 12
    - Efter uppdatering 8 kommer inga ytterligare uppdateringar att släppas. Det kommer att finnas begränsat stöd för snabbkorrigering för operativsystem som läggs till i uppdatering 8 och buggfixar baserat på bästa möjliga ansträngning.

## <a name="host-servers"></a>Värdservrar

**Operativsystem** | **Detaljer**
--- | ---
vCenter Server | vCenter 5.5, 6.0 och 6.5<br/><br/> Om du kör 6.0 eller 6.5 bör du tänka på att endast 5,5 funktioner stöds.


## <a name="replicated-vm-support"></a>Stöd för replikerad virtuell dator

I följande tabell sammanfattas stöd för operativsystem för datorer som replikeras med Platsåterställning. Alla arbetsbelastningar kan köras på det operativsystem som stöds.

**Operativsystem** | **Detaljer**
--- | ---
Windows Server | 64-bitars Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med minst SP1.
Linux | Red Hat Enterprise Linux 6,7, 6,8, 6,9, 7,1, 7,2 <br/><br/> Centos 6,5, 6,6, 6,7, 6,8, 6,9, 7,0, 7,1, 7,2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 som kör Den Red Hat-kompatibla kärnan eller Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
NFS | Ja 
SMB 3.0 | Ej tillämpligt 
SAN (ISCSI) | Ja 
Flera sökvägar (MPIO) | Ja 

### <a name="guest-or-physical-server-storage"></a>Gäst- eller fysiskserverlagring

**Konfiguration** | **Stöds** 
--- | --- 
Vmdk | Ja 
VHD/VHDX | Ej tillämpligt 
Gen 2 VM | Ej tillämpligt 
Delad klusterdisk | Ja 
Krypterad disk | Inga 
Uefi| Ja 
NFS | Inga 
SMB 3.0 | Inga 
Rdm | Ja 
Disk > 1 TB | Ja 
Volym med randig disk > 1 TB<br/><br/> Lvm | Ja 
Lagringsutrymmen | Inga 
Snabb lägg till/ta bort disk | Ja 
Uteslut disk | Ja 
Flera sökvägar (MPIO) | Ej tillämpligt 

## <a name="vaults"></a>Valv

**Åtgärd** | **Stöds** 
--- | --- 
Flytta valv mellan resursgrupper (inom eller mellan prenumerationer) | Inga 
Flytta lagring, nätverk, virtuella Azure-datorer mellan resursgrupper (inom eller mellan prenumerationer) | Inga 

## <a name="mobility-service-and-updates"></a>Mobilitetstjänst och uppdateringar

Mobilitetstjänsten samordnar replikering mellan lokala VMware-servrar eller fysiska servrar och den sekundära platsen. När du konfigurerar replikering bör du se till att du har den senaste versionen av mobilitetstjänsten och andra komponenter.

| **Uppdatering** | **Detaljer** |
| --- | --- |
|Scout uppdateringar | Scoutuppdateringar är kumulativa. <br/><br/> [Lär dig mer om och ladda ner](vmware-physical-secondary-disaster-recovery.md#updates) de senaste scoutuppdateringarna |
|Uppdateringar av komponenter | Scoutuppdateringar innehåller uppdateringar för alla komponenter, inklusive RX-servern, konfigurationsservern, process- och huvudmålservrarna, vContinuum-servrarna och källservrar som du vill skydda.<br/><br/> [Läs mer](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Nästa steg

Ladda ner [användarhandboken för InMage Scout](https://aka.ms/asr-scout-user-guide)

- [Replikera virtuella hyper-v-datorer i VMM-moln till en sekundär plats](tutorial-vmm-to-vmm.md)
- [Replikera virtuella VMware-datorer och fysiska servrar till en sekundär plats](tutorial-vmware-to-vmware.md)
