---
title: Översikt över portar hög tillgänglighet i Azure
titleSuffix: Azure Load Balancer
description: Läs mer om hög tillgänglighet portar belastningsutjämning på en intern belastningsutjämnare.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: c6529e2585a7fca2d160d093d303afa02e6f9379
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215081"
---
# <a name="high-availability-ports-overview"></a>Översikt över portar med hög tillgänglighet

Azure Standard Load Balancer hjälper dig att belastningsutjämna TCP och UDP flöden på alla portar samtidigt när du använder en intern belastningsutjämnare. 

En belastnings Utjämnings regel för belastnings utjämning med hög tillgänglighet (HA) är en variant av en belastnings Utjämnings regel som kon figurer ATS på ett internt Standard Load Balancer. Du kan förenkla användningen av en belastningsutjämnare genom att tillhandahålla en enkel regel för att belastningsutjämna alla TCP och UDP flöden som tas emot på alla portar för en intern Standard Load Balancer. Beslutet belastningsutjämning görs per flöde. Den här åtgärden baseras på följande fem-tuple-anslutning: Källans IP-adress, källport, mål-IP-adress, målport och protokoll

Belastnings Utjämnings reglerna för HA-portar hjälper dig med kritiska scenarier, till exempel hög tillgänglighet och skalning för virtuella nätverks installationer (NVA) i virtuella nätverk. Funktionen kan också när ett stort antal portar måste vara Utjämning av nätverksbelastning. 

Belastnings Utjämnings reglerna för HA-portar konfigureras när du ställer in frontend-och backend-portarna på **0** och protokollet till **alla**. Den interna belastnings Utjämnings resursen balanserar sedan alla TCP-och UDP-flöden, oavsett port nummer

## <a name="why-use-ha-ports"></a>Varför använda högtillgänglighetsportar?

### <a name="nva"></a>Virtuella nätverks enheter

Du kan använda nva: er för att skydda din arbetsbelastning i Azure från flera typer av säkerhetshot. När du använder nva: er i dessa scenarier kan de vara tillförlitlig och med hög tillgänglighet och de skalar du ut för begäran.

Du kan åstadkomma detta genom att lägga till NVA-instanser till backend-poolen för din interna belastningsutjämnare och konfigurera en hög tillgänglighet portarna belastningsutjämningsregel.

NVA HA scenarier erbjuder högtillgänglighetsportar följande fördelar:
- Ge snabb redundans till felfria instanser per instans hälsoavsökningar
- Se till att högre prestanda med skalbar till *n*-aktiva instanser
- Ange *n*-aktiva och aktiva-passiva scenarier
- I stället för komplicerade lösningar, till exempel Apache ZooKeeper-noder för att övervaka installationer

I följande diagram visas ett nav och eker virtual network-distributionen. Den ekrar tvinga tunneltrafik sin trafik till det virtuella navnätverket och via NVA, innan de lämnar det betrodda utrymmet. Nva: erna är bakom en intern Standard Load Balancer med en konfiguration för hög tillgänglighet portar. All trafik kan bearbetas och vidarebefordrats i enlighet med detta. När en belastnings Utjämnings regel för belastnings utjämning har kon figurer ATS som visas i följande diagram tillhandahåller belastnings Utjämnings regeln för belastnings utjämning också flödes symmetri för ingress

<a node="diagram"></a>
![diagram över virtuella nav-och eker-nätverk, med NVA distribuerat i HA-läge](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Om du använder nva: er, kontrollerar du hur du bäst använder högtillgänglighetsportar och lära dig vilka scenarier som stöds med sina leverantörer.

### <a name="load-balancing-large-numbers-of-ports"></a>Belastningsutjämning stort antal portar

Du kan också använda högtillgänglighetsportar för program som kräver belastningsutjämning av stora mängder portar. Du kan förenkla dessa scenarier genom att använda en intern [standard Load Balancer](load-balancer-standard-overview.md) med ha-portar. En enda regel för belastningsutjämning ersätter flera enskilda belastningsutjämningsregler, en för varje port.

## <a name="region-availability"></a>Regional tillgänglighet

Hög tillgänglighet portar funktionen är tillgänglig i alla globala Azure-regioner.

## <a name="supported-configurations"></a>Konfigurationer som stöds

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>En enskild, icke-flytande IP (icke - direkt Serverreturnering) högtillgänglighetsportar-konfiguration på en intern Standard Load Balancer

Den här konfigurationen är en grundläggande konfiguration för hög tillgänglighet-portar. Du kan konfigurera en hög tillgänglighet portar belastningsutjämning-regel för en frontend IP-adress genom att göra följande:
1. När du konfigurerar Standard Load Balancer markerar du kryss rutan **ha portar** i Load Balancer regel konfigurationen.
2. För **flytande IP-adress**väljer du **inaktive rad**.

Den här konfigurationen tillåter inte några andra belastningsutjämning regelkonfigurationen på aktuella belastningsutjämningsresursen. Det gör också att inga andra interna resursen belastningsutjämningskonfigurationen för den angivna uppsättningen serverdels-instanser.

Du kan dock konfigurera en offentlig Standard Load Balancer för serverdelsinstanserna förutom regeln portar för hög tillgänglighet.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>En enda flytande IP (direkt Serverreturnering) högtillgänglighetsportar konfiguration på en intern Standard Load Balancer

Du kan konfigurera belastningsutjämnaren på samma sätt genom att använda en belastnings Utjämnings regel med **ha-port** med en enda klient del genom att ställa in den **flytande IP-adressen** på **aktive rad**. 

Du kan lägga till mer flytande IP-regler för belastningsutjämning och/eller en offentlig belastningsutjämnare med hjälp av den här konfigurationen. Men du kan inte använda en icke-flytande IP högtillgänglighetsportar belastnings-utjämnande konfiguration på den här konfigurationen.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Konfigurationer med flera HA-portar på en intern Standard Load Balancer

Om din situation kräver att du konfigurerar mer än en hög tillgänglighet port klientdelen för samma backend-pool, kan du göra följande: 
- Konfigurera flera frontend privat IP-adress för en enskild resurs för interna Standard Load Balancer.
- Konfigurera flera regler-belastningsutjämning, där varje regel har en unik frontend IP-adress har valt.
- Välj alternativet **ha portar** och ställ sedan in **flytande IP** till **aktive rad** för alla regler för belastnings utjämning.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>En intern belastningsutjämnare med högtillgänglighetsportar och en offentlig belastningsutjämnare på samma backend-instans

Du kan konfigurera *en* offentlig standard Load Balancer resurs för backend-resurserna, tillsammans med en enda intern standard load BALANCER med ha-portar.

>[!NOTE]
>Den här funktionen är för närvarande tillgänglig via Azure Resource Manager-mallar, men det är inte tillgängligt via Azure portal.

## <a name="limitations"></a>Begränsningar

- Belastnings Utjämnings regler för HA-portar är bara tillgängliga för interna Standard Load Balancer.
- Kombinera med en hög tillgänglighet portarna regel för belastningsutjämning och en icke-HA-portar som regel för belastningsutjämning inte stöds.
- Befintliga IP-fragment vidarebefordras av HA port belastnings Utjämnings regler till samma mål som det första paketet.  IP-fragmentering av UDP-eller TCP-paket stöds inte.
- Belastnings Utjämnings reglerna för HA-portar är inte tillgängliga för IPv6.
- Flödes symmetrin (främst för NVA-scenarier) stöds med backend-instansen och ett enda nätverkskort (och enskild IP-konfiguration) endast när det används som det visas i diagrammet ovan och med belastnings Utjämnings regler för HA-portar. Den finns inte i något annat scenario. Det innebär att två eller flera Load Balancer resurser och deras respektive regler gör oberoende beslut och inte samordnade. Se beskrivningen och diagrammet för [virtuella nätverks enheter](#nva). När du använder flera nätverkskort eller mellan NVA mellan en offentlig och intern Load Balancer, är flödets symmetri inte tillgängligt.  Du kanske kan kringgå detta genom att källan NAT'ing ingångs flödet till IP-enheten för att tillåta svar på samma NVA.  Vi rekommenderar dock starkt att du använder ett enda nätverkskort och använder referens arkitekturen som visas i diagrammet ovan.


## <a name="next-steps"></a>Nästa steg

- [Konfigurera HA-portar på ett internt Standard Load Balancer](load-balancer-configure-ha-ports.md)
- [Läs mer om Standard Load Balancer](load-balancer-standard-overview.md)
