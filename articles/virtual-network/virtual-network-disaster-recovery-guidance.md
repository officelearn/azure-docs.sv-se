---
title: Kontinuitet för företag för virtuellt nätverk | Microsoft Docs
description: Lär dig vad du ska göra om ett avbrott i Azure-tjänsten påverkar virtuella Azure-nätverk.
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
ms.author: narayan;aglick
ms.openlocfilehash: d993144006d1fb17d79ffee4f2da538264a309a4
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
ms.locfileid: "29854163"
---
# <a name="virtual-network--business-continuity"></a>Virtuellt nätverk – kontinuitet för företag

## <a name="overview"></a>Översikt
Ett virtuellt nätverk (VNet) är en logisk representation av ditt nätverk i molnet. Det gör att du kan definiera dina egna privata IP-adressutrymme och segmentera nätverket i undernät. Vnet fungerar som en förtroendegräns som värd för dina beräkningsresurser som Azure virtuella datorer och molntjänster (web/worker-roller). Ett virtuellt nätverk kan direkt privata IP-kommunikation mellan resurser i den. Du kan länka ett virtuellt nätverk till ett lokalt nätverk via en VPN-Gateway eller ExpressRoute.

Ett virtuellt nätverk skapas inom omfånget för en region. Du kan skapa Vnet med samma adressutrymme i två olika regioner (till exempel oss Öst och oss Väst), men det går inte att ansluta dem till varandra. 

## <a name="business-continuity"></a>Verksamhetskontinuitet

Det kan finnas flera olika sätt att programmet kan avbrytas. En region kan vara helt bruten på grund av en naturkatastrof eller partiell katastrofåterställning, på grund av ett fel i flera enheter eller tjänster. Påverkan på VNet-tjänsten är samma i alla dessa situationer.

**F: Vad gör jag om ett avbrott inträffar för en hel region? Om exempelvis en region klipps helt av på grund av en naturkatastrof? Vad händer med virtuella nätverk finns i regionen?**

S: det virtuella nätverket och resurser i den berörda regionen förblir oåtkomlig under tid då avbrott i tjänsten.

![Enkel virtuella nätverksdiagram](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**F: Vad kan jag återskapa samma virtuella nätverk i en annan region?**

S: virtuella nätverk är ganska enkel resurser. Du kan anropa Azure API: er för att skapa ett virtuellt nätverk med samma adressutrymmet i en annan region. Om du vill återskapa samma miljö som fanns i den berörda regionen du API-anrop för att distribuera om Cloud Services-webb- och arbetsroller- och de virtuella datorerna som du hade. Om du har en lokal anslutning, måste som i en hybriddistribution du distribuera en ny VPN-Gateway och ansluta till ditt lokala nätverk.

För att skapa ett virtuellt nätverk, se [skapa ett virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network).

**F: kan en replik av ett VNet i en viss region skapas på nytt i en annan region i förväg?**

S: Ja, kan du skapa två Vnet som använder samma privata IP-adressutrymme och resurser i två olika områden i förväg. Om du är värd för internet-riktade i virtuella nätverk, kan du har lagt upp Traffic Manager att geo dirigera trafik till den region som är aktiv. Men du kan inte ansluta två virtuella nätverk med samma adressutrymme till ditt lokala nätverk eftersom det skulle orsaka problem med routning. Vid tiden för en katastrofåterställning och förlust av ett VNet i en region, kan du ansluta andra VNet i tillgängliga region med matchande adressutrymmet till ditt lokala nätverk.

För att skapa ett virtuellt nätverk, se [skapa ett virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network).

