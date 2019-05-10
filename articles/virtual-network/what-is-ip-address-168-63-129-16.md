---
title: Vad är IP-adressen 168.63.129.16? | Microsoft Docs
description: Läs mer om IP-adressen 168.63.129.16 och hur det fungerar med dina resurser.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: genli
ms.openlocfilehash: 78d2392e32465b3091c49032dc5df5f3a5b6061a
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416040"
---
# <a name="what-is-ip-address-1686312916"></a>Vad är IP-adressen 168.63.129.16?

IP-adressen 168.63.129.16 är virtuell offentliga IP-adress som används för att underlätta en kommunikationskanal för resurser i Azure-plattformen. Kunder kan definiera valfritt adressutrymme för sina privata virtuella nätverk i Azure. Azure-plattformen resurser måste vara angiven som en unik offentlig IP-adress. Den här virtuella offentliga IP-adressen underlättar följande saker:

- Gör det möjligt för VM-agenten kan kommunicera med Azure-plattformen för att signalera att den är i tillståndet ”klar”.
- Möjliggör kommunikation med den virtuella DNS-servern att tillhandahålla filtrerade namnmatchning för att de resurser (till exempel VM) som inte har en anpassad DNS-server. Den här filtreringen ser till att kunderna kan matcha endast värdnamnen för sina resurser.
- Aktiverar [hälsoavsökningar från Azure-belastningsutjämnare](../load-balancer/load-balancer-custom-probe-overview.md) att fastställa hälsotillståndet för virtuella datorer.
- Gör det möjligt för den virtuella datorn att hämta en dynamisk IP-adress från DHCP-tjänsten i Azure.
- Gör att Gästagenten heartbeat-meddelanden för PaaS-rollen.

## <a name="scope-of-ip-address-1686312916"></a>Omfattningen av IP-adressen 168.63.129.16

Den offentliga IP-adressen 168.63.129.16 används i alla regioner och alla nationella moln. Den här särskilda offentliga IP-adressen ägs av Microsoft och kommer inte att ändras. Det går som standard nätverkssäkerhetsgruppregel. Vi rekommenderar att du tillåter denna IP-adress i alla lokala Brandväggsprinciper. Kommunikationen mellan denna särskilda IP-adress och resurserna är säkra eftersom endast interna Azure-plattformen kan styra ett meddelande från den här IP-adressen. Om den här adressen är blockerad eller kan oväntade problem uppstå i en mängd olika scenarier.

[Azure Load Balancer hälsoavsökningar](../load-balancer/load-balancer-custom-probe-overview.md) samlas in från den här IP-adress. Om du blockerar den här IP-adressen kan inte din avsökningar.

Hälsoavsökningen kommer från en privat IP-adress och 168.63.129.16 används inte i ett scenario med icke-virtuellt nätverk.

## <a name="next-steps"></a>Nästa steg

- [Säkerhetsgrupper](security-overview.md)
- [Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](manage-network-security-group.md)
