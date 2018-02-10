---
title: "Om nätverksmappningen för Hyper-V VM-replikering med Site Recovery | Microsoft Docs"
description: "Konfigurera nätverksmappning för Hyper-V replikering av virtuella datorer från ett lokalt datacenter till Azure eller till en sekundär plats."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/07/2018
ms.author: raynew
ms.openlocfilehash: d56f8f5bfb40c1c43090f43e119bf9b98918d6e5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="about-network-mapping-for-hyper-v-vm-replication"></a>Om nätverksmappningen för Hyper-V VM-replikering


Den här artikeln hjälper dig att förstå och planera för nätverk mappning vid replikering av Hyper-V virtuella datorer till Azure eller till en sekundär plats, med hjälp av [Azure Site Recovery-tjänsten](site-recovery-overview.md).

När du har läst den här artikeln efter eventuella kommentarer längst ned i den här artikeln eller tekniska frågor om den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="network-mapping-for-replication-to-azure"></a>Nätverksmappningen för replikering till Azure

Nätverksmappning används vid replikering av Hyper-V virtuella datorer (hanteras i VMM) till Azure. Mappningar för mappning mellan Virtuella nätverk på en VMM-källservern för nätverk och Azure-målnätverken. Mappning gör följande:

- **Nätverksanslutning**– säkerställer att replikerade virtuella Azure-datorer är anslutna till den mappade nätverksenheten. Alla datorer som redundansväxlar i samma nätverk kan ansluta till varandra, även om de har redundansväxlats i olika återställningsplaner.
- **Nätverksgateway**– om en nätverksgateway har konfigurerats på Azure-målnätverket kan virtuella datorer kan ansluta till andra lokala virtuella datorer.

Tänk på följande:

- Du kan mappa en källa VMM VM-nätverk till ett Azure virtual network.
- Efter redundans virtuella Azure-datorer i källan ansluts nätverk till det virtuella nätverket mappade mål.
- Nya virtuella datorer läggs till källnätverket ansluts till det mappade Azure-nätverket när replikeringen sker.
- Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns så ansluts den virtuella replikdatorn till det målundernätverket efter en redundansväxling.
- Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.


## <a name="network-mapping-for-replication-to-a-secondary-datacenter"></a>Nätverksmappningen för replikering till ett sekundärt datacenter

Nätverksmappning används vid replikering av Hyper-V virtuella datorer (hanteras i System Center Virtual Machine Manager (VMM)) till ett sekundärt datacenter. Nätverksmappningen mappar mellan VM-nätverk på en VMM-källservern och Virtuella datornätverk på en VMM-målservern. Mappning gör följande:

- **Nätverksanslutning**– ansluter virtuella datorer till lämpliga nätverk efter redundansväxling. Replikerade virtuella datorn ansluts till målnätverket som är mappad till källnätverket.
- **Optimal placering**– optimalt placerar repliken virtuella datorer på Hyper-V-värdservrar. Replikdatorerna placeras på värdar som kan komma åt mappade VM-nätverk.
- **Inga nätverksmappning**– om du inte konfigurerar nätverksmappning replikering VMs ansluts inte till något VM-nätverk efter redundansväxling.

Tänk på följande:

- Du kan konfigurera nätverksmappning mellan Virtuella nätverk på två VMM-servrar eller på en VMM-server om två platser hanteras av samma server.
- När mappningen är korrekt konfigurerad och replikering har aktiverats, en virtuell dator på den primära platsen ska anslutas till ett nätverk och dess replik på målplatsen ansluts till dess mappade nätverksenheter.
-
- Om nätverk har konfigurerats korrekt i VMM när du väljer ett mål Virtuellt datornätverk under nätverksmappning, visas källa VMM-moln som använder källnätverket, tillsammans med tillgängliga målservrar Virtuella datornätverk på mål-moln som används för skydd.
- Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns, sedan ansluts den replikerade virtuella datorn till det målundernätverket efter en redundansväxling. Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.



### <a name="example"></a>Exempel

Här är ett exempel som illustrerar den här mekanismen. Låt oss ta en organisation med två platser i New York och Chicago.

**Plats** | **VMM-server** | **Virtuella datornätverk** | **Mappas till**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mappas till VMNetwork1 Chicago
 |  | VMNetwork2-NewYork | Inte mappad
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mappas till VMNetwork1 NewYork
 | | VMNetwork1-Chicago | Inte mappad

I det här exemplet:

- När en replikerad virtuell dator skapas för en virtuell dator som är ansluten till VMNetwork1 NewYork ansluts till VMNetwork1 Chicago.
- När en replikerad virtuell dator har skapats för VMNetwork2 NewYork eller VMNetwork2 Chicago kan ansluten den inte till något nätverk.

Här är hur VMM-moln ställs in i vårt exempelorganisation och logiska nätverk som är associerade med molnen.

#### <a name="cloud-protection-settings"></a>Skyddsinställningarna för molnet

**Skyddade moln** | **Skydda molnet** | **Logiskt nätverk (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>Ej tillämpligt</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>Ej tillämpligt</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

#### <a name="logical-and-vm-network-settings"></a>Inställningar för logiska och Virtuella nätverk

**Plats** | **Logiskt nätverk** | **Associerat VM-nätverk**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

#### <a name="target-network-settings"></a>Nätverksinställningar för mål

I följande tabell visas baserat på dessa inställningar när du väljer VM målnätverket, alternativen som är tillgängliga.

**Välj** | **Skyddade moln** | **Skydda molnet** | **Målnätverket som är tillgängliga**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Tillgänglig
 | GoldCloud1 | GoldCloud2 | Tillgänglig
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Inte tillgänglig
 | GoldCloud1 | GoldCloud2 | Tillgänglig


Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns, sedan ansluts den replikerade virtuella datorn till det målundernätverket efter en redundansväxling. Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.


#### <a name="failback-behavior"></a>Beteende för återställning efter fel

Om du vill se vad som händer vid återställning (omvänd replikering), anta att VMNetwork1 NewYork är mappad till VMNetwork1 Chicago med följande inställningar.


**Virtuell dator** | **Ansluten till nätverket**
---|---
VM1 | VMNetwork1-Network
VM2 (replik av VM1) | VMNetwork1-Chicago

Med dessa inställningar nu ska vi se vad som händer på några möjliga scenarier.

**Scenario** | **Resultatet**
---|---
Ingen ändring i Nätverksegenskaper för VM-2 efter växling vid fel. | VM-1 fortfarande är ansluten till nätverket källa.
Nätverksegenskaper för VM-2 har ändrats efter växling vid fel och har kopplats från. | VM-1 är frånkopplad.
Nätverksegenskaper för VM-2 har ändrats efter växling vid fel och är ansluten till VMNetwork2 Chicago. | Om VMNetwork2 Chicago har inte mappats, kopplas VM-1.
Nätverksmappningen för VMNetwork1 Chicago ändras. | VM-1 ansluts till nätverket nu mappat till VMNetwork1 Chicago.



## <a name="next-steps"></a>Nästa steg

Lär dig mer om [planera nätverksinfrastrukturen](site-recovery-network-design.md).
