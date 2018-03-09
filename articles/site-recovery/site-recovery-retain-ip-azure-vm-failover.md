---
title: "Behålla IP-adresser när redundansväxla virtuella Azure-datorer till en annan Azure-region | Microsoft Docs"
description: "Beskriver hur du behålla IP-adresser för redundanssituationer från Azure till Azure med Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 15f87ba87d90cee765f52d3188796bc1ff7b8a35
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>Kvarhållning av IP-adress för redundans för virtuell Azure-dator

Azure Site Recovery kan katastrofåterställning för virtuella Azure-datorer. När växling från en Azure-region till en annan, kräver kunder ofta kvarhållning av deras IP-konfigurationer. Site Recovery efterliknar som standard källa virtuella nätverk och undernätsstruktur när du skapar de här resurserna på mål-region. Site Recovery görs även ett bästa prestanda försöker etablera samma privata IP-Adressen för målet VM, om den IP inte är redan blockeras av en Azure-resurs eller en replikerad virtuell dator för virtuella Azure-datorer konfigurerade med statiska privata IP-adresser.

För enkla program är ovanstående standardkonfigurationen allt som behövs. För mer komplexa företagsprogram behöva kunder etablera ytterligare nätverksresurser för att säkerställa postredundans anslutning med andra komponenter i infrastrukturen. Den här artikeln förklarar nätverkskrav för misslyckas över virtuella Azure-datorer från en region till en annan samtidigt som VM-IP-adresser.

## <a name="azure-to-azure-connectivity"></a>Azure-Azure-anslutning

För det första scenariot Vi anser att **företag A** som har alla dess infrastruktur körs i Azure. För verksamhetskontinuitet och efterlevnad affärsskäl, **företag A** bestämmer sig för att använda Azure Site Recovery för att skydda sina program.

Krav för IP-Kvarhållning (t.ex. för program Bindningar) får har företag A samma virtuella nätverk och undernät struktur på mål-region. Att ytterligare minska recovery tid mål för Återställningstid, **företag A** använder replik noder för SQL Always ON, domänkontrollanter, etc. och dessa replik noder placeras i ett annat virtuellt nätverk på mål-region. Med ett annat adressutrymme för replik-noder kan **företag A** för att upprätta VPN plats-till-plats-anslutning mellan käll- och regioner, vilket annars inte skulle vara möjligt om samma-adressutrymmet som används i båda ändar .

Här är nätverksarkitekturen ser ut före redundans:
- Programmets virtuella datorer finns i Azure Östasien använder Azure-nätverk med adressutrymme 10.1.0.0/16. Det här virtuella nätverket har namnet **källa VNet**.
- Programbelastningar delas över tre undernät – 10.1.0.0/24, 10.1.1.0/24, 10.1.2.0/24, respektive med namnet **undernät 1**, **undernät 2**, **undernät 3**.
- Azure Sydostasien är målet region och har ett virtuellt nätverk för återställning som efterliknar adresskonfiguration för adressutrymmet och undernätsegenskaperna på källan. Det här virtuella nätverket har namnet **Recovery VNet**.
- Replik noder, till exempel de som krävs för alltid på domänkontrollanten, etc. placeras i ett virtuellt nätverk med adressutrymme 20.1.0.0/16 i undernätet 4 med adressen 20.1.0.0/24. Det virtuella nätverket har namnet **Azure VNet** och är på Azure Sydostasien.
- **Datakällan VNet** och **Azure VNet** är anslutna via VPN plats-till-plats-anslutning.
- **Återställning VNet** inte är kopplad till något virtuellt nätverk.
- **Företagets A** tilldelar/verifierar mål-IP-adress för replikerade objekt. I det här exemplet är samma som käll-IP för varje virtuell mål-IP.

![Azure-Azure-anslutningen innan växling vid fel](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Fullständig region växling vid fel

Vid ett regionalt strömavbrott **företag A** kan återställa dess hela distributionen snabbt och enkelt med hjälp av Azure Site Recovery kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md). Har redan angetts mål-IP-adress för varje virtuell dator innan redundans, **företag A** kan dirigera redundans och automatisera anslutningen upprättas mellan Recovery virtuella nätverk och Azure Vnet som visas i den under diagrammet.

![Azure-Azure-anslutningen fullständig region redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover.png)

Beroende på kraven för application anslutningar mellan två Vnet på målregionen som kan vara etablerade före, under (som ett steg) eller efter växling vid fel. Använd [återställningsplaner](site-recovery-create-recovery-plans.md) att lägga till skript och definiera ordningen för växling vid fel.

Företag A har också välja att använda VNet-peering eller plats-till-plats VPN för att upprätta en anslutning mellan Recovery virtuella nätverk och Azure VNet. VNET-peering använder ingen VPN-gateway och har även andra restriktioner. Dessutom beräknas [prissättningen för VNET-peering](https://azure.microsoft.com/pricing/details/virtual-network) på ett annat sätt jämfört med [prissättningen för VPN-gatewayen mellan virtuella nätverk](https://azure.microsoft.com/pricing/details/vpn-gateway). För växling vid fel är det att efterlikna käll-anslutningen, inklusive anslutningstypen för att minimera oförutsägbart incidenter som härrör från ändringar i nätverket.

### <a name="isolated-application-failover"></a>Isolerat program växling vid fel

Under vissa förhållanden kan användare behöva redundans delar av deras program i infrastrukturen. Ett exempel på misslyckas under ett visst program eller en nivå som finns sparad i ett dedikerat undernät. Även om ett undernät failover med IP-kvarhållning går rekommenderas det inte för de flesta situationer när det blir avsevärt anslutningen inkonsekvenser. Du förlorar också undernäts-anslutningen till andra undernät inom samma virtuella Azure-nätverket.

Ett bättre sätt att redovisa på undernätsnivå programkrav för växling vid fel är att använda olika mål-IP-adresser för redundans (om det krävs anslutning till andra undernät på källan virtuella nätverk) eller isolera program i sin egen dedikerade virtuella nätverk på källan. Med den senare metoden du upprätta en anslutning mellan nätverk på käll- och emulera samma när redundansväxlingen till mål-region.

Om du vill skapa enskilda program för återhämtning, är det bäst att innehålla ett program i sitt eget dedikerade virtuella nätverk och upprätta en anslutning mellan dessa virtuella nätverk som krävs. Detta ger isolerat program redundans men behålla ursprungliga privata IP-adresser.

Sedan pre-redundans-konfiguration ser ut som följer:
- Programmets virtuella datorer finns i Azure Östasien använder Azure-nätverk med adressutrymme 10.1.0.0/16 för det första programmet och 15.1.0.0/16 för det andra programmet. Virtuella nätverk är namngivna **källa VNet1** och **källa VNet2** för programmets första och andra respektive.
- Varje VNet är ytterligare uppdelad i två undernät.
- Azure Sydostasien är målet region och recovery virtuella nätverk Recovery VNet1 och återställning VNet2.
- Replik noder, till exempel de som krävs för Always On, domänkontrollant, etc. placeras i ett virtuellt nätverk med adressutrymme 20.1.0.0/16 inuti **undernät 4** med adressen 20.1.0.0/24. Det virtuella nätverket kallas Azure VNet och är på Azure Sydostasien.
- **Datakällan VNet1** och **Azure VNet** är anslutna via VPN plats-till-plats-anslutning. På liknande sätt **källa VNet2** och **Azure VNet** också är anslutna via VPN plats-till-plats-anslutning.
- **Datakällan VNet1** och **källa VNet2** också ansluten via S2S VPN i det här exemplet. Eftersom två Vnet i samma region, kan VNet-peering också användas i stället för S2S VPN.
- **Återställning VNet1** och **Recovery VNet2** är inte kopplad till något virtuellt nätverk.
- Om du vill minska recovery tid mål för Återställningstid VPN-gatewayer har konfigurerats på **Recovery VNet1** och **Recovery VNet2** före redundans.

![Azure-Azure-anslutningen isolerade program före redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover.png)

Vid en katastrof situation som påverkar endast ett program (i det här exemplet sparades i källan VNet2), kan företag A återställa programmet på följande sätt:
- VPN-anslutningar mellan **källa VNet1** och **källa VNet2**, och mellan **källa VNet2** och **Azure VNet** är frånkopplad.
- VPN-anslutningar upprättas mellan **källa VNet1** och **Recovery VNet2**, och mellan **Recovery VNet2** och **Azure VNet**.
- Virtuella datorer från **källa VNet2** redundansväxlats till **Recovery VNet2**.

![Azure-Azure-anslutningen isolerade program efter växling vid fel](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover.png)

Den ovan isolerade redundans exempel kan utökas för att inkludera fler program och nätverksanslutningar. Rekommenderar vi att du följer en liknande-liknande anslutning modell, så långt möjligt när redundansväxla från källan till målet.

### <a name="further-considerations"></a>Ytterligare överväganden

VPN-gatewayer använda offentliga IP-adresser och gateway-hopp upprätta anslutningar. Om du inte vill använda offentliga IP-Adressen och/eller om du vill undvika extra hopp kan du nu använda globala VNet-Peering för peer-virtuella nätverk i Azure-regioner.

Den här funktionen är för närvarande i förhandsversion och utökas för att stödja fler regioner – aktivera direktanslutning VM-till-VM utan inblandning av alla offentliga internet eller några extra hopp.

Mer information finns i den [peering dokumentationen](../virtual-network/tutorial-connect-virtual-networks-portal.md#register) och [priser](https://azure.microsoft.com/pricing/details/virtual-network/).

## <a name="on-premises-to-azure-connectivity"></a>På lokal-till-Azure-anslutning

För det andra scenariot Vi anser att **företag B** som har en del av sin infrastruktur för program som körs på Azure och resten körs lokalt. För verksamhetskontinuitet och efterlevnad affärsskäl, **företag B** bestämmer sig för att använda Azure Site Recovery för att skydda sina program som körs i Azure.

Här är nätverksarkitekturen ser ut före redundans:
- Programmets virtuella datorer finns i Azure Östasien använder Azure-nätverk med adressutrymme 10.1.0.0/16. Det här virtuella nätverket har namnet **källa VNet**.
- Programbelastningar delas över tre undernät – 10.1.0.0/24, 10.1.1.0/24, 10.1.2.0/24, respektive med namnet **undernät 1**, **undernät 2**, **undernät 3**.
- Azure Sydostasien är målet region och har ett virtuellt nätverk för återställning som efterliknar adresskonfiguration för adressutrymmet och undernätsegenskaperna på källan. Det här virtuella nätverket har namnet **Recovery VNet**.
- Virtuella datorer i Azure Östasien är anslutna till lokala datacenter via ExpressRoute eller plats-till-plats-VPN.
- Om du vill minska recovery tid mål för Återställningstid etablerar företag B gateways på Recovery VNet i Azure Sydostasien före redundans.
- **Företag B** tilldelar/verifierar mål-IP-adress för replikerade objekt. I det här exemplet är mål-IP samma som käll-IP för varje virtuell dator

![På lokal-till-Azure-anslutningen innan växling vid fel](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Fullständig region växling vid fel

Vid ett regionalt strömavbrott **företag B** kan återställa dess hela distributionen snabbt och enkelt med hjälp av Azure Site Recovery kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md). Har redan angetts mål-IP-adress för varje virtuell dator innan redundans, **företag B** kan dirigera redundans och automatisera anslutningen upprättas mellan Recovery virtuella nätverk och lokala datacenter som visas i den under diagrammet.

Den ursprungliga anslutningen mellan Azure Östasien och lokala datacenter ska kopplas bort innan du upprättar anslutningen mellan Azure Sydostasien och lokala datacenter. Lokal routning även konfigureras om för att peka till mål-region och gateways efter växling vid fel.

![På lokal-till-Azure-anslutningen efter växling vid fel](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover.png)

### <a name="subnet-failover"></a>Undernät växling vid fel

Till skillnad från Azure till Azure-scenario som beskrivs för **företag A**, redundans på undernätsnivå inte är möjligt i det här fallet för **företag B**. Detta är eftersom adressutrymmet på käll- och virtuella nätverk är samma och den ursprungliga källan till lokal anslutning är aktiv.

För att uppnå programmet, rekommenderas det att varje program finns i sin egen dedikerade virtuella Azure-nätverket. Program kan sedan flyttas över avskilt och lokalerna till datakällanslutningar kan vidarebefordras till mål-region som beskrivs ovan.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [återställningsplaner](site-recovery-create-recovery-plans.md).
