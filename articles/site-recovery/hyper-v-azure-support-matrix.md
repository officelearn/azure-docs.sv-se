---
title: Stöd mat ris för haveri beredskap för lokala virtuella Hyper-V-datorer till Azure | Microsoft Docs
description: Sammanfattar de komponenter och krav som stöds för haveri beredskap för virtuella Hyper-V-datorer till Azure med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: 3158ebddf6ffe5594c9daf0fd9f3e3fe980c0b24
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845663"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Stöd mat ris för haveri beredskap för lokala virtuella Hyper-V-datorer till Azure


Den här artikeln sammanfattar de komponenter och inställningar som stöds för haveri beredskap för lokala virtuella Hyper-V-datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Scenarier som stöds

**Scenario** | **Detaljer**
--- | ---
Hyper-V med Virtual Machine Manager | Du kan utföra haveri beredskap till Azure för virtuella datorer som körs på Hyper-V-värdar som hanteras i System Center Virtual Machine Manager-infrastrukturen.<br/><br/> Du kan distribuera det här scenariot i Azure Portal eller med hjälp av PowerShell.<br/><br/> När Hyper-V-värdar hanteras av Virtual Machine Manager kan du också utföra haveri beredskap på en sekundär lokal plats. Läs mer om det här scenariot i [den här](hyper-v-vmm-disaster-recovery.md)självstudien.
Hyper-V utan Virtual Machine Manager | Du kan utföra haveri beredskap till Azure för virtuella datorer som körs på Hyper-V-värdar som inte hanteras av Virtual Machine Manager.<br/><br/> Du kan distribuera det här scenariot i Azure Portal eller med hjälp av PowerShell.


## <a name="on-premises-servers"></a>Lokala servrar

**Server** | **Signaturkrav** | **Detaljer**
--- | --- | ---
Hyper-V (körs utan Virtual Machine Manager) |  Windows Server 2019, Windows Server 2016 (inklusive Server Core-installation), Windows Server 2012 R2 med senaste uppdateringar | Om du redan har konfigurerat Windows Server 2012 R2 med/eller SCVMM 2012 R2 med Azure Site Recovery och planerar att uppgradera operativ systemet, följer du vägledningen [.](upgrade-2012R2-to-2016.md) 
Hyper-V (körs med Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Om Virtual Machine Manager används bör Windows Server 2019-värdar hanteras i Virtual Machine Manager 2019. På samma sätt bör Windows Server 2016-värdar hanteras i Virtual Machine Manager 2016.<br/><br/>


## <a name="replicated-vms"></a>Replikerade virtuella datorer


I följande tabell sammanfattas stödet för virtuella datorer. Site Recovery stöder alla arbets belastningar som körs på ett operativ system som stöds.

 **Komponent** | **Detaljer**
--- | ---
Konfiguration av virtuell dator | Virtuella datorer som replikeras till Azure måste uppfylla [kraven för Azure](#azure-vm-requirements).
Gäst operativ system | Alla gäst operativ system [som stöds för Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)...<br/><br/> Windows Server 2016 Nano Server stöds inte.


## <a name="vmdisk-management"></a>Hantering av virtuell dator/disk

**Åtgärd** | **Detaljer**
--- | ---
Ändra storlek på disk på replikerad virtuell Hyper-V-dator | Stöds ej. Inaktivera replikering, gör ändringen och återaktivera replikering för den virtuella datorn.
Lägg till disk på replikerad virtuell Hyper-V-dator | Stöds ej. Inaktivera replikering, gör ändringen och återaktivera replikering för den virtuella datorn.

## <a name="hyper-v-network-configuration"></a>Nätverks konfiguration för Hyper-V

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | ---
Värd nätverk: NIC-Teamning | Ja | Ja
Värd nätverk: VLAN | Ja | Ja
Värd nätverk: IPv4 | Ja | Ja
Värd nätverk: IPv6 | Nej | Nej
Gäst-VM-nätverk: NIC-Teamning | Nej | Nej
Gäst-VM-nätverk: IPv4 | Ja | Ja
Gäst-VM-nätverk: IPv6 | Nej | Ja
Gäst-VM-nätverk: Statisk IP (Windows) | Ja | Ja
Gäst-VM-nätverk: Statisk IP (Linux) | Nej | Nej
Gäst-VM-nätverk: Multi-NIC | Ja | Ja



## <a name="azure-vm-network-configuration-after-failover"></a>Konfiguration av Azure VM-nätverk (efter redundans)

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Ja | Ja
ILB | Ja | Ja
ELB | Ja | Ja
Azure Traffic Manager | Ja | Ja
Multi-NIC | Ja | Ja
Reserverad IP | Ja | Ja
IPv4 | Ja | Ja
Behåll Källans IP-adress | Ja | Ja
Azure Virtual Network-tjänstens slut punkter<br/> (utan Azure Storage brand väggar) | Ja | Ja
Accelererat nätverk | Nej | Nej


## <a name="hyper-v-host-storage"></a>Lagring av Hyper-V-värd

**Storage** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | --- 
NFS | Ej tillämpligt | Ej tillämpligt
SMB 3.0 | Ja | Ja
SAN (ISCSI) | Ja | Ja
Multipath (MPIO). Testat med:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM för CLARiiON | Ja | Ja

## <a name="hyper-v-vm-guest-storage"></a>Gäst lagring för Hyper-V-VM

**Storage** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | ---
VMDK | Ej tillämpligt | Ej tillämpligt
VHD/VHDX | Ja | Ja
Generation 2 VM | Ja | Ja
EFI/UEFI| Ja | Ja
Delad kluster disk | Nej | Nej
Krypterad disk | Nej | Nej
NFS | Ej tillämpligt | Ej tillämpligt
SMB 3.0 | Nej | Nej
RDM | Ej tillämpligt | Ej tillämpligt
Disk > 1 TB | Ja, upp till 4 095 GB | Ja, upp till 4 095 GB
Diskdefragmenter logisk och fysisk 4K-sektor | Stöds inte: Gen 1/Gen 2 | Stöds inte: Gen 1/Gen 2
Diskdefragmenter fysisk sektor för 4K och 512 byte | Ja |  Ja
Hantering av logiska volymer (LVM). LVM stöds endast på data diskar. Azure tillhandahåller bara en enda OS-disk. | Ja | Ja
Volym med Striped disk > 1 TB | Ja | Ja
Lagrings utrymmen | Nej | Nej
Snabb Lägg till/ta bort disk | Nej | Nej
Uteslut disk | Ja | Ja
Multipath (MPIO) | Ja | Ja

## <a name="azure-storage"></a>Azure Storage

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | ---
Lokalt redundant lagring | Ja | Ja
Geografiskt redundant lagring | Ja | Ja
Geo-redundant lagring med Läs behörighet | Ja | Ja
Cool Storage | Nej | Nej
Frekvent lagring| Nej | Nej
Blockblob-objekt | Nej | Nej
Kryptering i rest (SSE)| Ja | Ja
Premium Storage | Ja | Ja
Import/export-tjänst | Nej | Nej
Azure Storage brand väggar för virtuella nätverk som har kon figurer ATS på mål lagring/cache lagrings konto (används för att lagra replikeringsdata) | Nej | Nej


## <a name="azure-compute-features"></a>Beräknings funktioner i Azure

**Funktion** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | ---
Tillgänglighetsuppsättningar | Ja | Ja
) | Ja | Ja  
Hanterade diskar | Ja, för redundans.<br/><br/> Det finns inte stöd för återställning efter fel för hanterade diskar. | Ja, för redundans.<br/><br/> Det finns inte stöd för återställning efter fel för hanterade diskar.

## <a name="azure-vm-requirements"></a>Krav för virtuell Azure-dator

Lokala virtuella datorer som du replikerar till Azure måste uppfylla de krav på Azure VM som sammanfattas i den här tabellen.

**Komponent** | **Signaturkrav** | **Detaljer**
--- | --- | ---
Gäst operativ system | Site Recovery stöder alla operativ system som [stöds av Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Krav kontrollen Miss lyckas om den inte stöds.
Gäst operativ systemets arkitektur | 64-bitars | Krav kontrollen Miss lyckas om den inte stöds.
Storlek på operativ system disk | Upp till 2 048 GB för virtuella datorer i generation 1.<br/><br/> Upp till 300 GB för virtuella datorer i generation 2.  | Krav kontrollen Miss lyckas om den inte stöds.
Antal operativ system diskar | 1 | Krav kontrollen Miss lyckas om den inte stöds.
Antal data diskar | 16 eller mindre  | Krav kontrollen Miss lyckas om den inte stöds.
VHD-storlek för datadisk | Upp till 4 095 GB | Krav kontrollen Miss lyckas om den inte stöds.
Nätverkskort | Flera nätverkskort stöds |
Delad virtuell hård disk | Stöds inte | Krav kontrollen Miss lyckas om den inte stöds.
FC-disk | Stöds inte | Krav kontrollen Miss lyckas om den inte stöds.
Hård disk format | VHD <br/><br/> VHDX | Site Recovery konverterar automatiskt VHDX till VHD när du växlar över till Azure. När du växlar tillbaka till den lokala datorn fortsätter de virtuella datorerna att använda VHDX-formatet.
BitLocker | Stöds inte | BitLocker måste inaktive ras innan du aktiverar replikering för en virtuell dator.
VM-namn | Mellan 1 och 63 tecken. Begränsat till bokstäver, siffror och bindestreck. VM-namnet måste börja och sluta med en bokstav eller en siffra. | Uppdatera värdet i VM-egenskaperna i Site Recovery.
Typ av virtuell dator | Generation 1<br/><br/> Generation 2--Windows | Generation 2 virtuella datorer med en OS-disktyp (som innehåller en eller två data volymer formaterade som VHDX) och mindre än 300 GB disk utrymme stöds.<br></br>Virtuella Linux generation 2-datorer stöds inte. [Läs mer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Recovery Services valv åtgärder

**Åtgärd** |  **Hyper-V med Virtual Machine Manager** | **Hyper-V utan Virtual Machine Manager**
--- | --- | ---
Flytta valv över resurs grupper<br/><br/> Inom och över prenumerationer | Nej | Nej
Flytta lagring, nätverk, virtuella Azure-datorer över resurs grupper<br/><br/> Inom och över prenumerationer | Nej | Nej

> [!NOTE]
> När du replikerar virtuella Hyper-VM (hanterade med/utan SCVMM) från lokala datorer till Azure, kan du bara replikera till en AD-klient från en särskild miljö – Hyper-V-plats eller SCVMM som tillämpligt.


## <a name="provider-and-agent"></a>Provider och agent

Se till att du kör de senaste leverantörs-och agent versionerna för att se till att distributionen är kompatibel med inställningarna i den här artikeln.

**Namn** | **Beskrivning** | **Detaljer**
--- | --- | --- 
Azure Site Recovery Provider | Koordinera kommunikation mellan lokala servrar och Azure <br/><br/> Hyper-V med Virtual Machine Manager: Installerad på Virtual Machine Manager-servrar<br/><br/> Hyper-V utan Virtual Machine Manager: Installerat på Hyper-V-värdar| Senaste version: 5.1.2700.1 (tillgängligt från Azure Portal)<br/><br/> [Senaste funktioner och korrigeringar](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services agent | Samordnar replikering mellan virtuella Hyper-V-datorer och Azure<br/><br/> Installerat på lokala Hyper-V-servrar (med eller utan Virtual Machine Manager) | Senaste agent som är tillgänglig från portalen






## <a name="next-steps"></a>Nästa steg
Lär dig hur du [förbereder Azure](tutorial-prepare-azure.md) för haveri beredskap för lokala virtuella Hyper-V-datorer.
