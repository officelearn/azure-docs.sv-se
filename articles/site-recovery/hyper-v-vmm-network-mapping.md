---
title: "Om nätverksmappningen för replikering av Hyper-V virtuella datorer i VMM-moln med Site Recovery | Microsoft Docs"
description: "Beskriver hur du ställer in nätverksmappningen för replikering av Hyper-V-datorer hanteras i VMM-moln med Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2018
ms.author: raynew
ms.openlocfilehash: 5b8ebf3bd118a7b082949b3f3c6ef60a07641ba1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="about-network-mapping-for-hyper-v-vm-with-vmm-replication"></a>Om nätverksmappningen för Hyper-V-dator med VMM-replikering 


Den här artikeln hjälper dig att förstå och planera för nätverksmappning under replikering av Hyper-V virtuella datorer i System Center Virtual Machine Manager (VMM) moln med hjälp av den [Azure Site Recovery-tjänsten](site-recovery-overview.md).

Nätverksmappningen för att replikera virtuella Hyper-V-datorer hanteras i ett VMM-moln till en sekundär VMM-moln eller till Azure.

## <a name="prepare-network-mapping-for-replication-to-azure"></a>Förbereda nätverksmappningen för replikering till Azure

När du replikerar till Azure mappar nätverksmappningen mellan Virtuella nätverk på en VMM-källservern och rikta virtuella Azure-nätverk. Mappning gör följande:
    -  **Nätverksanslutning**– säkerställer att replikerade virtuella Azure-datorer är anslutna till den mappade nätverksenheten. Alla datorer som redundansväxlar i samma nätverk kan ansluta till varandra, även om de har redundansväxlats i olika återställningsplaner.
    - **Nätverksgateway**– om en nätverksgateway har konfigurerats på Azure-målnätverket kan virtuella datorer kan ansluta till andra lokala virtuella datorer.

Nätverksmappning fungerar på följande sätt:

- Du kan mappa en källa VMM VM-nätverk till ett Azure virtual network.
- Efter redundans virtuella Azure-datorer i källan ansluts nätverk till det virtuella nätverket mappade mål.
- Nya virtuella datorer läggs till källnätverket ansluts till det mappade Azure-nätverket när replikeringen sker.
- Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns så ansluts den virtuella replikdatorn till det målundernätverket efter en redundansväxling.
- Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Förbereda nätverksmappningen för replikering till en sekundär plats

När du replikerar till en sekundär plats mappar nätverksmappningen mellan VM-nätverk på en VMM-källservern och Virtuella datornätverk på en VMM-målservern. Mappning gör följande:

- **Nätverksanslutning**– ansluter virtuella datorer till lämpliga nätverk efter redundansväxling. Replikerade virtuella datorn ansluts till målnätverket som är mappad till källnätverket.
- **Optimal placering av Virtuella**– optimalt placerar repliken virtuella datorer på Hyper-V-värdservrar. Replikdatorerna placeras på värdar som kan komma åt mappade VM-nätverk.
- **Inga nätverksmappning**– om du inte konfigurerar nätverksmappning replikering VMs ansluts inte till något VM-nätverk efter redundansväxling.

Nätverksmappning fungerar på följande sätt:

- Du kan konfigurera nätverksmappning mellan Virtuella nätverk på två VMM-servrar eller på en VMM-server om två platser hanteras av samma server.
- När mappningen är korrekt konfigurerad och replikering har aktiverats, en virtuell dator på den primära platsen ska anslutas till ett nätverk och dess replik på målplatsen ansluts till dess mappade nätverksenheter.
- När du väljer ett mål Virtuellt datornätverk under nätverksmappning i Site Recovery visas källa VMM-moln som använder källnätverket, tillsammans med tillgängliga målservrar Virtuella datornätverk på mål-moln som används för skydd.
- Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns, sedan ansluts replikerade virtuella datorn till det målundernätverket efter en redundansväxling. Om det inte finns något målundernät med ett matchande namn, ansluts den virtuella datorn till det första undernätet i nätverket.

## <a name="example"></a>Exempel

Här är ett exempel som illustrerar den här mekanismen. Låt oss ta en organisation med två platser i New York och Chicago.

**Plats** | **VMM-server** | **Virtuella datornätverk** | **Mappas till**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mappas till VMNetwork1 Chicago
 |  | VMNetwork2-NewYork | Inte mappad
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mappas till VMNetwork1 NewYork
 | | VMNetwork1-Chicago | Inte mappad

I det här exemplet:

- När en replik som virtuell dator skapas för varje virtuell dator som är ansluten till VMNetwork1 NewYork ansluts till VMNetwork1 Chicago.
- När en replik skapas VM för VMNetwork2 NewYork eller VMNetwork2 Chicago kan vara inte ansluten till något nätverk.

Här är hur VMM-moln ställs in i vårt exempelorganisation och logiska nätverk som är associerade med molnen.

### <a name="cloud-protection-settings"></a>Skyddsinställningarna för molnet

**Skyddade moln** | **Skydda molnet** | **Logiskt nätverk (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>Ej tillämpligt</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>Ej tillämpligt</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Inställningar för logiska och Virtuella nätverk

**Plats** | **Logiskt nätverk** | **Associerat VM-nätverk**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Nätverksinställningar för mål

I följande tabell visas baserat på dessa inställningar när du väljer VM målnätverket, alternativen som är tillgängliga.

**Välj** | **Skyddade moln** | **Skydda molnet** | **Målnätverket som är tillgängliga**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Tillgänglig
 | GoldCloud1 | GoldCloud2 | Tillgänglig
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Inte tillgänglig
 | GoldCloud1 | GoldCloud2 | Tillgänglig


Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns, sedan ansluts den replikerade virtuella datorn till det målundernätverket efter en redundansväxling. Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.


### <a name="failback-behavior"></a>Beteende för återställning efter fel

Om du vill se vad som händer vid återställning (omvänd replikering), anta att VMNetwork1 NewYork är mappad till VMNetwork1 Chicago med följande inställningar.


**VM** | **Ansluten till nätverket**
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

[Lär dig mer om](hyper-v-vmm-networking.md) IP-adresser efter växling till en sekundär plats för VMM.
