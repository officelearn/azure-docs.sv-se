---
title: Vanliga frågor och svar – Azure Load Balancer
description: Svar på vanliga frågor om Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 4ae15d0898cedb0ed17dc308584769395aa819c2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85079474"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar

## <a name="what-types-of-load-balancer-exist"></a>Vilka typer av Load Balancer finns?
Interna belastningsutjämnare som balanserar trafik i ett VNET och externa belastningsutjämnare som balanserar trafik till och från en ansluten Internet-slutpunkt. Mer information finns i [Load Balancer typer](components.md#frontend-ip-configurations). 

För båda dessa typer erbjuder Azure en grundläggande SKU och standard-SKU som har olika funktioner för funktioner, prestanda, säkerhet och hälso tillstånd. Dessa skillnader beskrivs i vår artikel om [SKU-jämförelse](skus.md) .

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Hur kan jag uppgradera från en Basic till en Standard Load Balancer?
Se artikeln [Uppgradera från Basic till standard](upgrade-basic-standard.md) för ett automatiserat skript och vägledning om hur du uppgraderar en Load Balancer SKU.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Vilka är de olika alternativen för belastnings utjämning i Azure?
Se [teknik guiden för belastningsutjämnare](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) för de tillgängliga belastnings Utjämnings tjänsterna och rekommenderade användnings områden för var och en.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Var hittar jag Load Balancer ARM-mallar?
Se [listan över Azure Load Balancer snabb starts mallar](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates) för ARM-mallar för vanliga distributioner.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Hur skiljer sig inkommande NAT-regler från regler för belastnings utjämning?
NAT-regler används för att ange en server dels resurs som trafik ska skickas till. Du kan till exempel konfigurera en speciell belastnings Utjämnings port för att skicka RDP-trafik till en angiven virtuell dator. Regler för belastnings utjämning används för att ange en pool med Server dels resurser för att dirigera trafik till, vilket balanserar belastningen över varje instans. En belastnings Utjämnings regel kan till exempel dirigera TCP-paket på port 80 för belastningsutjämnaren i en pool av webb servrar.

## <a name="what-is-ip-1686312916"></a>Vad är IP-168.63.129.16?
Den virtuella IP-adressen för värden som taggats som Azure-infrastruktur Load Balancer där Azures hälso avsökningen kommer. När du konfigurerar Server dels instanser måste de tillåta trafik från den här IP-adressen att kunna svara på hälso avsökningar. Den här regeln samverkar inte med åtkomst till din Load Balancer-frontend. Om du inte använder Azure Load Balancer kan du åsidosätta den här regeln. Du kan lära dig mer om service märken [här](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags).

## <a name="can-i-use-global-vnet-peering-with-basic-load-blancer"></a>Kan jag använda global VNET-peering med Basic load Blancer?
Nej. Basic Load Balancer stöder inte global VNET-peering. Du kan använda en Standard Load Balancer i stället. Se artikeln [Uppgradera från Basic till standard](upgrade-basic-standard.md) för sömlös uppgradering.

## <a name="next-steps"></a>Efterföljande moment
Om din fråga inte visas ovan kan du skicka feedback om den här sidan med din fråga. Detta skapar ett GitHub-problem för produkt teamet för att se till att alla våra värdefulla kund frågor besvaras.
