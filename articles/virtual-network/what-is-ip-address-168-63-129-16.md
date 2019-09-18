---
title: Vad är IP-168.63.129.16? | Microsoft Docs
description: Lär dig mer om IP-168.63.129.16 och hur det fungerar med dina resurser.
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
ms.openlocfilehash: 0ea8a8ec1a92a7dbc01dddc175f7116825ba00f9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067786"
---
# <a name="what-is-ip-address-1686312916"></a>Vad är IP-168.63.129.16?

IP-168.63.129.16 är en virtuell offentlig IP-adress som används för att under lätta en kommunikations kanal till Azures plattforms resurser. Kunder kan definiera valfritt adress utrymme för sitt privata virtuella nätverk i Azure. Därför måste Azures plattforms resurser presenteras som en unik offentlig IP-adress. Den här virtuella offentliga IP-adressen underlättar följande saker:

- Gör att VM-agenten kan kommunicera med Azure-plattformen för att signalera att den är i ett klart läge.
- Möjliggör kommunikation med den virtuella DNS-servern för att tillhandahålla filtrerad namn matchning till de resurser (t. ex. VM) som inte har en anpassad DNS-server. Den här filtreringen ser till att kunderna bara kan matcha sina resursers värdnamn.
- Aktiverar [hälso avsökningar från Azure Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) för att fastställa hälso tillståndet för virtuella datorer.
- Gör det möjligt för den virtuella datorn att hämta en dynamisk IP-adress från DHCP-tjänsten i Azure.
- Aktiverar pulsslags meddelanden för gäst agenten för PaaS-rollen.

## <a name="scope-of-ip-address-1686312916"></a>Omfånget för IP-168.63.129.16

Den offentliga IP-168.63.129.16 används i alla regioner och i alla nationella moln. Den här särskilda offentliga IP-adressen ägs av Microsoft och kommer inte att ändras. Den tillåts av standard regeln för nätverks säkerhets grupper. Vi rekommenderar att du tillåter den här IP-adressen i alla lokala brand Väggs principer i både inkommande och utgående riktning. Kommunikationen mellan den här särskilda IP-adressen och resurserna är säker eftersom endast den interna Azure-plattformen kan källa ett meddelande från den här IP-adressen. Om den här adressen är blockerad kan ett oväntat beteende uppstå i olika scenarier.

[Azure Load Balancer hälso avsökningar](../load-balancer/load-balancer-custom-probe-overview.md) härstammar från den här IP-adressen. Om du blockerar den här IP-adressen Miss söker avsökningarna.

I ett icke-virtuellt nätverks scenario (klassisk) kommer hälso avsökningen från en privat IP-adress och 168.63.129.16 inte användas.

## <a name="next-steps"></a>Nästa steg

- [Säkerhets grupper](security-overview.md)
- [Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](manage-network-security-group.md)
