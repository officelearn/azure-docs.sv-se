---
title: Översikt över hög tillgänglighet portar i Azure | Microsoft Docs
description: Läs mer om hög tillgänglighet portar för belastningsutjämning på en intern belastningsutjämnare.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: kumud
ms.openlocfilehash: 53e09498324f802ce1839d262999657d37ee973b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32169126"
---
# <a name="high-availability-ports-overview"></a>Översikt över portar för hög tillgänglighet

Azure Standard belastningsutjämnare hjälper dig att belastningsutjämna TCP och UDP-flöden på alla portar samtidigt när du använder en intern belastningsutjämnare. 

En regel för hög tillgänglighet (HA)-portar är en variant av en belastningsutjämning regel som konfigurerats på en intern Standard belastningsutjämnare. Du kan förenkla användningen av en belastningsutjämnare genom att tillhandahålla en enkel regel för att belastningsutjämna alla TCP och UDP-flöden som inkommer till alla portar för en intern Standard belastningsutjämnare. Beslutet belastningsutjämning görs per flöde. Den här åtgärden är baserad på följande fem-tuppel-anslutning: datakällan IP-adress, källport, mål-IP-adress, målport och protokoll.

Funktionen för hög tillgänglighet portar hjälper dig med viktiga scenarier, till exempel hög tillgänglighet och skala för det virtuella nätverket-installationer (NVAs) i virtuella nätverk. Funktionen kan också när ett stort antal portar måste vara belastningsutjämnad. 

Hög tillgänglighet portar funktionen konfigureras när du ställer in frontend och backend-portar **0** och protokoll som **alla**. Den interna belastningsutjämningsresurs balanserar sedan alla TCP och UDP flöden, oavsett portnummer.

## <a name="why-use-ha-ports"></a>Varför använda portar för hög tillgänglighet?

### <a name="nva"></a>Virtuella nätverksenheter

Du kan använda NVAs för att skydda din arbetsbelastning i Azure från flera typer av säkerhetshot. När du använder NVAs i dessa scenarier kan de vara tillförlitliga och hög tillgänglighet och måste skala ut för begäran.

Du kan uppnå dessa mål bara genom att lägga till NVA instanser backend-pool med din interna belastningsutjämnare och konfigurera en hög tillgänglighet portar regel för belastningsutjämnare.

NVA HA scenarier med ger hög tillgänglighet portar följande fördelar:
- Ge snabb växling till Felfri instanser per instans hälsoavsökningar
- Se till att högre prestanda med skalbara till *n*-aktiva instanser
- Ange *n*-aktiva och aktivt-passivt scenarier
- Eliminera behovet för komplexa lösningar, till exempel Apache ZooKeeper-noder för att övervaka installationer

I följande diagram visas en distribution med nav och ekrar virtuellt nätverk. Ekrar force-tunneln sin trafik till det virtuella nätverket hubb och via en NVA innan de lämnar det betrodda utrymmet. NVAs finns bakom en intern Standard belastningsutjämnare med en konfiguration för hög tillgänglighet portar. All trafik kan bearbetas och därefter vidarebefordras.

![Diagram över NAV och eker-nätverk med NVAs som distribuerats i läget för hög tillgänglighet](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Om du använder NVAs, kontrollerar du hur du bäst använder portar för hög tillgänglighet och vill veta vilka scenarier som stöds med sina leverantörer.

### <a name="load-balancing-large-numbers-of-ports"></a>Belastningsutjämning stort antal portar

Du kan också använda portar för hög tillgänglighet för program som kräver belastningsutjämning av stora mängder portar. Du kan förenkla dessa scenarier genom att använda en intern [Standard belastningsutjämnaren](load-balancer-standard-overview.md) med hög tillgänglighet portar. En enda regel för belastningsutjämning ersätter flera enskilda belastningsutjämning regler, ett för varje port.

## <a name="region-availability"></a>Regional tillgänglighet

Hög tillgänglighet portar funktionen är tillgänglig i alla globala Azure regioner.

## <a name="supported-configurations"></a>Konfigurationer som stöds

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>En enskild, icke flytande IP (icke - direkt Serverreturnering) HA-portar konfiguration på en intern belastningsutjämnare som Standard

Den här konfigurationen är en grundläggande konfiguration för hög tillgänglighet portar. Du kan konfigurera en hög tillgänglighet portar belastningsutjämning-regel för en frontend IP-adress genom att göra följande:
1. När du konfigurerar Standard belastningsutjämnare, Välj den **HA portar** kryssrutan i regelkonfigurationen belastningsutjämnaren.
2. För **flytande IP**väljer **inaktiverade**.

Den här konfigurationen tillåter inte några andra belastningsutjämning regelkonfigurationen på den aktuella belastningsutjämningsresurs. Det gör också att inga andra interna resurskonfiguration av belastningsutjämning för den angivna uppsättningen av backend-instanser.

Du kan dock konfigurera offentliga Standard belastningsutjämning för backend-instanser förutom regeln portar för hög tillgänglighet.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>En enda flytande IP (direkt Serverreturnering) HA-portar konfiguration på en intern belastningsutjämnare som Standard

På liknande sätt kan du konfigurera din belastningsutjämnare för att använda en regel för belastningsutjämning med **HA Port** med en enda klientdel genom att ange den **flytande IP** till **aktiverad**. 

Med den här konfigurationen kan du lägga till fler flytande IP belastningsutjämning regler och/eller en offentlig belastningsutjämnare. Du kan inte använda en icke-flytande IP HA portar belastnings-utjämnande konfiguration på den här konfigurationen.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Flera konfigurationer för hög tillgänglighet-portar på en intern belastningsutjämnare som Standard

Om din situation kräver att du konfigurerar mer än en hög tillgänglighet port klientdelen för samma backend-pool, kan du göra följande: 
- Konfigurera flera frontend privat IP-adress för en enskild resurs för intern belastningsutjämnare som Standard.
- Konfigurera flera belastningsutjämning regler, där varje regel har en unik frontend IP-adress markerad.
- Välj den **HA portar** alternativet och ange sedan **flytande IP** till **aktiverad** för alla belastningsutjämning regler.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>En intern belastningsutjämnare med hög tillgänglighet portar och en offentlig belastningsutjämnare på samma backend-instans

Du kan konfigurera *en* offentliga Standard belastningsutjämnaren resurs för backend-resurser, tillsammans med en enkel intern Standard belastningsutjämnare med hög tillgänglighet portar.

>[!NOTE]
>Den här funktionen är tillgänglig via Azure Resource Manager-mallar, men den är inte tillgänglig via Azure portal.

## <a name="limitations"></a>Begränsningar

- Hög tillgänglighet är portkonfiguration bara tillgängligt för interna belastningsutjämnare. Det är inte tillgänglig för offentliga belastningsutjämnare.

- Kombinera med en hög tillgänglighet portarna belastningsutjämning regel och en icke-HA portar belastningsutjämning inte stöds.

- Hög tillgänglighet portar funktionen är inte tillgänglig för IPv6.

- Flödet symmetri NVA scenarier stöds med ett enda nätverkskort. Se beskrivningen och diagram för [nätverks-virtuella installationer](#nva). Men om ett mål NAT fungerar för ditt scenario kan kan du använda den för att kontrollera att den interna belastningsutjämnaren skickar returnerade trafiken till samma NVA.


## <a name="next-steps"></a>Nästa steg

- [Konfigurera portar för hög tillgänglighet på en intern belastningsutjämnare som Standard](load-balancer-configure-ha-ports.md)
- [Lär dig mer om Standard belastningsutjämnare](load-balancer-standard-overview.md)
