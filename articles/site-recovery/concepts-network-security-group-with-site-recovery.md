---
title: Nätverkssäkerhetsgrupper med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du använder säkerhetsgrupper för nätverk med Azure Site Recovery för migrering och katastrofåterställning
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/21/2018
ms.author: manayar
ms.openlocfilehash: 2b35578e2dc49cd47689f62fc47c5341ea8767a4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655757"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Nätverkssäkerhetsgrupper med Azure Site Recovery

Nätverkssäkerhetsgrupper används för att begränsa nätverkstrafik till resurser i ett virtuellt nätverk. En [Nätverkssäkerhetsgrupp (NSG)](../virtual-network/security-overview.md#network-security-groups) innehåller en lista över säkerhetsregler som tillåter eller nekar inkommande eller utgående nätverkstrafik baserat på källan eller mål-IP-adress, port och protokoll.

NSG: er kan vara kopplad till undernät eller individuella nätverksgränssnitt under Resource Manager-distributionsmodellen. När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet. Trafik kan ytterligare begränsas av också koppla en NSG till enskilda nätverksgränssnitt i ett undernät som redan har en associerad NSG.

Den här artikeln beskriver hur du kan använda Nätverkssäkerhetsgrupper med Azure Site Recovery.

## <a name="using-network-security-groups"></a>Använda Nätverkssäkerhetsgrupper

Ett enskilt undernät kan ha noll eller ett, associerade NSG. En enskild nätverksgränssnitt kan också ha noll eller ett, associerade NSG. Så kan du effektivt har dubbla trafik begränsning för en virtuell dator genom att associera en NSG till ett undernät först och sedan en annan NSG till den Virtuella datorns nätverksgränssnitt. NSG-regler beror i det här fallet på trafikriktning och prioritet för reglerna för säkerhet har tillämpats.

Överväg ett enkelt exempel med en virtuell dator på följande sätt:
-   Den virtuella datorn placeras inuti den **Contoso Subnet**.
-   **Contoso Subnet** är associerad med **undernät NSG**.
-   Virtuella datorns nätverksgränssnitt är även kopplad till **VM NSG**.

![NSG med Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

I det här exemplet för inkommande trafik utvärderas NSG för undernätet först. All trafik som tillåts genom undernät NSG utvärderas sedan av VM NSG. Motsatsen är tillämpliga för utgående trafik med VM NSG utvärderas först. All trafik som tillåts genom VM NSG utvärderas sedan av NSG för undernätet.

Detta ger detaljerade säkerhetsprogram för regeln. Du kanske vill tillåta inkommande internet-åtkomst till några program virtuella datorer (till exempel klientdel VMs) under ett undernät, men begränsa inkommande internet-åtkomst till andra virtuella datorer (till exempel databasen och andra backend virtuella datorer). I det här fallet kan du ha en mer flexibla regel på undernätet NSG: N, så att internet-trafik och begränsa åtkomsten till specifika virtuella datorer genom att neka åtkomst på VM NSG. Samma kan användas för utgående trafik.

När du ställer in NSG konfigurationer, kontrollera att rätt prioriteter tillämpas den [säkerhetsregler](../virtual-network/security-overview.md#security-rules). Regler bearbetas i prioritetsordning. Låga tal bearbetas före höga tal eftersom låga tal har högre prioritet. När trafiken matchar en regel avbryts bearbetningen. Det innebär att regler som har lägre prioritet (högre tal) och samma attribut som regler med högre prioritet inte bearbetas.

Du kanske inte alltid är medveten om när nätverkssäkerhetsgrupper tillämpas för både ett nätverksgränssnitt och ett undernät. Du kan verifiera sammanställd reglerna som gäller för ett nätverksgränssnitt genom att visa den [effektiva säkerhetsregler](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) för ett nätverksgränssnitt. Du kan också använda den [IP-flöde Kontrollera](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) funktionen [Azure Nätverksbevakaren](../network-watcher/network-watcher-monitoring-overview.md) att avgöra om kommunikation tillåts till eller från ett nätverksgränssnitt. Verktyget visar om kommunikation tillåts, och om nätverkssäkerhetsregeln tillåter eller nekar trafik.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Lokal Azure replikering med NSG

Azure Site Recovery kan katastrofåterställning och migreringen till Azure för lokalt [Hyper-V-datorer](hyper-v-azure-architecture.md), [virtuella VMware-datorer](vmware-azure-architecture.md), och [fysiska servrar](physical-azure-architecture.md). För alla lokala scenarier, Azure, replikeringsdata skickas till och lagras i ett Azure Storage-konto. Vid replikering betala inte du alla avgifter för virtuell dator. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt virtuella Azure IaaS-datorer.

När du har skapat virtuella datorer efter redundans till Azure kan NSG: er användas till att begränsa nätverkstrafiken till virtuella nätverk och virtuella datorer. Site Recovery kan inte skapa NSG: er som en del av redundansväxlingen. Vi rekommenderar att du skapar nödvändiga Azure NGSs innan du påbörjar redundans. Du kan sedan koppla NSG: er till redundansväxlade virtuella datorer automatiskt under växling vid fel, med automatiserade skript med Site Recovery kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md).

Om exempelvis postredundans VM-konfiguration liknar den [Exempelscenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) detaljerad ovan:
-   Du kan skapa **Contoso VNet** och **Contoso Subnet** som en del av DR planera på målet Azure-region.
-   Du kan också skapa och konfigurera både **undernät NSG** samt **VM NSG** som en del av samma DR planering.
-   **Undernät NSG** kan sedan omedelbart associeras med **Contoso Subnet**, där både NSG: N och undernätet finns redan.
-   **VM NSG** kan vara kopplad till virtuella datorer under redundans med återställningsplaner.

När de NSG: er har skapats och konfigurerats, rekommenderar vi körs en [redundanstestningen](site-recovery-test-failover-to-azure.md) att verifiera skripta NSG kopplingarna och postredundans VM-anslutningar.

## <a name="azure-to-azure-replication-with-nsg"></a>Replikering i Azure till Azure med NSG

Azure Site Recovery gör det möjligt för katastrofåterställning av [virtuella Azure-datorer](azure-to-azure-architecture.md). När du aktiverar replikering för virtuella datorer i Azure kan Site Recovery skapa repliken virtuella nätverk (inklusive undernät och gateway-undernät) på mål-region och skapa nödvändiga mappningar mellan källa och mål virtuella nätverk. Du kan också skapa mål på serversidan nätverk och undernät och använder samma vid replikering. Site Recovery inte skapa virtuella datorer på de mål Azure-region före [redundans](azure-to-azure-tutorial-failover-failback.md).

Se till att tillåta NSG-regler på käll-Azure-region för Azure VM-replikering [utgående anslutning](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) för replikeringstrafik. Du kan också testa och verifiera reglerna krävs via [NSG exempelkonfiguration](azure-to-azure-about-networking.md#example-nsg-configuration).

Site Recovery inte skapar eller replikera NSG: er som en del av redundansväxlingen. Vi rekommenderar att du skapar nödvändiga NGSs på målet Azure-region innan du påbörjar redundans. Du kan sedan koppla NSG: er till redundansväxlade virtuella datorer automatiskt under växling vid fel, med automatiserade skript med Site Recovery kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md).

Med tanke på den [Exempelscenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) ovan:
-   Site Recovery kan skapa repliker av **Contoso VNet** och **Contoso Subnet** på målet Azure-region när replikering har aktiverats för den virtuella datorn.
-   Du kan skapa önskad replikerna för **undernät NSG** och **VM NSG** (med namnet, till exempel **mål undernät NSG** och **mål VM NSG**, respektive) på målvärden Azure-regionen för eventuella ytterligare regler som krävs på mål-region.
-   **Rikta undernät NSG** kan sedan omedelbart som är kopplade till målet region undernät, där både NSG: N och undernätet finns redan.
-   **Rikta VM NSG** kan vara kopplad till virtuella datorer under redundans med återställningsplaner.

När de NSG: er har skapats och konfigurerats, rekommenderar vi körs en [redundanstestningen](azure-to-azure-tutorial-dr-drill.md) att verifiera skripta NSG kopplingarna och postredundans VM-anslutningar.

## <a name="next-steps"></a>Nästa steg
-   Lär dig mer om [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md#network-security-groups).
-   Mer information om NSG [säkerhetsregler](../virtual-network/security-overview.md#security-rules).
-   Lär dig mer om [effektiva säkerhetsregler](../virtual-network/diagnose-network-traffic-filter-problem.md) för en NSG.
-   Lär dig mer om [återställningsplaner](site-recovery-create-recovery-plans.md) vill automatisera redundans för programmet.
