---
title: Nätverkssäkerhetsgrupper med Azure-platsåterställning | Microsoft-dokument
description: Beskriver hur du använder nätverkssäkerhetsgrupper med Azure Site Recovery för haveriberedskap och migrering
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: eb5ba99133f5726c44164b0ba45b7ab5d94e292f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292365"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Nätverkssäkerhetsgrupper med Azure-webbplatsåterställning

Nätverkssäkerhetsgrupper används för att begränsa nätverkstrafiken till resurser i ett virtuellt nätverk. En [NSG (Network Security Group)](../virtual-network/security-overview.md#network-security-groups) innehåller en lista över säkerhetsregler som tillåter eller nekar inkommande eller utgående nätverkstrafik baserat på käll- eller mål-IP-adress, port och protokoll.

Under resurshanterarens distributionsmodell kan NSG:er associeras till undernät eller enskilda nätverksgränssnitt. När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet. Trafiken kan ytterligare begränsas genom att också associera en NSG till enskilda nätverksgränssnitt i ett undernät som redan har en associerad NSG.

I den här artikeln beskrivs hur du kan använda nätverkssäkerhetsgrupper med Azure Site Recovery.

## <a name="using-network-security-groups"></a>Använda nätverkssäkerhetsgrupper

Ett enskilt undernät kan ha noll, eller ett, associerat NSG. Ett enskilt nätverksgränssnitt kan också ha noll, eller ett, associerat NSG. Så kan du effektivt ha dubbla trafikbegränsningar för en virtuell dator genom att associera en NSG först till ett undernät och sedan en annan NSG till den virtuella datorns nätverksgränssnitt. Tillämpningen av NSG-regler i detta fall beror på trafikriktningen och prioriteringen av tillämpade säkerhetsregler.

Tänk dig ett enkelt exempel med en virtuell dator enligt följande:
-    Den virtuella datorn placeras i **Contoso-undernätet**.
-    **Contoso Subnet** är associerat med **Undernät NSG**.
-    Vm-nätverksgränssnittet är dessutom associerat med **VM NSG**.

![NSG med site recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

I det här exemplet utvärderas undernätet NSG först för inkommande trafik. All trafik som tillåts via Undernät NSG utvärderas sedan av VM NSG. Det omvända gäller för utgående trafik, med VM NSG utvärderas först. All trafik som tillåts via VM NSG utvärderas sedan av Subnet NSG.

Detta möjliggör detaljerade säkerhetsregelprogram. Du kanske till exempel vill tillåta inkommande internetåtkomst till några virtuella program -datorer (till exempel virtuella start-och tv-kort för klientföretag) under ett undernät men begränsa inkommande internetåtkomst till andra virtuella datorer (till exempel databas och andra virtuella datorer med bakåtsida). I det här fallet kan du ha en mildare regel på undernätet NSG, vilket möjliggör internettrafik och begränsar åtkomsten till specifika virtuella datorer genom att neka åtkomst på VM NSG. Detsamma kan tillämpas för utgående trafik.

När du ställer in sådana NSG-konfigurationer ska du se till att rätt prioritet tillämpas på [säkerhetsreglerna](../virtual-network/security-overview.md#security-rules). Regler bearbetas i prioritetsordning. Låga tal bearbetas före höga tal eftersom låga tal har högre prioritet. När trafiken matchar en regel avbryts bearbetningen. Det innebär att regler som har lägre prioritet (högre tal) och samma attribut som regler med högre prioritet inte bearbetas.

Du kanske inte alltid är medveten om när nätverkssäkerhetsgrupper tillämpas för både ett nätverksgränssnitt och ett undernät. Du kan verifiera de aggregerade regler som tillämpas på ett nätverksgränssnitt genom att visa [de gällande säkerhetsreglerna](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) för ett nätverksgränssnitt. Du kan också använda [funktionen IP-flödeskontroll](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) i [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) för att avgöra om kommunikation tillåts till eller från ett nätverksgränssnitt. Verktyget visar om kommunikation tillåts, och om nätverkssäkerhetsregeln tillåter eller nekar trafik.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Lokalt till Azure-replikering med NSG

Azure Site Recovery möjliggör haveriberedskap och migrering till Azure för lokala [virtuella Hyper-V-datorer,](hyper-v-azure-architecture.md) [virtuella VMware-datorer](vmware-azure-architecture.md)och [fysiska servrar](physical-azure-architecture.md). För alla lokala till Azure-scenarier skickas replikeringsdata till och lagras i ett Azure Storage-konto. Under replikering betalar du inga avgifter för virtuella datorer. När du kör en redundans till Azure skapar Site Recovery automatiskt virtuella Azure IaaS-datorer.

När virtuella datorer har skapats efter redundans till Azure kan NSG användas för att begränsa nätverkstrafiken till det virtuella nätverket och virtuella datorer. Site Recovery skapar inte NSG som en del av redundansåtgärden. Vi rekommenderar att du skapar de nödvändiga Azure NSG-grupperna innan du initierar redundans. Du kan sedan associera NSGs till misslyckades över virtuella datorer automatiskt under redundans, med hjälp av automatiseringsskript med Site Recovery kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md).

Om vm-konfigurationen efter redundans till exempel liknar [det exempelscenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) som beskrivs ovan:
-    Du kan skapa **Contoso VNet** och **Contoso Subnet** som en del av DR-planeringen för Azure-regionen för målet.
-    Du kan också skapa och konfigurera både **Undernät NSG** samt **VM NSG** som en del av samma DR-planering.
-    **Subnet NSG** kan sedan omedelbart associeras med **Contoso Subnet**, eftersom både NSG och undernätet redan är tillgängliga.
-    **VM NSG** kan associeras med virtuella datorer under redundans med hjälp av återställningsplaner.

När NSG:erna har skapats och konfigurerats rekommenderar vi att du kör en [testundanställning](site-recovery-test-failover-to-azure.md) för att verifiera skriptbaserade NSG-associationer och vm-anslutning efter redundans.

## <a name="azure-to-azure-replication-with-nsg"></a>Azure till Azure-replikering med NSG

Azure Site Recovery möjliggör haveriberedskap för [virtuella Azure-datorer](azure-to-azure-architecture.md). När du aktiverar replikering för virtuella Azure-datorer kan Site Recovery skapa virtuella repliknätverk (inklusive undernät och gateway-undernät) i målregionen och skapa nödvändiga mappningar mellan källan och rikta virtuella nätverk. Du kan också förskapa målsidans nätverk och undernät och använda samma när du aktiverar replikering. Site Recovery skapar inga virtuella datorer i Azure-regionen för målet före [redundans](azure-to-azure-tutorial-failover-failback.md).

För Azure VM-replikering, se till att NSG-reglerna för käll azure-regionen tillåter [utgående anslutning](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) för replikeringstrafik. Du kan också testa och verifiera dessa regler via det här [exemplet NSG-konfiguration](azure-to-azure-about-networking.md#example-nsg-configuration).

Site Recovery skapar eller replikerar inte NSG som en del av redundansåtgärden. Vi rekommenderar att du skapar nödvändiga NSG:er på Azure-regionen för målet innan du initierar redundans. Du kan sedan associera NSGs till misslyckades över virtuella datorer automatiskt under redundans, med hjälp av automatiseringsskript med Site Recovery kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md).

Med tanke på [det exempelscenario som](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) beskrivits tidigare:
-    Site Recovery kan skapa repliker av **Contoso VNet** och **Contoso Subnet** på azure-regionen för mål när replikering är aktiverat för den virtuella datorn.
-    Du kan skapa önskade repliker av **Undernät NSG** och **VM NSG** (namngivet, till exempel **Target Subnet NSG** och **Target VM NSG**, respektive) på azure-regionen för målet, vilket möjliggör eventuella ytterligare regler som krävs för målregionen.
-    **Målundernät NSG** kan sedan omedelbart associeras med målområdets undernät, eftersom både NSG och undernätet redan är tillgängliga.
-    **Mål-VM NSG** kan associeras med virtuella datorer under redundans med hjälp av återställningsplaner.

När NSG:erna har skapats och konfigurerats rekommenderar vi att du kör en [testundanställning](azure-to-azure-tutorial-dr-drill.md) för att verifiera skriptbaserade NSG-associationer och vm-anslutning efter redundans.

## <a name="next-steps"></a>Nästa steg
-    Läs mer om [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md#network-security-groups).
-    Läs mer om [NSG:s säkerhetsregler](../virtual-network/security-overview.md#security-rules).
-    Läs mer om [effektiva säkerhetsregler](../virtual-network/diagnose-network-traffic-filter-problem.md) för en NSG.
-    Läs mer om [återställningsplaner](site-recovery-create-recovery-plans.md) för att automatisera programundans.
