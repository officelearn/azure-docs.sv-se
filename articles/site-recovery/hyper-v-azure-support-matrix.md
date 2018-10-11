---
title: Stödmatris för Hyper-V-replikering till Azure | Microsoft Docs
description: Sammanfattning av de komponenter som stöds och kraven för Hyper-V-replikering till Azure med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 83d2fabbe3a1b0ab44cb97e53baf0aa40349aeed
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079216"
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Stödmatris för Hyper-V-replikering till Azure


Den här artikeln sammanfattas de komponenter som stöds och inställningar för haveriberedskap för lokala Hyper-V-datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Scenarier som stöds

**Scenario** | **Detaljer**
--- | ---
Hyper-V med Virtual Machine Manager | Du kan utföra katastrofåterställning till Azure för virtuella datorer som körs på Hyper-V-värdar som hanteras i System Center Virtual Machine Manager-infrastrukturen.<br/><br/> Du kan distribuera det här scenariot i Azure portal eller med hjälp av PowerShell.<br/><br/> När Hyper-V-värdar hanteras av Virtual Machine Manager kan utföra du också haveriberedskap till en sekundär lokal plats. Mer information om det här scenariot [den här självstudien](hyper-v-vmm-disaster-recovery.md).
Hyper-V utan att Virtual Machine Manager | Du kan utföra katastrofåterställning till Azure för virtuella datorer som körs på Hyper-V-värdar som inte hanteras av Virtual Machine Manager.<br/><br/> Du kan distribuera det här scenariot i Azure portal eller med hjälp av PowerShell.


## <a name="on-premises-servers"></a>Lokala servrar

**Server** | **Krav** | **Detaljer**
--- | --- | ---
Hyper-V (som körs utan att Virtual Machine Manager) | Windows Server 2016 (inklusive server core-installation), Windows Server 2012 R2 med de senaste uppdateringarna | När du konfigurerar en Hyper-V-plats i Site Recovery kan stöds blanda värdar som kör Windows Server 2016 och 2012 R2 inte.<br/><br/> För virtuella datorer finns på en värd som kör Windows Server 2016, stöds inte återställning till en annan plats.
Hyper-V (som körs med Virtual Machine Manager) | Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Om du använder Virtual Machine Manager, ska Windows Server 2016-värdar hanteras i Virtual Machine Manager 2016.<br/><br/> Ett Virtual Machine Manager-moln där Hyper-V-värdar som kör Windows Server 2016 och 2012 R2 stöds inte för närvarande.<br/><br/> Miljöer som inkluderar en uppgradering av en befintlig server i Virtual Machine Manager 2012 R2 till 2016 stöds inte.


## <a name="replicated-vms"></a>Replikerade virtuella datorer


I följande tabell sammanfattas support för virtuella datorer. Site Recovery stöder alla arbetsbelastningar som körs på ett operativsystem som stöds.

 **Komponent** | **Detaljer**
--- | ---
Konfiguration av virtuell dator | Virtuella datorer som replikerar till Azure måste uppfylla [krav för Azure](#azure-vm-requirements).
Gästoperativsystem | Alla gästoperativsystem [stöds för Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)...<br/><br/> Windows Server 2016 Nano Server stöds inte.


## <a name="vmdisk-management"></a>VM/Diskhantering

**Åtgärd** | **Detaljer**
--- | ---
Ändra storlek på disk på replikerade Hyper-V-dator | Stöds ej. Inaktivera replikering, ändra och sedan återaktivera replikering för den virtuella datorn.
Lägg till disk på replikerade Hyper-V-dator | Stöds ej. Inaktivera replikering, ändra och sedan återaktivera replikering för den virtuella datorn.

## <a name="hyper-v-network-configuration"></a>Hyper-V-nätverkskonfiguration

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
Värdnätverket: NIC Teaming | Ja | Ja
Värdnätverket: VLAN | Ja | Ja
Värdnätverket: IPv4 | Ja | Ja
Värdnätverket: IPv6 | Nej | Nej
Gäst VM-nätverk: NIC Teaming | Nej | Nej
Gäst VM-nätverk: IPv4 | Ja | Ja
Gäst VM-nätverk: IPv6 | Nej | Ja
Gäst VM-nätverk: statisk IP-adress (Windows) | Ja | Ja
Gäst VM-nätverk: statisk IP-adress (Linux) | Nej | Nej
Gäst VM-nätverk: Multi-NIC | Ja | Ja



## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM-nätverkskonfiguration (efter redundans)

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Ja | Ja
ILB | Ja | Ja
ELB | Ja | Ja
Azure Traffic Manager | Ja | Ja
Multi-NIC | Ja | Ja
Reserverad IP | Ja | Ja
IPv4 | Ja | Ja
Behåll källans IP-adress | Ja | Ja
Azure Virtual Network-tjänstslutpunkter<br/> (utan Azure Storage-brandväggar) | Ja | Ja
Accelererat nätverk | Nej | Nej


## <a name="hyper-v-host-storage"></a>Lagring för Hyper-V-värd

**Storage** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | --- | ---
NFS | Ej tillämpligt | Ej tillämpligt
SMB 3.0 | Ja | Ja
SAN (ISCSI) | Ja | Ja
Flera sökvägar (MPIO). Testats med:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM för CLARiiON | Ja | Ja

## <a name="hyper-v-vm-guest-storage"></a>Lagring för Hyper-V VM-gäst

**Storage** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
VMDK | Ej tillämpligt | Ej tillämpligt
VHD-/ VHDX | Ja | Ja
Generation 2 VM | Ja | Ja
EFI/UEFI| Ja | Ja
Delad klusterdisk | Nej | Nej
Krypterad disk | Nej | Nej
NFS | Ej tillämpligt | Ej tillämpligt
SMB 3.0 | Nej | Nej
RDM | Ej tillämpligt | Ej tillämpligt
Disk > 1 TB | Ja, upp till 4095 GB | Ja, upp till 4095 GB
Disk: 4K logisk och fysisk sektor | Stöds inte: Gen 1/Gen 2 | Stöds inte: Gen 1/Gen 2
Disk: 4K logisk och fysisk 512 byte-sektor | Ja |  Ja
Hantering av logisk volym (LVM). LVM stöds på data-diskar. Azure tillhandahåller endast en enda OS-disk. | Ja | Ja
Volym med stripe-disk > 1 TB | Ja | Ja
Lagringsutrymmen | Ja | Ja
Frekvent Lägg till/ta bort disk | Nej | Nej
Uteslut disk | Ja | Ja
Flera sökvägar (MPIO) | Ja | Ja

## <a name="azure-storage"></a>Azure Storage

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
Lokalt redundant lagring | Ja | Ja
Geografiskt redundant lagring | Ja | Ja
Läsåtkomst till geografiskt redundant lagring | Ja | Ja
Lågfrekvent lagring | Nej | Nej
Frekvent lagring| Nej | Nej
Blockblob-objekt | Nej | Nej
Kryptering i vila (SSE)| Ja | Ja
Premium Storage | Ja | Ja
Import/export-tjänsten | Nej | Nej
Azure Storage-brandväggar för virtuella nätverk som konfigurerats på mållagringskontot för lagring/cache (används för att lagra data för replikering) | Nej | Nej


## <a name="azure-compute-features"></a>Azure compute-funktioner

**Funktion** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
Tillgänglighetsuppsättningar | Ja | Ja
HUB | Ja | Ja  
Hanterade diskar | Ja, för redundans.<br/><br/> Återställning efter fel för hanterade diskar stöds inte. | Ja, för redundans.<br/><br/> Återställning efter fel för hanterade diskar stöds inte.

## <a name="azure-vm-requirements"></a>Krav för Azure VM

Lokala virtuella datorer som du replikerar till Azure måste uppfylla kraven för virtuella Azure-datorer som sammanfattas i den här tabellen.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
Gästoperativsystem | Site Recovery stöder alla operativsystem som är [stöds av Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Kravkontroll misslyckas om stöds inte.
Gästen operativsystemets arkitektur | 64-bitars | Kravkontroll misslyckas om stöds inte.
Storleken på operativsystemdisken | Upp till 2 048 GB för virtuella datorer i generation 1.<br/><br/> Upp till 300 GB för virtuella datorer i generation 2.  | Kravkontroll misslyckas om stöds inte.
Antal operativsystemdiskar | 1 | Kravkontroll misslyckas om stöds inte.
Datadiskar | 16 eller mindre  | Kravkontroll misslyckas om stöds inte.
VHD för datadiskstorleken | Upp till 4095 GB | Kravkontroll misslyckas om stöds inte.
Nätverkskort | Flera nätverkskort stöds |
Delad VHD | Stöds inte | Kravkontroll misslyckas om stöds inte.
FC-disk | Stöds inte | Kravkontroll misslyckas om stöds inte.
Format för hårddisk | VIRTUELL HÅRDDISK <br/><br/> VHDX | Konverterar site Recovery automatiskt VHDX till VHD när du redundansväxlar till Azure. När du växlar tillbaka till den lokala virtuella datorer att fortsätta att använda VHDX-formatet.
BitLocker | Stöds inte | BitLocker måste inaktiveras innan du aktiverar replikering för en virtuell dator.
VM-namn | Mellan 1 och 63 tecken. Begränsat till bokstäver, siffror och bindestreck. VM-namnet måste börja och sluta med en bokstav eller en siffra. | Uppdatera värdet i VM-egenskaperna i Site Recovery.
Typ av virtuell dator | Generation 1<br/><br/> Generation 2 – Windows | Virtuella datorer i generation 2 med en OS-disktyp Basic (som innehåller en eller två datavolymer som formaterats som VHDX) och mindre än 300 GB diskutrymme som stöds.<br></br>Virtuella datorer i Linux Generation 2 stöds inte. [Läs mer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Recovery Services-valv åtgärder

**Åtgärd** |  **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
Flytta valv mellan resursgrupper<br/><br/> Inom och mellan prenumerationer | Nej | Nej
Flytta lagring, nätverk, virtuella Azure-datorer mellan resursgrupper<br/><br/> Inom och mellan prenumerationer | Nej | Nej


## <a name="provider-and-agent"></a>Provider och agent

Om du vill kontrollera din distribution är kompatibla med inställningarna i den här artikeln genom att kontrollera att du kör den senaste providern och agentversioner.

**Namn** | **Beskrivning** | **Detaljer**
--- | --- | --- | --- | ---
Azure Site Recovery-providern | Samordnar kommunikationen mellan lokala servrar och Azure <br/><br/> Hyper-V med Virtual Machine Manager: installerad på Virtual Machine Manager-servrar<br/><br/> Hyper-V utan att Virtual Machine Manager: installerad på Hyper-V-värdar| Senaste versionen: 5.1.2700.1 (tillgängligt från Azure portal)<br/><br/> [Senaste funktionerna och korrigeringarna](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services-agenten | Samordnar replikering mellan Hyper-V-datorer och Azure<br/><br/> Installerad på den lokala Hyper-V-servrar (med eller utan Virtual Machine Manager) | Senaste agenten som är tillgängliga från portalen






## <a name="next-steps"></a>Nästa steg
Lär dig hur du [förbereda Azure](tutorial-prepare-azure.md) för haveriberedskap för lokala Hyper-V-datorer.
