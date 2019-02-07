---
title: Översikt över portar hög tillgänglighet i Azure
titlesuffix: Azure Load Balancer
description: Läs mer om hög tillgänglighet portar belastningsutjämning på en intern belastningsutjämnare.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: kumud
ms.openlocfilehash: 328471292ea6cbe07e96cc18af7f9c524407de3d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809478"
---
# <a name="high-availability-ports-overview"></a>Översikt över portar med hög tillgänglighet

Azure Standard Load Balancer hjälper dig att belastningsutjämna TCP och UDP flöden på alla portar samtidigt när du använder en intern belastningsutjämnare. 

En hög tillgänglighet (HA) portar regel för belastningsutjämning är en variant av en regel som konfigurerats på en intern Standard Load Balancer för belastningsutjämning. Du kan förenkla användningen av en belastningsutjämnare genom att tillhandahålla en enkel regel för att belastningsutjämna alla TCP och UDP flöden som tas emot på alla portar för en intern Standard Load Balancer. Beslutet belastningsutjämning görs per flöde. Den här åtgärden är baserad på följande fem-tuppel-anslutning: käll-IP-adress, källport, målets IP-adress, målport och protokoll

Belastningsutjämningsregler de HA portar hjälper dig med viktiga scenarier, till exempel hög tillgänglighet och skalbarhet för virtuella nätverksinstallationer (Nva) i virtuella nätverk. Funktionen kan också när ett stort antal portar måste vara Utjämning av nätverksbelastning. 

Hög tillgänglighet portar belastningsutjämningsreglerna konfigureras när du ställer in de frontend och backend-portarna på **0** och protokoll som **alla**. Interna belastningsutjämningsresursen balanserar sedan alla flöden i TCP och UDP, oavsett portnummer

## <a name="why-use-ha-ports"></a>Varför använda högtillgänglighetsportar?

### <a name="nva"></a>Virtuella nätverksinstallationer

Du kan använda nva: er för att skydda din arbetsbelastning i Azure från flera typer av säkerhetshot. När du använder nva: er i dessa scenarier kan de vara tillförlitlig och med hög tillgänglighet och de skalar du ut för begäran.

Du kan åstadkomma detta genom att lägga till NVA-instanser till backend-poolen för din interna belastningsutjämnare och konfigurera en hög tillgänglighet portarna belastningsutjämningsregel.

NVA HA scenarier erbjuder högtillgänglighetsportar följande fördelar:
- Ge snabb redundans till felfria instanser per instans hälsoavsökningar
- Se till att bättre prestanda med skalning till *n*-aktiva instanser
- Ange *n*-aktiva och aktivt-passivt scenarier
- I stället för komplicerade lösningar, till exempel Apache ZooKeeper-noder för att övervaka installationer

I följande diagram visas ett nav och eker virtual network-distributionen. Den ekrar tvinga tunneltrafik sin trafik till det virtuella navnätverket och via NVA, innan de lämnar det betrodda utrymmet. Nva: erna är bakom en intern Standard Load Balancer med en konfiguration för hög tillgänglighet portar. All trafik kan bearbetas och vidarebefordrats i enlighet med detta. Konfigurerades som visas i följande diagram ger en HA Ports belastningsutjämningsregel dessutom flow symmetri för ingående och utgående trafik.

<a node="diagram"></a>
![Diagram över NAV och eker virtuellt nätverk, med Nva som distribuerats i läge för hög tillgänglighet](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Om du använder nva: er, kontrollerar du hur du bäst använder högtillgänglighetsportar och lära dig vilka scenarier som stöds med sina leverantörer.

### <a name="load-balancing-large-numbers-of-ports"></a>Belastningsutjämning stort antal portar

Du kan också använda högtillgänglighetsportar för program som kräver belastningsutjämning av stora mängder portar. Du kan förenkla dessa scenarier genom att använda en intern [Standardbelastningsutjämnare](load-balancer-standard-overview.md) med hög tillgänglighet portar. En enda regel för belastningsutjämning ersätter flera enskilda belastningsutjämningsregler, en för varje port.

## <a name="region-availability"></a>Regional tillgänglighet

Hög tillgänglighet portar funktionen är tillgänglig i alla globala Azure-regioner.

## <a name="supported-configurations"></a>Konfigurationer som stöds

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>En enskild, icke-flytande IP (icke - direkt Serverreturnering) högtillgänglighetsportar-konfiguration på en intern Standard Load Balancer

Den här konfigurationen är en grundläggande konfiguration för hög tillgänglighet-portar. Du kan konfigurera en hög tillgänglighet portar belastningsutjämning-regel för en frontend IP-adress genom att göra följande:
1. När du konfigurerar Standard Load Balancer, Välj den **högtillgänglighetsportar** kryssrutan i regelkonfigurationen för belastningsutjämnaren.
2. För **flytande IP**väljer **inaktiverad**.

Den här konfigurationen tillåter inte några andra belastningsutjämning regelkonfigurationen på aktuella belastningsutjämningsresursen. Det gör också att inga andra interna resursen belastningsutjämningskonfigurationen för den angivna uppsättningen serverdels-instanser.

Du kan dock konfigurera en offentlig Standard Load Balancer för serverdelsinstanserna förutom regeln portar för hög tillgänglighet.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>En enda flytande IP (direkt Serverreturnering) högtillgänglighetsportar konfiguration på en intern Standard Load Balancer

På samma sätt kan du konfigurera belastningsutjämnaren om du vill använda en regel för belastningsutjämning med **HA Port** med en enda klientdel genom att ange den **flytande IP** till **aktiverad**. 

Du kan lägga till mer flytande IP-regler för belastningsutjämning och/eller en offentlig belastningsutjämnare med hjälp av den här konfigurationen. Men du kan inte använda en icke-flytande IP högtillgänglighetsportar belastnings-utjämnande konfiguration på den här konfigurationen.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Konfigurationer med flera HA-portar på en intern Standard Load Balancer

Om din situation kräver att du konfigurerar mer än en hög tillgänglighet port klientdelen för samma backend-pool, kan du göra följande: 
- Konfigurera flera frontend privat IP-adress för en enskild resurs för interna Standard Load Balancer.
- Konfigurera flera regler-belastningsutjämning, där varje regel har en unik frontend IP-adress har valt.
- Välj den **högtillgänglighetsportar** alternativet och ange sedan **flytande IP** till **aktiverad** för alla belastningsutjämning regler.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>En intern belastningsutjämnare med högtillgänglighetsportar och en offentlig belastningsutjämnare på samma backend-instans

Du kan konfigurera *en* offentlig Standard Load Balancer-resurs för backend-resurser, tillsammans med en enda intern Standard Load Balancer med hög tillgänglighet portar.

>[!NOTE]
>Den här funktionen är för närvarande tillgänglig via Azure Resource Manager-mallar, men det är inte tillgängligt via Azure portal.

## <a name="limitations"></a>Begränsningar

- Hög tillgänglighet är portkonfiguration endast tillgänglig för interna belastningsutjämnare. Det är inte tillgänglig för offentliga belastningsutjämnare.

- Kombinera med en hög tillgänglighet portarna regel för belastningsutjämning och en icke-HA-portar som regel för belastningsutjämning inte stöds.

- Hög tillgänglighet portar funktionen är inte tillgänglig för IPv6.

- Flow symmetri (främst för scenarier för NVA) stöds med backend-instans och en enda nätverkskort (och enda IP-konfiguration) endast när det används som visas i diagrammet ovan och använda HA Ports belastningsutjämningsregler. Den finns inte i något annat. Det innebär att två eller flera belastningsutjämnare resurser och deras respektive regler oberoende fatta och samordnas aldrig. Se beskrivningen och diagram för [virtuella nätverksinstallationer](#nva). När du använder flera nätverkskort eller sandwiching NVA mellan en offentliga och interna belastningsutjämnare, är flow symmetri inte tillgänglig.  Du kanske kan lösa detta genom källa NAT'ing ingående flöda till den IP-Adressen för en enhet så att svar tas emot på samma NVA.  Vi rekommenderar dock starkt använder ett enda nätverkskort och referensarkitekturen visas i diagrammet ovan.


## <a name="next-steps"></a>Nästa steg

- [Konfigurera portar med hög tillgänglighet på en intern Standard Load Balancer](load-balancer-configure-ha-ports.md)
- [Lär dig mer om Standard Load Balancer](load-balancer-standard-overview.md)
