---
title: Stöd matrix för Hyper-V-replikering till Azure | Microsoft Docs
description: Sammanfattning av de stödda komponenter och krav för Hyper-V-replikering till Azure med Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2018
ms.author: raynew
ms.openlocfilehash: c818ff0df5cb1f1b3d20c726b20b30c418f53061
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266969"
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Stöd matrix för Hyper-V-replikering till Azure


Den här artikeln sammanfattar de stödda komponenter och inställningar för katastrofåterställning för lokala Hyper-V virtuella datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Scenarier som stöds

**Scenario** | **Detaljer**
--- | ---
Hyper-V med Virtual Machine Manager | Du kan utföra katastrofåterställning till Azure för virtuella datorer som körs på Hyper-V-värdar som hanteras i System Center Virtual Machine Manager-infrastrukturen.<br/><br/> Du kan distribuera det här scenariot i Azure-portalen eller med hjälp av PowerShell.<br/><br/> När Hyper-V-värdar som hanteras av Virtual Machine Manager, utföra du också återställning till en sekundär lokal plats. Mer information om det här scenariot [självstudierna](tutorial-vmm-to-vmm.md).
Hyper-V utan att Virtual Machine Manager | Du kan utföra katastrofåterställning till Azure för virtuella datorer som körs på Hyper-V-värdar som inte hanteras av Virtual Machine Manager.<br/><br/> Du kan distribuera det här scenariot i Azure-portalen eller med hjälp av PowerShell.


## <a name="on-premises-servers"></a>Lokala servrar

**Server** | **Krav** | **Detaljer**
--- | --- | ---
Hyper-V (kör utan Virtual Machine Manager) | Windows Server 2016, Windows Server 2012 R2 med de senaste uppdateringarna | När du konfigurerar en Hyper-V-platsen i Site Recovery stöds blanda värdar som kör Windows Server 2016 och 2012 R2 inte.<br/><br/> För virtuella datorer finns på en värd som kör Windows Server 2016, stöds inte återställning till en alternativ plats.
Hyper-V (som körs med Virtual Machine Manager) | Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Om Virtual Machine Manager används ska Windows Server 2016 värdar hanteras i Virtual Machine Manager 2016.<br/><br/> Ett Virtual Machine Manager-moln som kombinerar Hyper-V-värdar som kör Windows Server 2016 och 2012 R2 stöds inte för närvarande.<br/><br/> Miljöer som innehåller en uppgradering av en befintlig Virtual Machine Manager 2012 R2-server till 2016 stöds inte.


## <a name="replicated-vms"></a>Replikerade virtuella datorer


I följande tabell sammanfattas VM-stöd. Site Recovery har stöd för alla arbetsbelastningar som körs på ett operativsystem som stöds.

 **Komponent** | **Detaljer**
--- | ---
VM-konfiguration | Virtuella datorer som replikeras till Azure måste uppfylla [krav för Azure](#failed-over-azure-vm-requirements).
Gästoperativsystemet | Alla gäst-OS som stöds av Azure.<br/><br/> Windows Server 2016 Nano Server stöds inte.




## <a name="hyper-v-network-configuration"></a>Konfiguration av Hyper-V-nätverk

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
Nätverk för värden: NIC Teaming | Ja
Nätverk för värden: VLAN | Ja
Nätverk för värden: IPv4 | Ja
Nätverk för värden: IPv6 | Nej
Gäst VM-nätverk: NIC Teaming | Nej
Gäst VM-nätverk: IPv4 | Ja
Gäst VM-nätverk: IPv6 | Nej
Gäst VM-nätverk: statisk IP-adress (Windows) | Ja
Gäst VM-nätverk: statisk IP-adress (Linux) | Nej
Gäst VM-nätverk: flera nätverkskort | Ja



## <a name="azure-vm-network-configuration-after-failover"></a>Nätverkskonfigurationen på Azure VM (efter växling vid fel)

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
Azure ExpressRoute | Ja | Ja
ILB | Ja | Ja
ELB | Ja | Ja
Azure Traffic Manager | Ja | Ja
Flera nätverkskort | Ja | Ja
Reserverad IP | Ja | Ja
IPv4 | Ja | Ja
Behåll källans IP-adress | Ja | Ja
Azure Virtual Network service-slutpunkter<br/> (utan Azure Storage brandväggar) | Ja | Ja
Accelererat nätverk | Nej | Nej


## <a name="hyper-v-host-storage"></a>Lagring för Hyper-V-värd

**Storage** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | --- | ---
NFS | Ej tillämpligt | Ej tillämpligt
SMB 3.0 | Ja | Ja
SAN (ISCSI) | Ja | Ja
Multipath (MPIO). Testats med:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM för CLARiiON | Ja | Ja

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM gäst-lagring

**Storage** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
VMDK | Ej tillämpligt | Ej tillämpligt
VHD-ELLER VHDX | Ja | Ja
Generation 2 VM | Ja | Ja
EFI/UEFI| Ja | Ja
Delad klusterdisk | Nej | Nej
Krypterade disk | Nej | Nej
NFS | Ej tillämpligt | Ej tillämpligt
SMB 3.0 | Nej | Nej
RDM | Ej tillämpligt | Ej tillämpligt
Disk > 1 TB | Ja, upp till 4,095 GB | Ja, upp till 4,095 GB
Disk: 4K logisk och fysisk sektor | Stöds inte: Gen 1/Gen 2 | Stöds inte: Gen 1/Gen 2
Disk: 4K logisk och fysisk 512 byte-sektor | Ja |  Ja
Volym med stripe disk > 1 TB<br/><br/> Logisk volym management (LVM) | Ja | Ja
Lagringsutrymmen | Ja | Ja
Varm Lägg till/ta bort disken | Nej | Nej
Uteslut disk | Ja | Ja
Multipath (MPIO) | Ja | Ja

## <a name="azure-storage"></a>Azure Storage

**Komponent** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
Lokalt redundant lagring | Ja | Ja
Geografiskt redundant lagring | Ja | Ja
Geo-redundant lagring med läsbehörighet | Ja | Ja
Lågfrekvent | Nej | Nej
Frekvent| Nej | Nej
Blockblob-objekt | Nej | Nej
Kryptering i vila (SSE)| Ja | Ja
Premium Storage | Ja | Ja
Import/export service | Nej | Nej
Azure Storage-brandväggar för virtuella nätverk som konfigurerats på mål-storage-cache lagringskontot (används för att lagra data för replikering) | Nej | Nej


## <a name="azure-compute-features"></a>Azure compute-funktioner

**Funktion** | **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
Tillgänglighetsuppsättningar | Ja | Ja
HUBBEN | Ja | Ja  
Hanterade diskar | Ja, för växling vid fel.<br/><br/> Återställning efter fel för hanterade diskar stöds inte. | Ja, för växling vid fel.<br/><br/> Återställning efter fel för hanterade diskar stöds inte.

## <a name="azure-vm-requirements"></a>Krav för Azure VM

Lokala virtuella datorer som du replikerar till Azure måste uppfylla kraven för Azure VM som sammanfattas i den här tabellen.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
Gästoperativsystemet | Site Recovery har stöd för alla operativsystem som är [stöds av Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Kravkontroll misslyckas om stöds inte.
Gästen operativsystemets arkitektur | 64-bitars | Kravkontroll misslyckas om stöds inte.
Operativsystemdisken | Upp till 2 048 GB för virtuella datorer i generation 1.<br/><br/> Upp till 300 GB för generation 2 virtuella datorer.  | Kravkontroll misslyckas om stöds inte.
Operativsystemet disk antal | 1 | Kravkontroll misslyckas om stöds inte.
Datadiskar | 16 eller mindre  | Kravkontroll misslyckas om stöds inte.
Datadisken för virtuell Hårddisk | Upp till 4,095 GB | Kravkontroll misslyckas om stöds inte.
Nätverkskort | Flera nätverkskort stöds |
Delad virtuell Hårddisk | Stöds inte | Kravkontroll misslyckas om stöds inte.
FC-disk | Stöds inte | Kravkontroll misslyckas om stöds inte.
Format för hårddisk | VIRTUELL HÅRDDISK <br/><br/> VHDX | Site Recovery konverterar automatiskt VHDX till virtuell Hårddisk när du redundansväxlar till Azure. När du växlar tillbaka till lokala virtuella datorer att fortsätta att använda VHDX-format.
BitLocker | Stöds inte | BitLocker måste inaktiveras innan du aktiverar replikering för en virtuell dator.
VM-namn | Mellan 1 och 63 tecken. Begränsat till bokstäver, siffror och bindestreck. VM-namnet måste börja och sluta med en bokstav eller en siffra. | Uppdatera värdet i VM-egenskaper i Site Recovery.
VM-typ | Generation 1<br/><br/> Generation 2 – Windows | Generation 2 virtuella datorer med en OS-disktyp av grundläggande (som innehåller en eller två datavolymer som formaterats som VHDX) och mindre än 300 GB diskutrymme stöds.<br></br>Linux Generation 2 virtuella datorer stöds inte. [Läs mer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Recovery Services-valvet åtgärder

**Åtgärd** |  **Hyper-V med Virtual Machine Manager** | **Hyper-V utan att Virtual Machine Manager**
--- | --- | ---
Flytta valvet mellan resursgrupper<br/><br/> Inom och över prenumerationer | Nej | Nej
Flytta lagring, nätverk, virtuella datorer i Azure över resursgrupper<br/><br/> Inom och över prenumerationer | Nej | Nej


## <a name="provider-and-agent"></a>Provider och agent

Kontrollera att du kör den senaste providern och agentversioner för att kontrollera distributionen är kompatibel med inställningarna i den här artikeln.

**Namn** | **Beskrivning** | **Detaljer**
--- | --- | --- | --- | ---
Azure Site Recovery-providern | Samordnar kommunikationen mellan lokala servrar och Azure <br/><br/> Hyper-V med Virtual Machine Manager: installerad på Virtual Machine Manager-servrar<br/><br/> Hyper-V utan att Virtual Machine Manager: installerad på Hyper-V-värdar| Senaste version: 5.1.2700.1 (tillgänglig från Azure portal)<br/><br/> [Senaste funktionerna och korrigeringarna](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services-agenten | Samordnar replikering mellan Hyper-V virtuella datorer och Azure<br/><br/> Installerad på lokala Hyper-V-servrar (med eller utan Virtual Machine Manager) | Senaste agenten som är tillgängliga från portalen






## <a name="next-steps"></a>Nästa steg
Lär dig hur du [förbereda Azure](tutorial-prepare-azure.md) för katastrofåterställning för lokala Hyper-V virtuella datorer.
