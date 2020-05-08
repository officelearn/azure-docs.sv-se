---
title: Vanliga frågor och svar – Azure Load Balancer
description: Svar på vanliga frågor om Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3648bb102d539156067cdc259c1cf9b6e73d6a49
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884493"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

## <a name="what-types-of-load-balancer-exist"></a>Vilka typer av Load Balancer finns?
Interna belastningsutjämnare som balanserar trafik i ett VNET och externa belastningsutjämnare som balanserar trafik till och från en ansluten Internet-slutpunkt. Mer information finns i [Load Balancer typer] (https://docs.microsoft.com/azure/load-balancer/concepts-limitations#load-balancer-types). 

För båda dessa typer erbjuder Azure en grundläggande SKU och standard-SKU som har olika funktioner för funktioner, prestanda, säkerhet och hälso tillstånd. Dessa skillnader beskrivs i vår [SKU-jämförelse] (https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus) artikel.

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Hur kan jag uppgradera från en Basic till en Standard Load Balancer?
Se artikeln [Uppgradera från Basic till standard](upgrade-basic-standard.md) för ett automatiserat skript och vägledning om hur du uppgraderar en Load Balancer SKU.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Vilka är de olika alternativen för belastnings utjämning i Azure?
Se [teknik guiden för belastningsutjämnare](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) för de tillgängliga belastnings Utjämnings tjänsterna och rekommenderade användnings områden för var och en.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Var hittar jag Load Balancer ARM-mallar?
Se [listan över Azure Load Balancer snabb starts mallar](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) för ARM-mallar för vanliga distributioner.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Hur skiljer sig inkommande NAT-regler från regler för belastnings utjämning?
NAT-regler används för att ange en server dels resurs som trafik ska skickas till. Du kan till exempel konfigurera en speciell belastnings Utjämnings port för att skicka RDP-trafik till en angiven virtuell dator. Regler för belastnings utjämning används för att ange en pool med Server dels resurser för att dirigera trafik till, vilket balanserar belastningen över varje instans. En belastnings Utjämnings regel kan till exempel dirigera TCP-paket på port 80 för belastningsutjämnaren i en pool av webb servrar.

## <a name="next-steps"></a>Nästa steg
Om din fråga inte visas ovan kan du skicka feedback om den här sidan med din fråga. Detta skapar ett GitHub-problem för produkt teamet för att se till att alla våra värdefulla kund frågor besvaras.
