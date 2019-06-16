---
title: Affärskontinuitet för virtuellt nätverk | Microsoft Docs
description: Lär dig vad du gör i händelse av ett avbrott i Azure-tjänst påverkar Azure-nätverk.
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
ms.openlocfilehash: 68a9523dcc9c4dd84399c68fc7e31a692c011487
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523262"
---
# <a name="virtual-network--business-continuity"></a>Virtuellt nätverk – kontinuitet för företag

## <a name="overview"></a>Översikt
Ett virtuellt nätverk (VNet) är en logisk representation av ditt nätverk i molnet. Det kan du definiera egna privata IP-adressutrymme och segmentera nätverket i undernät. Virtuella nätverk fungerar som en förtroendegräns som värd för dina beräkningsresurser som Azure Virtual Machines och Cloud Services (web/worker-roller). Ett virtuellt nätverk kan direkt privata IP-kommunikation mellan de resurser som finns i den. Du kan länka ett virtuellt nätverk till ett lokalt nätverk via en VPN-Gateway eller ExpressRoute.

Ett virtuellt nätverk skapas inom omfånget för en region. Du kan *skapa* virtuella nätverk med samma adressutrymme i två olika regioner (till exempel USA, östra och USA, Väst), men eftersom de har samma-adressutrymme kan du inte kan ansluta dem tillsammans. 

## <a name="business-continuity"></a>Verksamhetskontinuitet

Det kan finnas flera olika sätt att ditt program kan avbrytas. En region kan helt klipps bort på grund av naturkatastrofer eller en partiell katastrof på grund av ett fel på flera enheter eller tjänster. Påverkan på VNet-tjänsten skiljer sig i var och en av dessa situationer.

**F: Vad gör jag om ett avbrott inträffar för en hel region? Om exempelvis en region är helt klipps bort på grund av en naturkatastrof? Vad händer med de virtuella nätverken finns i regionen?**

S: Det virtuella nätverket och resurser i den berörda regionen förblir oåtkomlig vid tidpunkten för avbrott i tjänsten.

![Diagram över enkla virtuellt nätverk](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**F: Vad kan jag göra återskapa samma virtuella nätverk i en annan region?**

S: Virtuella nätverk är ganska enkelt resurser. Du kan anropa API: erna för Azure för att skapa ett virtuellt nätverk med samma adressutrymmet i en annan region. Om du vill återskapa samma miljö som fanns i den berörda regionen, kan du göra API-anrop för att distribuera om Cloud Services web och worker-roller och de virtuella datorerna som du tidigare. Om du har en lokal anslutning behöver som i en hybriddistribution, du distribuera en ny VPN-Gateway och Anslut till ditt lokala nätverk.

För att skapa ett virtuellt nätverk, se [skapa ett virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network).

**F: Kan en replik av ett virtuellt nätverk i en viss region återskapas i en annan region förväg?**

S: Ja, kan du skapa två virtuella nätverk med samma privata IP-adressutrymme och resurser i två olika regioner förbereds i förväg. Om du är värd för webbservergrupper på internet-tjänster i det virtuella nätverket, kan du har lagt upp Traffic Manager att geo-dirigera trafik till den region som är aktiv. Men du kan inte ansluta två virtuella nätverk med samma-adressutrymme i dina lokala nätverk, eftersom det skulle orsaka routningsproblem. Vid tidpunkten för en katastrof och förlust av ett virtuellt nätverk i en region, kan du ansluta det andra virtuella nätverket i regionen tillgängliga med matchande adressutrymme till ditt lokala nätverk.

För att skapa ett virtuellt nätverk, se [skapa ett virtuellt nätverk](manage-virtual-network.md#create-a-virtual-network).

