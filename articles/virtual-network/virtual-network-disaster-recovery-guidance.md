---
title: "Vad du ska göra om ett avbrott i Azure-tjänsten påverkar virtuella Azure-nätverk | Microsoft Docs"
description: "Lär dig vad du ska göra om ett avbrott i Azure-tjänsten påverkar virtuella Azure-nätverk."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan;aglick
ms.openlocfilehash: 4e125406d2e798138c45e3fbbf61a610afab69fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network--business-continuity"></a>Virtuellt nätverk – kontinuitet för företag
## <a name="overview"></a>Översikt
Ett virtuellt nätverk (VNet) är en logisk representation av ditt nätverk i molnet. Det gör att du kan definiera dina egna privata IP-adressutrymme och segmentera nätverket i undernät. Vnet fungerar som en förtroendegräns som värd för dina beräkningsresurser som Azure virtuella datorer och molntjänster (web/worker-roller). Ett virtuellt nätverk kan direkt privata IP-kommunikation mellan resurser i den. Ett virtuellt nätverk kan också vara länkad till ett lokalt nätverk via ett av hybrid alternativ, till exempel en VPN-Gateway eller ExpressRoute.

Ett virtuellt nätverk skapas inom omfånget för en region. Du kan skapa Vnet med samma adressutrymme i två olika regioner (d.v.s. oss Öst och oss Väst men kan inte ansluta dem till varandra direkt). 

## <a name="business-continuity"></a>Verksamhetskontinuitet
Det kan finnas flera olika sätt att programmet kan avbrytas. En viss region kan vara helt bruten på grund av en naturkatastrof eller en partiell katastrof på grund av ett fel på flera enheter och tjänster. Påverkan på VNet-tjänsten är samma i alla dessa situationer.

**F: Vad gör vid ett avbrott för en hel region? dvs. Om en region är helt klara på grund av en naturkatastrof? Vad händer med virtuella nätverk finns i regionen?**

S: det virtuella nätverket och resurser i den berörda regionen förblir oåtkomlig under tid då avbrott i tjänsten.

![Enkel virtuella nätverksdiagram](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**F: Vad kan jag återskapa samma virtuella nätverk i en annan region?**

S: virtuella nätverk (VNet) är ganska enkel resurs. Du kan anropa Azure API: er för att skapa ett virtuellt nätverk med samma adressutrymmet i en annan region. Om du vill återskapa samma miljö som fanns i den berörda regionen, som du behöver göra API-anrop för att distribuera dina molntjänster (web/worker-roller) och virtuella datorer som du hade. Du måste även ha att få igång en VPN-Gateway och ansluta till det lokala nätverket om du har lokala anslutningen (till exempel i en hybriddistribution).

Anvisningar för att skapa ett virtuellt nätverk finns [här](virtual-networks-create-vnet-arm-pportal.md). 

**F: kan en replik av ett VNet i en viss region skapas på nytt i en annan region i förväg?**

S: Ja, kan du skapa två Vnet som använder samma privata IP-adressutrymme och resurser i två olika områden i förväg. Om en kund värd för tjänster i virtuella nätverk mot internet, kan de har ställt in Traffic Manager på geo dirigera trafik till den region som är aktiv. Men kan inte en kund ansluta två virtuella nätverk med samma adressutrymmet till deras lokala nätverk eftersom det skulle orsaka problem med routning. Tiden för en katastrofåterställning och förlust av ett VNet i en region, kan en kund ansluta andra VNet i tillgängliga region med matchande adressutrymme till deras lokala nätverk.

Anvisningar för att skapa ett virtuellt nätverk finns [här](virtual-networks-create-vnet-arm-pportal.md).

