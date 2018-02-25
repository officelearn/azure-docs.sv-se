---
title: "Stöd matrix för replikering till en sekundär plats med Azure Site Recovery | Microsoft Docs"
description: "Sammanfattning av de operativsystem som stöds och komponenter för Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: raynew
ms.openlocfilehash: 256bad0c3c06182b6be2b647ae27db90fe69724d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Stöd matrix för replikering till en sekundär plats med Azure Site Recovery

Den här artikeln sammanfattar vad stöds när du använder den [Azure Site Recovery](site-recovery-overview.md) -tjänsten för att replikera till en sekundär lokal plats.

## <a name="supported-scenarios"></a>Scenarier som stöds

**Distribution** | **Detaljer** 
--- | ---
**VMware till VMware** | Haveriberedskap för lokala virtuella VMware-datorer till sekundär VMware-plats.<br/><br/> Hämta den [InMage Scout användarhandboken](https://aka.ms/asr-scout-user-guide)
**Hyper-V till Hyper-V** | Haveriberedskap för lokala Hyper-V virtuella datorer i VMM-moln till en sekundär VMM-moln.<br></br> Stöder inte utan VMM.



  

## <a name="host-servers"></a>Värdservrar för fjärrskrivbordssessioner

**Distribution** | **Support**
--- | ---
**VMware-dator/fysisk server** | vCenter 5.5, 6.0 och 6.5 (stöd för 5.5 funktioner)
**Hyper-V med VMM** | Windows Server 2016 och Windows Server 2012 R2 med de senaste uppdateringarna.<br/><br/> Windows Server 2016 värdar ska hanteras av VMM 2016.<br/><br/> VMM 2016 moln med en blandning av Windows Server 2016 och 2012 R2-värdar stöds inte för närvarande.<br/><br/> Distribution som innehåller en uppgradering av en befintlig VMM 2012 R2 till System Center 2016 stöds inte för närvarande.


## <a name="support-for-replicated-machine-os-versions"></a>Stöd för replikerade datorn OS-versioner

I följande tabell sammanfattas operativsystem för datorer som replikeras med Site Recovery. Alla arbetsbelastningar som kan köras på operativsystem som stöds.

**VMware/fysisk server** | **Hyper-V (med VMM)**
--- | ---
64-bitars Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med på minst SP1<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 6,9, 7.1, 7.2 <br/><br/> Centos 6.5 6.6, 6.7, 6.8, 6,9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 med Red Hat kompatibel kernel eller Unbreakable Enterprise Kernel version 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4  | Någon gästen operativsystemet [stöds av Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

## <a name="linux-machine-storage"></a>Lagring av Linux-dator

Linux-baserade datorer med följande lagringen kan replikeras:

- Filsystem (EXT3, ETX4, ReiserFS, XFS).
- Mappning för flera sökvägar programvara-enhet.
- Volymhanterare (LVM2).
- Fysiska servrar med HP CCISS domänkontrollant lagring stöds inte.
- Filsystemet ReiserFS stöds endast för SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration"></a>Nätverkskonfiguration

### <a name="hosts"></a>Värdar

**Konfiguration** | **VMware/fysisk server** | **Hyper-V (med VMM)**
--- | --- | ---
NIC-teamindelning | Ja | Ja
VLAN | Ja | Ja
IPv4 | Ja | Ja
IPv6 | Nej | Nej

### <a name="guest-vms"></a>Gäst-VM

**Konfiguration** | **VMware/fysisk server** | **Hyper-V (med VMM)**
--- | --- | ---
NIC-teamindelning | Nej | Nej
IPv4 | Ja | Ja
IPv6 | Nej | Nej
Statisk IP-adress (Windows) | Ja | Ja
Statisk IP-adress (Linux) | Ja | Ja
Flera nätverkskort | Ja | Ja


## <a name="storage"></a>Lagring

### <a name="host-storage"></a>Värd storage

**Lagring (värd)** | **VMware/fysisk server** | **Hyper-V (med VMM)**
--- | --- | ---
NFS | Ja | Gäller inte
SMB 3.0 | Gäller inte | Ja
SAN (ISCSI) | Ja | Ja
Multipath (MPIO) | Ja | Ja

### <a name="guest-or-physical-server-storage"></a>Gästen eller fysisk serverlagring

**Konfiguration** | **VMware/fysisk server** | **Hyper-V (med VMM)**
--- | --- | ---
VMDK | Ja | Gäller inte
VHD/VHDX | Gäller inte | Ja (upp till 16 diskar)
Generation 2 VM | Gäller inte | Ja
Delad klusterdisk | Ja  | Nej
Krypterade disk | Nej | Nej
UEFI| Ja | Gäller inte
NFS | Nej | Nej
SMB 3.0 | Nej | Nej
RDM | Ja | Gäller inte
Disk > 1 TB | Ja | Ja
Volymen med stripe disk > 1 TB<br/><br/> LVM | Ja | Ja
Lagringsutrymmen | Nej | Ja
Varm Lägg till/ta bort disken | Ja | Nej
Uteslut disk | Ja | Ja
Multipath (MPIO) | Gäller inte | Ja

## <a name="vaults"></a>Valv

**Åtgärd** | **VMware/fysisk server** | **Hyper-V (med VMM)**
--- | --- | ---
Flytta valv mellan resursgrupper (inom eller mellan prenumerationer) | Nej | Nej
Flytta lagring, nätverk, virtuella datorer i Azure över resursgrupper (inom eller mellan prenumerationer) | Nej | Nej

## <a name="provider-and-agent"></a>Provider och agent

**Namn** | **Beskrivning** | **senaste versionen** | **Detaljer**
--- | --- | --- | --- | ---
**Azure Site Recovery Provider** | Samordnar kommunikationen mellan lokala servrar och Azure <br/><br/> Installeras på lokal VMM-servrar, eller på Hyper-V-servrar om det finns ingen VMM-server | 5.1.19 ([tillgänglig från portalen](http://aka.ms/downloaddra)) | [Senaste funktionerna och korrigeringarna](https://support.microsoft.com/kb/3155002)
**Mobilitetstjänsten** | Samordnar replikering mellan lokala VMware-servrar eller fysiska servrar och den sekundära platsen<br/><br/> Installerad på VMware VM eller fysiska servrar som du vill replikera  | Ej tillämpligt (tillgänglig från portalen) | Gäller inte


## <a name="next-steps"></a>Nästa steg

- [Replikera virtuella Hyper-V-datorer i VMM-moln till en sekundär plats](tutorial-vmm-to-vmm.md)
- [Replikera virtuella VMware-datorer och fysiska servrar till en sekundär plats](tutorial-vmware-to-vmware.md)
