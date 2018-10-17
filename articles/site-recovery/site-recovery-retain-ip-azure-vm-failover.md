---
title: Behålla IP-adresser när redundansväxla virtuella Azure-datorer till en annan Azure-region | Microsoft Docs
description: Beskriver hur du behålla IP-adresser för redundansscenarier från Azure till Azure med Azure Site Recovery
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/16/2018
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 4260a4487480c42b518374965d8deda1e7803e62
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353330"
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>Kvarhållning av IP-adress för Azure VM-redundans

Azure Site Recovery kan haveriberedskap för virtuella Azure-datorer. När redundansväxlingen från en Azure-region till en annan, kräver kunder ofta kvarhållning av deras IP-konfigurationer. Site Recovery kan imiterar som standard virtuellt Källnätverk och undernätets struktur när du skapar dessa resurser i målregionen. Site Recovery gör även bästa förmåga att försöka att etablera den samma privata IP-Adressen på den Virtuella, måldatorn om den IP inte redan har blockerats av en Azure-resurs eller en replikerad virtuell dator för Azure-datorer som har konfigurerats med statiska privata IP-adresser.

För enkla program är standardkonfigurationen ovan allt som krävs. För mer komplexa företagsprogram, kan kunderna behöva etablera ytterligare nätverksresurser som anslutningen ska fungera efter en redundansväxling med andra komponenter i sin infrastruktur. Den här artikeln förklarar nätverkskrav för misslyckas över Azure virtuella datorer från en region till en annan samtidigt som du behåller VM-IP-adresser.

## <a name="azure-to-azure-connectivity"></a>Azure till Azure-anslutning

För det första scenariot Vi anser **företag A** som har alla dess infrastruktur som körs i Azure. För kontinuitet och efterlevnad affärsskäl, **företag A** bestämmer sig för att använda Azure Site Recovery för att skydda sina program.

Med krav på IP-Kvarhållning (t.ex. för program-Bindningar) kan har företag A samma virtuella nätverk och undernät struktur på målregion. Att ytterligare minska återställningstid (RTO), **företag A** repliken noder för SQL Always ON, domänkontrollanter osv och dessa repliken noder är placerade i ett annat virtuellt nätverk på målregion. Med hjälp av en annan adressutrymme för replik-noder kan **företag A** för att upprätta VPN plats-till-plats-anslutning mellan käll- och regioner som annars inte skulle vara möjligt om samma-adressutrymmet som används i båda ändar .

Här är nätverksarkitekturen ser det ut före redundans:
- Programmet virtuella datorer finns i Azure östra Asien, med ett Azure-nätverk med adress utrymme 10.1.0.0/16. Det här virtuella nätverket har namnet **Källnätverk**.
- Programarbetsbelastningar är uppdelade i alla tre undernät – 10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24, respektive med namnet **undernät 1**, **undernät 2**, **undernät 3**.
- Azure Sydostasien är målregionen och har ett virtuellt nätverk för återställning som efterliknar adresskonfiguration för adressutrymmet och undernätsegenskaperna på källdatorn. Det här virtuella nätverket har namnet **Recovery VNet**.
- Repliken noder, till exempel de som krävs för Always On, domänkontrollant, o.s.v. placeras i ett virtuellt nätverk med adress utrymme 10.2.0.0/16 i undernätet 4 med adressen 10.2.4.0/24. Det virtuella nätverket har namnet **virtuella Azure-nätverket** och är på Azure Sydostasien.
- **Datakällan VNet** och **virtuella Azure-nätverket** är anslutna via VPN plats-till-plats-anslutning.
- **Recovery VNet** är inte kopplad till något virtuellt nätverk.
- **Företagets A** tilldelar/verifierar mål-IP-adress för replikerade objekt. För det här exemplet är mål-IP samma som käll-IP för varje virtuell dator.

![Azure till Azure-anslutningen innan redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Redundans fullständig regioner

I händelse av ett regionalt strömavbrott **företag A** kan återställa dess hela distributionen snabbt och enkelt med Azure Site Recoverys kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md). Har redan angetts mål-IP för varje virtuell dator innan du redundansväxlar, **företag A** kan samordna redundans och automatisera anslutningen upprättas mellan Recovery virtuellt nätverk och Azure Vnet, enligt den nedanstående diagram.

![Redundans i Azure till Azure-anslutningen fullständig regioner](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)

Beroende på kraven för application anslutningar mellan de två virtuella nätverken i målregionen kan vara etablerad innan, under (som ett mellanliggande steg) eller efter redundans. Använd [återställningsplaner](site-recovery-create-recovery-plans.md) att lägga till skript och definiera redundansordningen.

Företag A har också välja att använda VNet-peering eller plats-till-plats-VPN för att upprätta en anslutning mellan Recovery virtuellt nätverk och virtuella Azure-nätverket. VNET-peering använder ingen VPN-gateway och har även andra restriktioner. Dessutom beräknas [prissättningen för VNet-peering](https://azure.microsoft.com/pricing/details/virtual-network) på ett annat sätt jämfört med [prissättningen för VPN Gateway mellan virtuella nätverk](https://azure.microsoft.com/pricing/details/vpn-gateway). För redundans är det att efterlikna käll-anslutning, inklusive anslutningstyp för att minimera oförutsägbara incidenter som härrör från ändringar i nätverket.

### <a name="isolated-application-failover"></a>Isolerat program redundans

Under vissa förhållanden kan användare behöva redundans delar av programinfrastrukturen sina. Ett exempel på misslyckas under ett visst program eller en nivå som finns sparad i ett dedikerat undernät. Redundans med kvarhållning av IP-undernätet är möjligt, är det inte lämpligt för de flesta situationer, eftersom det ökar avsevärt anslutning inkonsekvenser. Du kommer också att förlora undernäts-anslutningen till andra undernät inom samma Azure-nätverk.

Ett bättre sätt att redovisa på undernätsnivå programkrav för växling vid fel är att använda annat mål-IP-adresser för redundans (om det krävs anslutning till andra undernät på virtuellt Källnätverk) och isolera varje program i sin egen dedikerade virtuella nätverk på källdatorn. Du kan använda den senare metoden för att fastställa mellan nätverksanslutning på källan och emulera samma när redundansväxla till målregionen.

Om du vill skapa enskilda program för återhämtning, är det bäst att innehålla ett program i sitt eget dedikerade virtuella nätverk och upprätta en anslutning mellan dessa virtuella nätverk som krävs. Detta kan isolerat program redundans behåller sitt ursprungliga privata IP-adresser.

Sedan före redundans konfigurationen ser ut så här:
- Programmet virtuella datorer finns i Azure östra Asien, med ett Azure-nätverk med adress utrymme 10.1.0.0/16 för det första programmet och 10.2.0.0/16 för det andra programmet. De virtuella nätverken är namngivna **källa VNet1** och **källa VNet2** för det första och andra programmet, respektive.
- Varje virtuellt nätverk är ytterligare uppdelat i två undernät.
- Azure Sydostasien är målregionen och har virtuella nätverk i recovery Recovery VNet1 och Recovery VNet2.
- Repliken noder, till exempel de som krävs för Always On, domänkontrollant, etc. placeras i ett virtuellt nätverk med adress utrymme 10.3.0.0/16 inuti **undernät 4** med adressen 10.3.4.0/24. Det virtuella nätverket kallas virtuella Azure-nätverket och är på Azure Sydostasien.
- **Käll-VNet1** och **virtuella Azure-nätverket** är anslutna via VPN plats-till-plats-anslutning. På samma sätt **källa VNet2** och **virtuella Azure-nätverket** även är anslutna via VPN plats-till-plats-anslutning.
- **Käll-VNet1** och **källa VNet2** även är anslutna via S2S VPN i det här exemplet. Eftersom de två virtuella nätverken är i samma region är kan VNet-peering också användas i stället för S2S VPN.
- **Recovery VNet1** och **Recovery VNet2** inte är anslutna med andra virtuella nätverket.
- För att minska återställningstid (RTO), VPN-gatewayer har konfigurerats på **Recovery VNet1** och **Recovery VNet2** före redundans.

![Azure till Azure-anslutningen isolerade program innan redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

I händelse av en katastrof situation som påverkar endast ett program (i det här exemplet förvaras i källan VNet2), kan företag A återställa ett program på följande sätt:
- VPN-anslutningar mellan **källa VNet1** och **källa VNet2**, och mellan **källa VNet2** och **virtuella Azure-nätverket** kopplas bort.
- VPN-anslutningar upprättas mellan **källa VNet1** och **Recovery VNet2**, och mellan **Recovery VNet2** och **virtuella Azure-nätverket**.
- Virtuella datorer från **källa VNet2** växlas över till **Recovery VNet2**.

![Azure till Azure-anslutningen isolerade program efter en redundansväxling](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)

Den ovan isolerade redundans exempel kan utökas för att inkludera fler program och nätverksanslutningar. Rekommendationen är att följa en liknande-liknande anslutning modell, så långt möjligt när redundansväxlingen från källan till målet.

### <a name="further-considerations"></a>Ytterligare överväganden

VPN-Gateways använda offentliga IP-adresser och gateway hopp att upprätta anslutningar. Om du inte vill använda offentliga IP-Adressen och/eller vill undvika extra hopp kan du använda Azure [virtuell nätverkspeering](../virtual-network/virtual-network-peering-overview.md) peer-koppla virtuella nätverk över [Azure-regioner som stöds](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="on-premises-to-azure-connectivity"></a>Anslutning från plats-till-Azure

För det andra scenariot är vi anser **företag B** som har en del av dess infrastruktur som körs på Azure och resten som körs lokalt. För kontinuitet och efterlevnad affärsskäl, **företag B** bestämmer sig för att använda Azure Site Recovery för att skydda de program som körs i Azure.

Här är nätverksarkitekturen ser det ut före redundans:
- Programmet virtuella datorer finns i Azure östra Asien, med ett Azure-nätverk med adress utrymme 10.1.0.0/16. Det här virtuella nätverket har namnet **Källnätverk**.
- Programarbetsbelastningar är uppdelade i alla tre undernät – 10.1.1.0/24, 10.1.2.0/24, 10.1.3.0/24, respektive med namnet **undernät 1**, **undernät 2**, **undernät 3**.
- Azure Sydostasien är målregionen och har ett virtuellt nätverk för återställning som efterliknar adresskonfiguration för adressutrymmet och undernätsegenskaperna på källdatorn. Det här virtuella nätverket har namnet **Recovery VNet**.
- Virtuella datorer i Azure Östasien är anslutna till lokala datacenter via ExpressRoute eller VPN för plats-till-plats.
- För att minska återställningstid (RTO), etablerar företag B gateways på Recovery VNet i Azure Sydostasien före redundans.
- **Företag B** tilldelar/verifierar mål-IP-adress för replikerade objekt. I det här exemplet är mål-IP samma som käll-IP för varje virtuell dator

![På-plats-till-Azure-anslutningen innan redundans](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>Redundans fullständig regioner

I händelse av ett regionalt strömavbrott **företag B** kan återställa dess hela distributionen snabbt och enkelt med Azure Site Recoverys kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md). Har redan angetts mål-IP för varje virtuell dator innan du redundansväxlar, **företag B** kan samordna redundans och automatisera anslutningen upprättas mellan Recovery virtuellt nätverk och lokala datacenter, enligt den nedanstående diagram.

Den ursprungliga anslutningen mellan Azure-Östasien och datacenter på plats ska kopplas bort innan du upprättar anslutningen mellan Azure, Sydostasien och lokala datacenter. Den lokala routning konfigureras också för att peka till målregion och gatewayer efter redundans.

![På-plats-till-Azure-anslutning efter redundansväxling](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

### <a name="subnet-failover"></a>Undernät redundans

Till skillnad från Azure till Azure-scenariot som beskrivs för **företag A**, undernätverksnivån redundans är inte möjligt i det här fallet för **företag B**. Detta är eftersom adressutrymmet på käll- och virtuella nätverk är samma och den ursprungliga källan till den lokala anslutningen är aktiv.

För att uppnå programåterhämtning, rekommenderas det att varje program finns i en egen dedikerade Azure-nätverk. Program kan sedan flyttas över avskilt och de krävs en lokal plats till anslutningar till datakällor kan dirigeras till målregionen enligt beskrivningen ovan.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [återställningsplaner](site-recovery-create-recovery-plans.md).
