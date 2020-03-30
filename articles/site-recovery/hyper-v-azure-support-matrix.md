---
title: Stöd för haveriberedskap av virtuella hyper-virtuella datorer till Azure med Azure Site Recovery
description: Sammanfattar komponenter och krav som stöds för hyper-v-återställning av virtuella datorer till Azure med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 1/27/2020
ms.author: raynew
ms.openlocfilehash: d4409fe61bfe1f0a9fe74171f5b1ec471b9a6a26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258062"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Stödmatris för haveriberedskap av lokala virtuella hyper-virtuella datorer till Azure


Den här artikeln sammanfattar komponenter och inställningar som stöds för haveriberedskap för lokala virtuella hyper-virtuella datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).



## <a name="supported-scenarios"></a>Scenarier som stöds

**Scenario** | **Detaljer**
--- | ---
Hyper-V med hanteraren för virtuella datorer <br> <br>| Du kan utföra haveriberedskap till Azure för virtuella datorer som körs på Hyper-V-värdar som hanteras i fabricen System Center Virtual Machine Manager.<br/><br/> Du kan distribuera det här scenariot i Azure-portalen eller med hjälp av PowerShell.<br/><br/> När Hyper-V-värdar hanteras av Virtual Machine Manager kan du också utföra haveriberedskap till en sekundär lokal plats. Om du vill veta mer om det här scenariot läser du [den här självstudien](hyper-v-vmm-disaster-recovery.md).
Hyper-V utan hanteraren för virtuella datorer | Du kan utföra haveriberedskap till Azure för virtuella datorer som körs på Hyper-V-värdar som inte hanteras av Virtual Machine Manager.<br/><br/> Du kan distribuera det här scenariot i Azure-portalen eller med hjälp av PowerShell.

## <a name="on-premises-servers"></a>Lokala servrar

**Server** | **Krav** | **Detaljer**
--- | --- | ---
Hyper-V (körs utan hanteraren för virtuella datorer) |  Windows Server 2019, Windows Server 2016 (inklusive serverkärninstallation), Windows Server 2012 R2 med de senaste uppdateringarna | Om du redan har konfigurerat Windows Server 2012 R2 med/eller SCVMM 2012 R2 med Azure Site Recovery och planerar att uppgradera operativsystemet följer du [vägledningsdokumentationen.](upgrade-2012R2-to-2016.md) 
Hyper-V (körs med Hanteraren för virtuella datorer) | Hanteraren för virtuella datorer 2019, hanteraren för virtuella datorer 2016, hanteraren för virtuella datorer 2012 R2 | Om Virtual Machine Manager används ska Windows Server 2019-värdar hanteras i Virtual Machine Manager 2019. På samma sätt bör Windows Server 2016-värdar hanteras i Virtual Machine Manager 2016.<br/><br/> Återställning efter fel på den alternativa platsen stöds inte för Windows Server 2019-värdar.


## <a name="replicated-vms"></a>Replikerade virtuella datorer


I följande tabell sammanfattas stöd för virtuella datorer. Site Recovery stöder alla arbetsbelastningar som körs på ett operativsystem som stöds.

 **Komponent** | **Detaljer**
--- | ---
Vm-konfiguration | Virtuella datorer som replikeras till Azure måste uppfylla [Azure-kraven](#azure-vm-requirements).
Gästoperativsystem | Alla gästoperativ [som stöds för Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)..<br/><br/> Windows Server 2016 Nano Server stöds inte.


## <a name="vmdisk-management"></a>Hantering av vm/disk

**Åtgärd** | **Detaljer**
--- | ---
Ändra storlek på disk på replikerad virtuell hyper-V-dator | Stöds inte. Inaktivera replikering, gör ändringen och aktivera sedan replikeringen för den virtuella datorn.
Lägga till disk på replikerad virtuell hyper-v-dator | Stöds inte. Inaktivera replikering, gör ändringen och aktivera sedan replikeringen för den virtuella datorn.

## <a name="hyper-v-network-configuration"></a>Hyper-V-nätverkskonfiguration

**Komponent** | **Hyper-V med hanteraren för virtuella datorer** | **Hyper-V utan hanteraren för virtuella datorer**
--- | --- | ---
Värdnätverk: NIC-teaming | Ja | Ja
Värdnätverk: VLAN | Ja | Ja
Värdnätverk: IPv4 | Ja | Ja
Värdnätverk: IPv6 | Inga | Inga
Gäst-VM-nätverk: NIC Teaming | Inga | Inga
VM-nätverk för gäst: IPv4 | Ja | Ja
VM-nätverk för gäst: IPv6 | Inga | Ja
Vm-nätverk för gäst: Statisk IP (Windows) | Ja | Ja
Gäst-VM-nätverk: Statisk IP (Linux) | Inga | Inga
Gäst-VM-nätverk: Multi-NIC | Ja | Ja



## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM-nätverkskonfiguration (efter redundans)

**Komponent** | **Hyper-V med hanteraren för virtuella datorer** | **Hyper-V utan hanteraren för virtuella datorer**
--- | --- | ---
Azure ExpressRoute | Ja | Ja
ILB (på andra sätt) | Ja | Ja
Elb | Ja | Ja
Azure Traffic Manager | Ja | Ja
Multi-NIC | Ja | Ja
Reserverad IP | Ja | Ja
IPv4 | Ja | Ja
Behåll källans IP-adress | Ja | Ja
Slutpunkter för Azure Virtual Network-tjänsten<br/> (utan Azure Storage-brandväggar) | Ja | Ja
Accelererat nätverk | Inga | Inga


## <a name="hyper-v-host-storage"></a>Hyper-V-värdlagring

**Lagring** | **Hyper-V med hanteraren för virtuella datorer** | **Hyper-V utan hanteraren för virtuella datorer**
--- | --- | --- 
NFS | Ej tillämpligt | Ej tillämpligt
SMB 3.0 | Ja | Ja
SAN (ISCSI) | Ja | Ja
Multi-path (MPIO). Testad med:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM för CLARiiON | Ja | Ja

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM gästlagring

**Lagring** | **Hyper-V med hanteraren för virtuella datorer** | **Hyper-V utan hanteraren för virtuella datorer**
--- | --- | ---
Vmdk | Ej tillämpligt | Ej tillämpligt
VHD/VHDX | Ja | Ja
Generation 2 VM | Ja | Ja
EFI/UEFI<br></br>Den migrerade virtuella datorn i Azure konverteras automatiskt till en VIRTUELL BIOS-start. Den virtuella datorn bör köra Windows Server 2012 och senare endast. OS-disken bör ha upp till fem partitioner eller färre och storleken på OS-disken bör vara mindre än 300 GB.| Ja | Ja
Delad klusterdisk | Inga | Inga
Krypterad disk | Inga | Inga
NFS | Ej tillämpligt | Ej tillämpligt
SMB 3.0 | Inga | Inga
Rdm | Ej tillämpligt | Ej tillämpligt
Disk >1 TB | Ja, upp till 4 095 GB | Ja, upp till 4 095 GB
Disk: 4K logisk och fysisk sektor | Stöds inte: Gen 1/Gen 2 | Stöds inte: Gen 1/Gen 2
Disk: 4K logisk och 512 byte fysisk sektor | Ja |  Ja
Logisk volymhantering (LVM). LVM stöds endast på datadiskar. Azure tillhandahåller endast en enda OS-disk. | Ja | Ja
Volym med randig disk >1 TB | Ja | Ja
Lagringsutrymmen | Inga | Inga
Snabb lägg till/ta bort disk | Inga | Inga
Uteslut disk | Ja | Ja
Flera sökvägar (MPIO) | Ja | Ja

## <a name="azure-storage"></a>Azure Storage

**Komponent** | **Hyper-V med hanteraren för virtuella datorer** | **Hyper-V utan hanteraren för virtuella datorer**
--- | --- | ---
Lokalt redundant lagring | Ja | Ja
Geografiskt redundant lagring | Ja | Ja
Geo-redundant lagring med läsbehörighet (RA-GRS) | Ja | Ja
Kyl lagring | Inga | Inga
Varm lagring| Inga | Inga
Blockblobar | Inga | Inga
Kryptering i vila (SSE)| Ja | Ja
Kryptering i vila (CMK) <br></br> (Endast för redundans till hanterade diskar)| Ja (via PowerShell Az 3.3.0-modulen och framåt) | Ja (via PowerShell Az 3.3.0-modulen och framåt)
Premium Storage | Ja | Ja
Tjänsten Importera/exportera | Inga | Inga
Azure Storage-konton med brandvägg aktiverad | Ja. För mållagring och cacheminne. | Ja. För mållagring och cacheminne.
Ändra lagringskonto | Nej. Målet Azure Storage-konto kan inte ändras när replikering. Om du vill ändra inaktiverar och aktiverar och aktiverar sedan haveriberedskapen igen. | Inga


## <a name="azure-compute-features"></a>Azure-beräkningsfunktioner

**Funktion** | **Hyper-V med hanteraren för virtuella datorer** | **Hyper-V utan hanteraren för virtuella datorer**
--- | --- | ---
Tillgänglighetsuppsättningar | Ja | Ja
Nav | Ja | Ja  
Hanterade diskar | Ja, för redundans.<br/><br/> Återställning av hanterade diskar stöds inte. | Ja, för redundans.<br/><br/> Återställning av hanterade diskar stöds inte.

## <a name="azure-vm-requirements"></a>Virtuella Azure VMware-datorer

Lokala virtuella datorer som du replikerar till Azure måste uppfylla azure vm-kraven som sammanfattas i den här tabellen.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
Gästoperativsystem | Site Recovery stöder alla operativsystem som [stöds av Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Förkunskapskontroll misslyckas om det inte finns något stöd.
Arkitektur för gästoperativsystem | 32-bitars (Windows Server 2008)/64-bitars | Förkunskapskontroll misslyckas om det inte finns något stöd.
Storlek på operativsystemdisk | Upp till 2 048 GB för virtuella datorer med generation 1.<br/><br/> Upp till 300 GB för virtuella generationer 2 virtuella datorer.  | Förkunskapskontroll misslyckas om det inte finns något stöd.
Antal operativsystemdiskar | 1 | Förkunskapskontroll misslyckas om det inte finns något stöd.
Antal datadiskar | 16 eller mindre  | Förkunskapskontroll misslyckas om det inte finns något stöd.
VHD-storlek för datadisk | Upp till 4 095 GB | Förkunskapskontroll misslyckas om det inte finns något stöd.
Nätverkskort | Flera nätverkskort stöds |
Delad VHD | Stöds inte | Förkunskapskontroll misslyckas om det inte finns något stöd.
FC-disk | Stöds inte | Förkunskapskontroll misslyckas om det inte finns något stöd.
Format för hårddisk | VHD <br/><br/> VHDX (PÅ) | Site Recovery konverterar automatiskt VHDX till VHD när du växlar över till Azure. När du växlar tillbaka till lokalt fortsätter de virtuella datorerna att använda VHDX-formatet.
BitLocker | Stöds inte | BitLocker måste inaktiveras innan du aktiverar replikering för en virtuell dator.
VM-namn | Mellan 1 och 63 tecken. Begränsat till bokstäver, siffror och bindestreck. VM-namnet måste börja och sluta med en bokstav eller en siffra. | Uppdatera värdet i vm-egenskaperna i Site Recovery.
VM-typ | Generation 1<br/><br/> Generation 2 – Windows | Generation 2 virtuella datorer med en OS-disktyp av grundläggande (som innehåller en eller två datavolymer som är formaterade som VHDX) och mindre än 300 GB diskutrymme stöds.<br></br>Virtuella datorer för Linux Generation 2 stöds inte. [Läs mer](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Valvåtgärder för Återställningstjänster

**Åtgärd** |  **Hyper-V med VMM** | **Hyper-V utan VMM**
--- | --- | ---
Flytta valv över resursgrupper<br/><br/> Inom och mellan prenumerationer | Inga | Inga
Flytta lagring, nätverk, virtuella Azure-datorer mellan resursgrupper<br/><br/> Inom och mellan prenumerationer | Inga | Inga

> [!NOTE]
> När du replikerar virtuella hyper-virtuella datorer från lokala till Azure kan du replikera till endast en AD-klient från en specifik miljö - Hyper-V-plats eller Hyper-V med VMM beroende på vad som är tillämpligt.


## <a name="provider-and-agent"></a>Provider och agent

Kontrollera att distributionen är kompatibel med inställningarna i den här artikeln genom att kontrollera att du kör de senaste provider- och agentversionerna.

**Namn** | **Beskrivning** | **Detaljer**
--- | --- | --- 
Azure Site Recovery-provider | Samordnar kommunikationen mellan lokala servrar och Azure <br/><br/> Hyper-V med Hanteraren för virtuella datorer: Installerad på servrar för Virtual Machine Manager<br/><br/> Hyper-V utan Virtual Machine Manager: Installerat på Hyper-V-värdar| Senaste versionen: 5.1.2700.1 (tillgänglig från Azure-portalen)<br/><br/> [Senaste funktionerna och korrigeringar](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services-agenten | Koordinerar replikering mellan virtuella datorer och Azure<br/><br/> Installeras på lokala Hyper-V-servrar (med eller utan Virtual Machine Manager) | Senaste agent tillgänglig från portalen






## <a name="next-steps"></a>Nästa steg
Lär dig hur du [förbereder Azure](tutorial-prepare-azure.md) för haveriberedskap av lokala virtuella hyper-virtuella datorer.
