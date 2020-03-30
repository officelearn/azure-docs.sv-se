---
title: Vad är IP-adress 168.63.129.16? | Microsoft Docs
description: Läs mer om IP-adress 168.63.129.16 och hur den fungerar med dina resurser.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77114353"
---
# <a name="what-is-ip-address-1686312916"></a>Vad är IP-adress 168.63.129.16?

IP-adress 168.63.129.16 är en virtuell offentlig IP-adress som används för att underlätta en kommunikationskanal till Azure-plattformsresurser. Kunder kan definiera valfritt adressutrymme för sitt privata virtuella nätverk i Azure. Därför måste Azure-plattformsresurserna presenteras som en unik offentlig IP-adress. Den här virtuella offentliga IP-adressen underlättar följande:

- Gör det möjligt för VM-agenten att kommunicera med Azure-plattformen för att signalera att den är i tillståndet "Klar".
- Gör det möjligt för kommunikation med den virtuella DNS-servern att tillhandahålla filtrerad namnmatchning till de resurser (till exempel virtuell dator) som inte har en anpassad DNS-server. Den här filtrningen säkerställer att kunderna bara kan lösa värdnamnen för sina resurser.
- Aktiverar [hälsoavsökningar från Azure-belastningsutjämnar](../load-balancer/load-balancer-custom-probe-overview.md) för att fastställa hälsotillståndet för virtuella datorer.
- Aktiverar den virtuella datorn för att hämta en dynamisk IP-adress från DHCP-tjänsten i Azure.
- Aktiverar Pulsslagsmeddelanden för Gästagenten för PaaS-rollen.

## <a name="scope-of-ip-address-1686312916"></a>IP-adressens omfattning 168.63.129.16

Den offentliga IP-adressen 168.63.129.16 används i alla regioner och alla nationella moln. Den här särskilda offentliga IP-adressen ägs av Microsoft och ändras inte. Vi rekommenderar att du tillåter den här IP-adressen i alla lokala (i den virtuella datorn) brandväggsprinciper (utgående riktning). Kommunikationen mellan den här speciella IP-adressen och resurserna är säker eftersom endast den interna Azure-plattformen kan köpa ett meddelande från den här IP-adressen. Om den här adressen är blockerad kan oväntade beteenden uppstå i en mängd olika scenarier. 168.63.129.16 är en virtuell IP för [värdnoden](../virtual-network/security-overview.md#azure-platform-considerations) och som sådan är den inte föremål för användardefinierade vägar.

- VM-agenten kräver utgående kommunikation över portarna 80, 443, 32526 med WireServer (168.63.129.16). Dessa bör vara öppna i den lokala brandväggen på den virtuella datorn. Kommunikationen på dessa portar med 168.63.129.16 omfattas inte av de konfigurerade nätverkssäkerhetsgrupperna.
- 168.63.129.16 kan tillhandahålla DNS-tjänster till den virtuella datorn. Om detta inte önskas kan den här trafiken blockeras i den lokala brandväggen på den virtuella datorn. Som standard omfattas DNS-kommunikation inte av de konfigurerade nätverkssäkerhetsgrupperna om inte specifikt riktad utnyttja [AzurePlatformDNS-tjänsttaggen.](../virtual-network/service-tags-overview.md#available-service-tags)
- När den virtuella datorn är en del av en serverdelspool för belastningsutjämnare bör [hälsoavsökningskommunikation](../load-balancer/load-balancer-custom-probe-overview.md) tillåtas att komma från 168.63.129.16. Standardkonfigurationen för nätverkssäkerhetsgrupp har en regel som tillåter den här kommunikationen. Den här regeln utnyttjar [AzureLoadBalancer-tjänsttaggen.](../virtual-network/service-tags-overview.md#available-service-tags) Om så önskas kan den här trafiken blockeras genom att konfigurera nätverkssäkerhetsgruppen, men detta kommer att resultera i avsökningar som misslyckas.

I ett icke-virtuellt nätverksscenario (Classic) kommer hälsoavsökningen från en privat IP och 168.63.129.16 används inte.

## <a name="next-steps"></a>Nästa steg

- [Säkerhetsgrupper](security-overview.md)
- [Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](manage-network-security-group.md)
