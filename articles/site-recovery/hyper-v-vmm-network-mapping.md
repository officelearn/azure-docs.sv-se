---
title: Om nätverksmappning för haveriberedskap för virtuella Hyper-V-datorer (med VMM) till Azure med Site Recovery | Microsoft Docs
description: Beskriver hur du ställer in nätverksmappning för haveriberedskap för Hyper-V-datorer (som hanteras i VMM-moln) till Azure med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 10/28/2018
ms.author: raynew
ms.openlocfilehash: cecc1df23ebed88db315e7de14ea850ba5297697
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212989"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Förbereda nätverksmappning för haveriberedskap för virtuella Hyper-V-datorer till Azure


Den här artikeln hjälper dig att förstå och förbereda för nätverksmappning när du replikerar virtuella Hyper-V-datorer i System Center Virtual Machine Manager (VMM)-moln till Azure eller till en sekundär plats med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Förbereda nätverksmappning för replikering till Azure

När du replikerar till Azure mappar nätverksmappningen mellan Virtuella datornätverk i en VMM-källservern och rikta in Azure-nätverk. Mappningen gör följande:
    -  **Nätverksanslutning**– ser till att replikerade virtuella Azure-datorer är anslutna till den mappade nätverksenheten. Alla datorer som redundansväxlar i samma nätverk kan ansluta till varandra, även om de inte över i olika återställningsplaner.
    - **Nätverksgateway**– om en nätverksgateway har konfigurerats på Azure-målnätverket kan virtuella datorer kan ansluta till andra lokala virtuella datorer.

Nätverksmappning fungerar på följande sätt:

- Du kan mappa en källa VMM VM-nätverk till ett Azure-nätverk.
- Nätverket ska anslutas till det mappade virtuella målnätverket efter redundansväxling av virtuella Azure-datorer i källan.
- Nya virtuella datorer läggs till VM-källnätverket ansluts till det mappade Azure-nätverket när replikeringen sker.
- Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns så ansluts den virtuella replikdatorn till det målundernätverket efter en redundansväxling.
- Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Förbereda nätverksmappning för replikering till en sekundär plats

När du replikerar till en sekundär plats mappar nätverksmappningen mellan VM-nätverk på en VMM-källservern och Virtuella datornätverk på en målserver för VMM. Mappningen gör följande:

- **Nätverksanslutning**– ansluter datorer till lämpliga nätverk efter redundansväxling. Replikerade virtuella datorn ansluts till målnätverket som är mappad till källnätverket.
- **Optimal placering av Virtuella**– optimalt placerar repliken virtuella datorer på Hyper-V-värdservrar. Virtuella replikdatorerna placeras på värdar som har åtkomst till de mappade Virtuella datornätverk.
- **Ingen nätverksmappning**– om du inte konfigurerar nätverksmappning replikering av VMs inte ska anslutas till alla Virtuella datornätverk efter redundansväxling.

Nätverksmappning fungerar på följande sätt:

- Du kan konfigurera nätverksmappning mellan Virtuella nätverk på två VMM-servrar eller på en enda VMM-server om två platser som hanteras av samma server.
- När mappningen är korrekt konfigurerad och replikering har aktiverats, en virtuell dator på den primära platsen ska anslutas till ett nätverk och dess replik på målplatsen som ska anslutas till dess mappade nätverksenheter.
- När du väljer ett mål Virtuellt datornätverk under nätverksmappning i Site Recovery visas källan VMM-moln som använder VM-källnätverket, tillsammans med tillgängliga målservrar Virtuella datornätverk på mål-moln som används för skydd.
- Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns, sedan ansluts replikerade virtuella datorn till det målundernätverket efter en redundansväxling. Om det inte finns något målundernät med ett matchande namn, ansluts den virtuella datorn till det första undernätet i nätverket.

## <a name="example"></a>Exempel

Här är ett exempel som illustrerar den här mekanismen. Låt oss ta en organisation med två platser i New York och Chicago.

**Plats** | **VMM-server** | **Virtuella datornätverk** | **Mappad till**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mappad till VMNetwork1 Chicago
 |  | VMNetwork2-NewYork | Ingen mappning
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mappad till VMNetwork1 NewYork
 | | VMNetwork2-Chicago | Ingen mappning

I det här exemplet:

- När en replik som datorn har skapats för alla virtuella datorer som är ansluten till VMNetwork1 NewYork ansluts till VMNetwork1 Chicago.
- När en replik som datorn har skapats för VMNetwork2 NewYork eller VMNetwork2 Chicago kan vara inte ansluten till något nätverk.

Här är hur VMM-moln är konfigurerat i vårt exempel-organisation och de logiska nätverk som är associerade med molnen.

### <a name="cloud-protection-settings"></a>Skyddsinställningarna för molnet

**Skyddat moln** | **Skydda molnet** | **Logiskt nätverk (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>Ej tillämpligt</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>Ej tillämpligt</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Inställningar för logiska och Virtuella nätverk

**Plats** | **Logiskt nätverk** | **Associerade VM-nätverk**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Målnätverksinställningarna

Baserat på de här inställningarna när du väljer målnätverket för virtuell dator visas i följande tabell de val som är tillgängliga.

**Välj** | **Skyddat moln** | **Skydda molnet** | **Målnätverket som är tillgängliga**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Tillgängligt
 | GoldCloud1 | GoldCloud2 | Tillgängligt
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Inte tillgängligt
 | GoldCloud1 | GoldCloud2 | Tillgängligt


Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns, sedan ansluts den replikerade virtuella datorn till det målundernätverket efter en redundansväxling. Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.


### <a name="failback-behavior"></a>Beteende för återställning efter fel

Om du vill se vad som händer när det gäller återställning efter fel (omvänd replikering), antar vi att VMNetwork1 NewYork är mappade till VMNetwork1 Chicago med följande inställningar.


**VIRTUELL DATOR** | **Ansluten till VM-nätverk**
---|---
VM1 | VMNetwork1-Network
VM2 (replik av VM1) | VMNetwork1-Chicago

Med de här inställningarna du nu ska vi se vad som händer på några möjliga scenarier.

**Scenario** | **Resultatet**
---|---
Ingen ändring i Nätverksegenskaper för VM 2 efter en redundansväxling. | VM-1 fortsätter att vara anslutna till källnätverket.
Nätverksegenskaper för VM 2 har ändrats efter redundans och är frånkopplad. | VM-1 är frånkopplad.
Nätverksegenskaper för VM 2 har ändrats efter redundans och är ansluten till VMNetwork2 Chicago. | Om VMNetwork2 Chicago har inte mappats, kopplas VM-1.
Nätverksmappning för VMNetwork1 Chicago ändras. | VM-1 ansluts till nätverket nu mappat till VMNetwork1 Chicago.



## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om](hyper-v-vmm-networking.md) IP-adresser efter redundansväxling till en sekundär VMM-plats.
- [Lär dig mer om](concepts-on-premises-to-azure-networking.md) IP-adresser efter redundansväxling till Azure.
