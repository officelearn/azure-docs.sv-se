---
title: Nätverkssäkerhetsgrupper med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du använder Nätverkssäkerhetsgrupper med Azure Site Recovery för katastrofåterställning och migrering
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0c06283080a4ee51f863714e4c515672299b420d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799294"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Nätverkssäkerhetsgrupper med Azure Site Recovery

Nätverkssäkerhetsgrupper används för att begränsa nätverkstrafiken till resurser i ett virtuellt nätverk. En [Nätverkssäkerhetsgrupp (NSG)](../virtual-network/security-overview.md#network-security-groups) innehåller en lista över säkerhetsregler som tillåter eller nekar inkommande eller utgående nätverkstrafik baserat på källan eller målets IP-adress, port och protokoll.

NSG: er kan vara associerade med undernät eller separata nätverksgränssnitt under Resource Manager-distributionsmodellen. När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet. Trafiken kan begränsas ytterligare genom att även associera en NSG till enskilda nätverkskort i ett undernät som redan har en associerad NSG.

Den här artikeln beskrivs hur du kan använda Nätverkssäkerhetsgrupper med Azure Site Recovery.

## <a name="using-network-security-groups"></a>Använda Nätverkssäkerhetsgrupper

Ett individuellt undernät kan ha noll eller en, associerad Nätverkssäkerhetsgrupp. En enskild nätverksgränssnitt kan också ha ingen eller en, associerad Nätverkssäkerhetsgrupp. Därför kan du effektivt ha dubbla trafik begränsning för en virtuell dator genom att associera en NSG först till ett undernät och sedan en annan NSG till den Virtuella datorns nätverksgränssnitt. Tillämpningen av NSG-regler beror i det här fallet på trafikriktning och prioriteten för tillämpade säkerhetsregler.

Överväg ett enkelt exempel med en virtuell dator på följande sätt:
-   Den virtuella datorn placeras inuti den **Contoso undernät**.
-   **Contoso undernät** är associerad med **NSG för undernätet**.
-   Virtuella datorns nätverksgränssnitt associeras dessutom med **VM NSG**.

![NSG med Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

I det här exemplet för inkommande trafik utvärderas i Nätverkssäkerhetsgruppen för undernätet först. All trafik som tillåts via NSG för undernätet utvärderas sedan av VM NSG. Motsatsen är tillämpliga för utgående trafik med VM-NSG utvärderas först. All trafik som tillåts via NSG VM utvärderas sedan av NSG för undernätet.

Detta ger kontroll över säkerheten regeln program. Du kanske vill tillåta inkommande internet-åtkomst till några program virtuella datorer (till exempel klientdelens virtuella datorer) under ett undernät men begränsa inkommande internet-åtkomst till andra virtuella datorer (till exempel databasen och andra serverdelens virtuella datorer). I det här fallet kan du ha en mer flexibel regel på undernätet NSG: N, vilket gör att internet-trafik och begränsa åtkomsten till specifika virtuella datorer genom att neka åtkomst på VM NSG. Samma kan användas för utgående trafik.

När du konfigurerar sådana NSG-konfigurationer, kontrollera att rätt prioriteringarna tillämpas den [säkerhetsregler](../virtual-network/security-overview.md#security-rules). Regler bearbetas i prioritetsordning. Låga tal bearbetas före höga tal eftersom låga tal har högre prioritet. När trafiken matchar en regel avbryts bearbetningen. Det innebär att regler som har lägre prioritet (högre tal) och samma attribut som regler med högre prioritet inte bearbetas.

Du kanske inte alltid är medveten om när nätverkssäkerhetsgrupper tillämpas för både ett nätverksgränssnitt och ett undernät. Du kan kontrollera vilka regler som tillämpas till ett nätverksgränssnitt genom att visa den [gällande säkerhetsregler](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) för ett nätverksgränssnitt. Du kan också använda den [IP-flödesverifieringen](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) funktion [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) att avgöra om kommunikation tillåts till eller från ett nätverksgränssnitt. Verktyget visar om kommunikation tillåts, och om nätverkssäkerhetsregeln tillåter eller nekar trafik.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Lokalt till Azure-replikering med NSG

Azure Site Recovery kan haveriberedskap och migrering till Azure för lokala [Hyper-V-datorer](hyper-v-azure-architecture.md), [virtuella VMware-datorer](vmware-azure-architecture.md), och [fysiska servrar](physical-azure-architecture.md). För alla lokala till Azure-scenarier, replikeringsdata skickas till och lagras i ett Azure Storage-konto. Du behöver inte betala några avgifter för virtuella datorer under replikeringen. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt Azure IaaS-datorer.

NSG: er kan användas för att begränsa trafik till det virtuella nätverket och virtuella datorer när virtuella datorer har skapats efter redundansväxlingen till Azure. Site Recovery kan inte skapa NSG: er som en del av redundansväxlingen. Vi rekommenderar att du skapar NSG: er för nödvändiga Azure innan du påbörjar redundans. Därefter kan du associera NSG: er till redundansväxlade virtuella datorer automatiskt under redundansväxlingen, med automatiserade skript med Site Recovery kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md).

Exempel: om postredundant VM-konfigurationen liknar den [Exempelscenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) beskrivs ovan:
-   Du kan skapa **Contoso VNet** och **Contoso undernät** som en del av DR planera på mål-Azure-region.
-   Du kan också skapa och konfigurera både **NSG för undernätet** samt **VM NSG** som en del samma DR planera.
-   **En Nätverkssäkerhetsgrupp för undernät** kan sedan omedelbart associeras med **Contoso undernät**, där både NSG: N och undernätet finns redan.
-   **VM NSG** kan associeras med virtuella datorer under en redundansväxling med hjälp av återställningsplaner.

När NSG: erna har skapats och konfigurerats, rekommenderar vi körs en [redundanstest](site-recovery-test-failover-to-azure.md) att verifiera skriptade NSG associationer och postredundant VM-anslutning.

## <a name="azure-to-azure-replication-with-nsg"></a>Replikering från Azure till Azure med NSG

Azure Site Recovery kan haveriberedskap för [Azure-datorer](azure-to-azure-architecture.md). När du aktiverar replikering för virtuella datorer i Azure kan Site Recovery skapa repliken virtuella nätverk (inklusive undernät och gateway-undernät) på målregion och skapa nödvändiga mappningar mellan källan och rikta in virtuella nätverk. Du kan också skapa mål på serversidan nätverk och undernät och använder samma vid aktivering av replikering. Site Recovery skapar inte alla virtuella datorer på de target Azure-region före [redundans](azure-to-azure-tutorial-failover-failback.md).

Se till att NSG-reglerna på den Azure källregion tillåter för Azure VM replikering [utgående anslutning](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) för replikeringstrafik. Du kan också testa och verifiera reglerna krävs via [NSG exempelkonfiguration](azure-to-azure-about-networking.md#example-nsg-configuration).

Site Recovery inte skapar eller replikera NSG: er som en del av redundansväxlingen. Vi rekommenderar att du skapar nödvändiga NSG: er på den Azure-målregion innan du påbörjar redundans. Därefter kan du associera NSG: er till redundansväxlade virtuella datorer automatiskt under redundansväxlingen, med automatiserade skript med Site Recovery kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md).

Funderar på att den [Exempelscenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) som beskrivs ovan:
-   Site Recovery kan skapa repliker av **Contoso VNet** och **Contoso undernät** på mål-Azure-region när replikering har aktiverats för den virtuella datorn.
-   Du kan skapa de önskade replikerna av **NSG för undernätet** och **VM NSG** (med namnet, till exempel **Target undernät NSG** och **mål VM NSG**, respektive) på det aktiva Azure-region, så att alla ytterligare regler som krävs på målregion.
-   **Rikta NSG för undernätet** kan sedan vara direkt kopplad målundernätet region där både NSG: N och undernätet finns redan.
-   **Rikta VM NSG** kan associeras med virtuella datorer under en redundansväxling med hjälp av återställningsplaner.

När NSG: erna har skapats och konfigurerats, rekommenderar vi körs en [redundanstest](azure-to-azure-tutorial-dr-drill.md) att verifiera skriptade NSG associationer och postredundant VM-anslutning.

## <a name="next-steps"></a>Nästa steg
-   Läs mer om [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md#network-security-groups).
-   Mer information om NSG [säkerhetsregler](../virtual-network/security-overview.md#security-rules).
-   Läs mer om [gällande säkerhetsregler](../virtual-network/diagnose-network-traffic-filter-problem.md) för en NSG.
-   Läs mer om [återställningsplaner](site-recovery-create-recovery-plans.md) vill automatisera redundans i programmet.
