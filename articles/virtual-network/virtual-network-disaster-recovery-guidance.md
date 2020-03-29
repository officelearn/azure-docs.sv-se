---
title: Kontinuitet i verksamheten i det virtuella nätverket | Microsoft-dokument
description: Lär dig vad du kan göra i händelse av ett avbrott i Azure-tjänsten som påverkar Azure Virtual Networks.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876175"
---
# <a name="virtual-network--business-continuity"></a>Virtuellt nätverk – Kontinuitet i verksamheten

## <a name="overview"></a>Översikt
Ett virtuellt nätverk (VNet) är en logisk representation av nätverket i molnet. Det låter dig definiera din egen privata IP-adressutrymme och segmentera nätverket i undernät. Virtuella nätverk fungerar som en förtroendegräns för att vara värd för dina beräkningsresurser som Azure Virtual Machines och Cloud Services (webb-/arbetsroller). Ett virtuella nätverk möjliggör direkt privat IP-kommunikation mellan de resurser som finns i det. Du kan länka ett virtuellt nätverk till ett lokalt nätverk via en VPN-gateway eller ExpressRoute.

Ett virtuella nätverk skapas inom en region. Du kan *skapa* virtuella nätverk med samma adressutrymme i två olika regioner (till exempel USA Öst och USA Väst), men eftersom de har samma adressutrymme kan du inte koppla ihop dem. 

## <a name="business-continuity"></a>Verksamhetskontinuitet

Det kan finnas flera olika sätt att ditt program kan störas. En region kan vara helt avskuren på grund av en naturkatastrof, eller en partiell katastrof, på grund av ett fel på flera enheter eller tjänster. Effekten på VNet-tjänsten är olika i var och en av dessa situationer.

**F: Om ett avbrott inträffar för en hel region, vad gör jag då? Till exempel, om en region är helt avskuren på grund av en naturkatastrof? Vad händer med de virtuella nätverk som finns i regionen?**

S: Det virtuella nätverket och resurserna i den drabbade regionen förblir otillgängliga under tiden för avbrott i tjänsten.

![Enkelt virtuellt nätverksdiagram](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**F: Vad kan jag göra för att återskapa samma virtuella nätverk i en annan region?**

S: Virtuella nätverk är ganska lätta resurser. Du kan anropa Azure API:er för att skapa ett virtuella nätverk med samma adressutrymme i en annan region. Om du vill återskapa samma miljö som fanns i den drabbade regionen gör du API-anrop för att distribuera om webb- och arbetsrollerna för Molntjänster och de virtuella datorer som du hade. Om du har lokal anslutning, till exempel i en hybriddistribution, måste du distribuera en ny VPN-gateway och ansluta till ditt lokala nätverk.

Om du vill skapa ett virtuellt nätverk finns i [Skapa ett virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network).

**F: Kan en replik av ett VNet i en viss region återskapas i en annan region i förväg?**

S: Ja, du kan skapa två virtuella nätverk med samma privata IP-adressutrymme och resurser i två olika regioner i förväg. Om du är värd för internetinriktade tjänster i det virtuella nätverket kan du ha ställt in Traffic Manager för geo-dirigera trafik till den region som är aktiv. Du kan dock inte ansluta två virtuella nätverk med samma adressutrymme till det lokala nätverket, eftersom det skulle orsaka routningsproblem. Vid tidpunkten för en katastrof och förlust av ett virtuella nätverk i en region kan du ansluta det andra virtuella nätverket i den tillgängliga regionen, med matchande adressutrymme till ditt lokala nätverk.

Om du vill skapa ett virtuellt nätverk finns i [Skapa ett virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network).

