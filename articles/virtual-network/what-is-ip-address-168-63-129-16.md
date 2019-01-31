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
ms.date: 01/21/2019
ms.author: genli
ms.openlocfilehash: 7f0539e7c2f7e5ae8847b35b47d3708c6c5d6a09
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296046"
---
# <a name="what-is-ip-address-1686312916"></a>Vad är IP-adressen 168.63.129.16?

IP-adressen 168.63.129.16 är virtuell offentliga IP-adress som används för att underlätta en kommunikationskanal för resurser i Azure-plattformen. Kunder kan definiera valfritt adressutrymme för sina privata virtuella nätverk i Azure. Azure-plattformen resurser måste vara angiven som en unik offentlig IP-adress. Den här virtuella offentliga IP-adressen underlättar följande saker:

- Gör det möjligt för VM-agenten kan kommunicera med Azure-plattformen för att signalera att den är i tillståndet ”klar”.
- Möjliggör kommunikation med den virtuella DNS-servern att tillhandahålla filtrerade namnmatchning för att de resurser (till exempel VM) som inte har en anpassad DNS-server. Den här filtreringen ser till att kunderna kan matcha endast värdnamnen för sina resurser.
- Aktiverar hälsokontroller av slutpunkter från belastningsutjämnaren för att fastställa hälsotillståndet för virtuella datorer i en belastningsutjämnad uppsättning.
- Gör att Gästagenten heartbeat-meddelanden för PaaS-rollen.

## <a name="scope-of-ip-address-1686312916"></a>Omfattningen av IP-adressen 168.63.129.16

Virtuella offentliga IP-adressen 168.63.129.16 används i alla regioner och alla nationella moln. Den här särskilda offentlig IP-adress ändras inte. Det går som standard nätverkssäkerhetsgruppregel. Vi rekommenderar att du tillåter denna IP-adress i alla lokala Brandväggsprinciper. Kommunikationen mellan denna särskilda IP-adress och resurserna är säkra eftersom endast interna Azure-plattformen kan styra ett meddelande från den här IP-adressen. Om den här adressen är blockerad eller kan oväntade problem uppstå i en mängd olika scenarier.

Dessutom kan du kan förvänta dig att trafik kan flöda från virtuella offentliga IP-adressen 168.63.129.16 till den slutpunkt som har konfigurerats för en [hälsoavsökning för belastningsutjämnaren](../load-balancer/load-balancer-custom-probe-overview.md). I en icke-virtuell scenariot kommer hälsoavsökningen från en privat IP-adress. 

## <a name="next-steps"></a>Nästa steg

- [Säkerhetsgrupper](security-overview.md)
- [Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](manage-network-security-group.md)
