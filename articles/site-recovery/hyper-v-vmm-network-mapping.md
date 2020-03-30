---
title: Om Hyper-V-nätverksmappning (med VMM) med platsåterställning
description: Beskriver hur du konfigurerar nätverksmappning för haveriberedskap av virtuella hyper-virtuella datorer (hanteras i VMM-moln) till Azure, med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082566"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Förbereda nätverksmappning för hyper-v-återställning av virtuella datorer till Azure


Den här artikeln hjälper dig att förstå och förbereda för nätverksmappning när du replikerar virtuella datorer med hyper-V i VMM-moln (System Center Virtual Machine Manager) till Azure eller till en sekundär plats med hjälp av [Azure Site Recovery-tjänsten.](site-recovery-overview.md)


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Förbereda nätverksmappning för replikering till Azure

När du replikerar till Azure mappas nätverksmappning mellan VM-nätverk på en VMM-källserver och inriktar virtuella Azure-nätverk. Mappning utför följande:
-  **Nätverksanslutning**– Säkerställer att replikerade virtuella Azure-datorer är anslutna till det mappade nätverket. Alla datorer som växlar över i samma nätverk kan ansluta till varandra, även om de misslyckades i olika återställningsplaner.
- **Nätverksgateway**– Om en nätverksgateway har konfigurerats i Azure-nätverket för målet kan virtuella datorer ansluta till andra lokala virtuella datorer.

Nätverksmappning fungerar på följande sätt:

- Du mappar ett VMM-källnätverk till ett virtuellt Azure-nätverk.
- Efter redundans Azure virtuella datorer i källnätverket kommer att anslutas till den mappade målet virtuella nätverket.
- Nya virtuella datorer som läggs till i käll-VM-nätverket är anslutna till det mappade Azure-nätverket när replikeringen sker.
- Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns så ansluts den virtuella replikdatorn till det målundernätverket efter en redundansväxling.
- Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Förbereda nätverksmappning för replikering till en sekundär plats

När du replikerar till en sekundär plats mappas nätverksmappning mellan VM-nätverk på en VMM-källserver och VM-nätverk på en VMM-server för mål. Mappning utför följande:

- **Nätverksanslutning**– Ansluter virtuella datorer till lämpliga nätverk efter redundans. Repliken VM kommer att anslutas till målnätverket som är mappat till källnätverket.
- **Optimal VM-placering**– Placerar på bästa sätt repliken virtuella datorer på Hyper-V-värdservrar. Virtuella repliker placeras på värdar som kan komma åt de mappade VM-nätverken.
- **Ingen nätverksmappning**– Om du inte konfigurerar nätverksmappning ansluts inte virtuella repliker till några VM-nätverk efter redundans.

Nätverksmappning fungerar på följande sätt:

- Nätverksmappning kan konfigureras mellan VM-nätverk på två VMM-servrar eller på en enda VMM-server om två platser hanteras av samma server.
- När mappningen är korrekt konfigurerad och replikeringen är aktiverad, ansluts en virtuell dator på den primära platsen till ett nätverk och dess replik på målplatsen ansluts till det mappade nätverket.
- När du väljer ett mål-VM-nätverk under nätverksmappning i Platsåterställning visas VMM-källmolnen som använder käll-VM-nätverket, tillsammans med tillgängliga virtuella mål-nätverk för virtuella mål på målmolnen som används för skydd.
- Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns, ansluts den virtuella repliken till målundernätet efter redundans. Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.

## <a name="example"></a>Exempel

Här är ett exempel för att illustrera denna mekanism. Låt oss ta en organisation med två platser i New York och Chicago.

**Location** | **VMM-server** | **VM-nätverk** | **Mappad till**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mappad till VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Inte mappad
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mappad till VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Inte mappad

I det här exemplet:

- När en virtuell replik skapas för alla virtuella datorer som är anslutna till VMNetwork1-NewYork ansluts den till VMNetwork1-Chicago.
- När en virtuell replik skapas för VMNetwork2-NewYork eller VMNetwork2-Chicago är den inte ansluten till något nätverk.

Så här konfigureras VMM-moln i vår exempelorganisation och de logiska nätverk som är associerade med molnen.

### <a name="cloud-protection-settings"></a>Inställningar för molnskydd

**Skyddat moln** | **Skydda molnet** | **Logiskt nätverk (New York)**  
---|---|---
GoldCloud1 (På andra sätt) | GoldCloud2 (På andra) |
SilverCloud1 (Av silvermol1)| SilverCloud2 (Avsört) |
GoldCloud2 (På andra) | <p>Ej tillämpligt</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 (Avsört) | <p>Ej tillämpligt</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Logiska nätverksinställningar och VM-nätverksinställningar

**Location** | **Logiskt nätverk** | **Associerat VM-nätverk**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Inställningar för målnätverk

Baserat på dessa inställningar, när du väljer mål-VM-nätverket, visar följande tabell de alternativ som kommer att vara tillgängliga.

**Välj** | **Skyddat moln** | **Skydda molnet** | **Målnätverk tillgängligt**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 (Av silvermol1) | SilverCloud2 (Avsört) | Tillgängligt
 | GoldCloud1 (På andra sätt) | GoldCloud2 (På andra) | Tillgängligt
VMNetwork2-Chicago | SilverCloud1 (Av silvermol1) | SilverCloud2 (Avsört) | Inte tillgängligt
 | GoldCloud1 (På andra sätt) | GoldCloud2 (På andra) | Tillgängligt


Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella datorn för källan finns, ansluts den virtuella repliken till målundernätet efter redundans. Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.


### <a name="failback-behavior"></a>Felbeteende vid felning

Om du vill se vad som händer vid återställning efter fel (omvänd replikering) får vi anta att VMNetwork1-NewYork mappas till VMNetwork1-Chicago med följande inställningar.


**VM** | **Ansluten till VM-nätverk**
---|---
VM1 | VMNetwork1-nätverk
VM2 (replik av VM1) | VMNetwork1-Chicago

Med de här inställningarna ska vi granska vad som händer i ett par möjliga scenarier.

**Scenario** | **Resultat**
---|---
Ingen ändring i nätverksegenskaperna för VM-2 efter redundans. | VM-1 förblir ansluten till källnätverket.
Nätverksegenskaper för VM-2 ändras efter redundans och kopplas från. | VM-1 kopplas från.
Nätverksegenskaper för VM-2 ändras efter redundans och är anslutna till VMNetwork2-Chicago. | Om VMNetwork2-Chicago inte mappas kopplas VM-1 från.
Nätverksmappning av VMNetwork1-Chicago har ändrats. | VM-1 ansluts till nätverket som nu är mappat till VMNetwork1-Chicago.



## <a name="next-steps"></a>Nästa steg

- [Läs mer](hyper-v-vmm-networking.md) IP-adressering efter redundans till en sekundär VMM-plats.
- [Läs mer](concepts-on-premises-to-azure-networking.md) IP-adressering efter redundans till Azure.
