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
ms.openlocfilehash: f6e9dd09558a3485629d5b70dd8b68b292427b18
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="high-availability-ports-overview"></a>Översikt över portar för hög tillgänglighet

Azure Standard belastningsutjämnare kan du läsa belastningsutjämna TCP och UDP-flöden på alla portar samtidigt, när du använder en intern belastningsutjämnare. 

En regel för hög tillgänglighet portar är en variant av en regel som konfigurerats på en intern Standard belastningsutjämnare för belastningsutjämning. Du kan förenkla din användning av belastningsutjämnare genom att tillhandahålla en enkel regel för att belastningsutjämna alla TCP och UDP-flöden som inkommer på alla portar för en intern Standard belastningsutjämnare. Belastningsutjämning beslut görs per flöde. Detta baseras på följande fem-tuppel-anslutning: datakällan IP-adress, källport, mål-IP-adress, målport och protokoll.

Funktionen för hög tillgänglighet portar hjälper dig med viktiga scenarier, till exempel hög tillgänglighet och skala för det virtuella nätverket-installationer (NVA) i virtuella nätverk. Det kan också när ett stort antal portar måste vara belastningsutjämnad. 

Hög tillgänglighet portar funktionen konfigureras när du ställer in frontend och backend-portar **0**, och protokoll som **alla**. Den interna belastningsutjämnaren resursen balanserar sedan alla TCP och UDP flöden, oavsett portnummer.

## <a name="why-use-ha-ports"></a>Varför använda portar för hög tillgänglighet?

### <a name="nva"></a>Virtuella nätverksenheter

Du kan använda NVAs för att skydda din arbetsbelastning i Azure från flera typer av säkerhetshot. När NVAs används i dessa scenarier kan de vara tillförlitliga och hög tillgänglighet och måste skala ut för begäran.

Du kan åstadkomma detta genom att lägga till NVA instanser till backend-poolen för Azure interna belastningsutjämnare och konfigurera en regel för hög tillgänglighet portar belastningsutjämnaren.

Hög tillgänglighet finns portar många fördelar för NVA HA scenarier:
- Snabb växling till Felfri instanser med per instans hälsoavsökningar
- Högre prestanda med skalbara till *n*-aktiva instanser
- *N*-aktiva och aktivt-passivt scenarier
- Eliminera behovet av komplexa lösningar som Apache ZooKeeper-noder för att övervaka installationer

I följande diagram visas en distribution med nav och ekrar virtuellt nätverk. Ekrar force-tunneln sin trafik till det virtuella nätverket hubb och via en NVA innan de lämnar det betrodda utrymmet. NVAs finns bakom en intern Standard belastningsutjämnare med en konfiguration för hög tillgänglighet portar. All trafik kan bearbetas och därefter vidarebefordras.

![Diagram över NAV och eker-nätverk med NVAs som distribuerats i läget för hög tillgänglighet](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Om du använder NVAs bekräfta med respektive providern hur du bäst använder portar för hög tillgänglighet och vilka scenarier som stöds.

### <a name="load-balancing-large-numbers-of-ports"></a>Belastningsutjämning stort antal portar

Du kan också använda portar för hög tillgänglighet för program som kräver belastningsutjämning av stora mängder portar. Du kan förenkla dessa scenarier genom att använda en intern [Standard belastningsutjämnaren](load-balancer-standard-overview.md) med hög tillgänglighet portar. En enda regel för belastningsutjämning ersätter flera enskilda belastningsutjämningsregler, ett för varje port.

## <a name="region-availability"></a>Regional tillgänglighet

Hög tillgänglighet portar funktionen är tillgänglig i alla globala Azure regioner.

## <a name="supported-configurations"></a>Konfigurationer som stöds

### <a name="one-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>En enskild icke flytande IP (icke - direkt Serverreturnering) HA Portkonfiguration på interna Standard belastningsutjämnaren

Detta är en grundläggande konfiguration för hög tillgänglighet portar. Följande konfiguration kan du konfigurera hög tillgänglighet portar för belastningsutjämning på en enda klientdel IP-adress-
- När du konfigurerar din Standard belastningsutjämnare, Välj **HA portar** kryssrutan i regelkonfigurationen belastningsutjämnare 
- Kontrollera att **flytande IP** är inställd på **inaktiverade**.

Den här konfigurationen tillåter inte några andra belastningsutjämning regelkonfigurationen på den aktuella resursen belastningsutjämnare och inga andra interna resurskonfiguration av belastningsutjämning för den angivna uppsättningen av backend-instanser.

Du kan dock konfigurera offentliga Standard belastningsutjämning för backend-instanser förutom den här HA regeln.

### <a name="one-single-floating-ip-direct-server-return-ha-ports-configuration-on-the-internal-standard-load-balancer"></a>En enda flytande IP (direkt Serverreturnering) HA Portkonfiguration på interna Standard belastningsutjämnaren

På liknande sätt kan du konfigurera din belastningsutjämnare för att använda en belastningsutjämningsregeln med **HA Port** med en enda klientdel och **flytande IP** inställd på **aktiverad**. 

Den här konfigurationen kan du lägga till fler flytande IP för belastningsutjämning regler och / eller en offentlig belastningsutjämnare. Du kan dock använda en icke - flytande IP HA boad belastningsutjämning portkonfiguration ovanpå den här konfigurationen.

### <a name="multiple-ha-ports-configurations-on-the-internal-standard-load-balancer"></a>Flera konfigurationer för hög tillgänglighet portar på den interna belastningsutjämnare som Standard

Om din situation kräver att du konfigurerar mer än en hög tillgänglighet port frontends för samma serverdelspoolen, kan du åstadkomma detta genom att: 
- konfigurera mer än en klientdel privata IP-adresser för en enskild resurs för intern belastningsutjämnare som Standard.
- konfigurera flera regler, där varje regel har en enda för belastningsutjämning unika klientdelens IP-adress är markerat.
- Välj **HA portar** alternativet och ange **flytande IP** till **aktiverad** för alla regler för belastningsutjämning.

### <a name="internal-load-balancer-with-ha-ports--public-load-balancer-on-the-same-backend-instances"></a>Interna belastningsutjämnare med hög tillgänglighet portar och offentliga belastningsutjämnare på samma backend-instanser

Du kan konfigurera **en** offentliga Standard belastningsutjämnaren resurs för serverdelsresurser tillsammans med en enkel intern Standard belastningsutjämnare med hög tillgänglighet portar.

>[!NOTE]
>Den här funktionen är tillgänglig via Azure Resource Manager-mallar idag, men inte via Azure portal.

## <a name="limitations"></a>Begränsningar

- Hög tillgänglighet portkonfiguration är endast tillgängligt för intern belastningsutjämnare, den är inte tillgänglig för en offentlig belastningsutjämnare.

- En kombination av hög tillgänglighet portar belastningsutjämning och icke - hög tillgänglighet portar belastningsutjämning regel stöds inte.

- Hög tillgänglighet portar-funktionen är inte tillgänglig för IPv6.

- Flödet symmetri NVA scenarier stöds med ett enda nätverkskort. Se beskrivningen och diagram för [nätverks-virtuella installationer](#nva). Om en mål-NAT kan fungera för ditt scenario kan du dock använda som för att kontrollera att den interna belastningsutjämnaren skickar returnerade trafiken till samma NVA.


## <a name="next-steps"></a>Nästa steg

- [Konfigurera hög tillgänglighet portar på en intern belastningsutjämnare som Standard](load-balancer-configure-ha-ports.md)
- [Lär dig mer om Standard belastningsutjämnare](load-balancer-standard-overview.md)
