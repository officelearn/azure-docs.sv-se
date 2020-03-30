---
title: Vad är nätdelegering i virtuella Azure-nätverk?
description: Lär dig mer om undernätsdelegering i virtuella Azure-nätverk
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2019
ms.author: kumud
ms.openlocfilehash: b33ff808b802b6848e2d5debaf515a73bf21a1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74281342"
---
# <a name="what-is-subnet-delegation"></a>Vad är subnätdelegering?

Med nätdelegering kan du ange ett specifikt undernät för en Azure PaaS-tjänst som du väljer som måste injiceras i det virtuella nätverket. Undernätsdelegering ger fullständig kontroll till kunden när det gäller att hantera integreringen av Azure-tjänster i sina virtuella nätverk.

När du delegerar ett undernät till en Azure-tjänst tillåter du att tjänsten upprättar några grundläggande regler för nätverkskonfiguration för det undernätet, vilket hjälper Azure-tjänsten att driva sina instanser på ett stabilt sätt. Därför kan Azure-tjänsten fastställa vissa villkor före eller efter distribution, till exempel:
- distribuera tjänsten i ett delat kontra dedikerat undernät.
- lägga till en uppsättning principer för nätverksavsikter efter distribution som krävs för att tjänsten ska fungera korrekt.

##  <a name="advantages-of-subnet-delegation"></a>Fördelar med subnätdelegering

Att delegera ett undernät till specifika tjänster ger följande fördelar:

- hjälper till att ange ett undernät för en eller flera Azure-tjänster och hantera instanserna i undernätet enligt kraven. Ägaren till det virtuella nätverket kan till exempel definiera följande för ett delegerat undernät för att bättre hantera resurser och åtkomst enligt följande:
    - trafikprinciper för nätverksfiltrering med nätverkssäkerhetsgrupper.
    - routningsprinciper med användardefinierade vägar.
    - integrering av tjänster med tjänstslutpunkter.
- hjälper till att injicera tjänster för att bättre integrera med det virtuella nätverket genom att definiera deras förutsättningar för distributioner i form av principer för nätverksavsikter. Detta säkerställer att alla åtgärder som kan påverka den injicerade tjänstens funktion kan blockeras vid PUT.


## <a name="who-can-delegate"></a>Vem kan delegera?
Undernätsdelegering är en övning som de virtuella nätverksägarna måste utföra för att ange ett av undernäten för en viss Azure-tjänst. Azure Service distribuerar i sin tur instanserna till det här undernätet för förbrukning av kundarbetsbelastningar.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Inverkan av undernätsdelegering i undernätet
Varje Azure-tjänst definierar sin egen distributionsmodell, där de kan definiera vilka egenskaper de gör eller inte stöder i ett delegerat undernät för injektionsändamål, till exempel följande:
- delat undernät med andra Azure Services- eller VM/virtuell datorskala som angetts i samma undernät, eller stöder bara ett dedikerat undernät med endast instanser av den här tjänsten i det.
- stöder NSG-association med det delegerade undernätet.
- stöder NSG som är associerat med det delegerade undernätet kan också associeras med något annat undernät.
- tillåter flödestabellasasociation med det delegerade undernätet.
- gör att flödestabellen som är associerad med det delegerade undernätet kan associeras med något annat undernät.
- anger det minsta antalet IP-adresser i det delegerade undernätet.
- dikterar IP-adressutrymmet i det delegerade undernätet så att det kommer från privat IP-adressutrymme (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- dikterar att den anpassade DNS-konfigurationen har en Azure DNS-post.

Injicerade tjänster kan också lägga till sina egna principer enligt följande:
- **Säkerhetsprinciper**: Insamling av säkerhetsregler som krävs för att en viss tjänst ska fungera.
- **Flödesprinciper**: Insamling av rutter som krävs för att en viss tjänst ska fungera.

## <a name="what-subnet-delegation-does-not-do"></a>Vad undernätsdelegering inte gör

De Azure-tjänster som injiceras i ett delegerat undernät har fortfarande den grundläggande uppsättningen egenskaper som är tillgängliga för icke-delegerade undernät, till exempel:
-  Azure-tjänster kan injicera instanser i kundens undernät, men kan inte påverka de befintliga arbetsbelastningarna.
-  De principer eller vägar som dessa tjänster gäller är flexibla och kan åsidosättas av kunden.

## <a name="next-steps"></a>Nästa steg

- [Delegera ett undernät](manage-subnet-delegation.md)
