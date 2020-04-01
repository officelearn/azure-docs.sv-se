---
title: Översikt över portar med hög tillgänglighet i Azure
titleSuffix: Azure Load Balancer
description: Lär dig mer om belastningsutjämning på hög tillgänglighetsportar på en intern belastningsutjämnare.
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
ms.openlocfilehash: d3bd1156de4aed7d1ea5c530605697f2dc80d63c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476978"
---
# <a name="high-availability-ports-overview"></a>Översikt över portar med hög tillgänglighet

Azure Standard Load Balancer hjälper dig att ladda-balansera TCP och UDP-flöden på alla portar samtidigt när du använder en intern belastningsutjämnare. 

En hög tillgänglighetsregel (HA) portar belastningsutjämningsregel är en variant av en belastningsutjämningsregel som konfigurerats på en intern standardbelastningsutjämningsregel. Du kan förenkla användningen av en belastningsutjämnare genom att tillhandahålla en enda regel för att belastningsutjämna alla TCP- och UDP-flöden som anländer till alla portar i en intern standardbelastningsutjämningsfaktor. Beslutet om belastningsutjämning fattas per flöde. Den här åtgärden baseras på följande fem-tuppelanslutning: käll-IP-adress, källport, mål-IP-adress, målport och protokoll

Ha-portarnas belastningsutjämningsregler hjälper dig med kritiska scenarier, till exempel hög tillgänglighet och skala för virtuella nätverksinstallationer (NVA) i virtuella nätverk. Funktionen kan också vara till hjälp när ett stort antal portar måste vara belastningsbalanserade. 

Belastningsutjämningsreglerna för HA-portar konfigureras när du ställer in front-end- och backend-portarna till **0** och protokollet till **alla**. Den interna belastningsutjämnarresursen balanserar sedan alla TCP- och UDP-flöden, oavsett portnummer

## <a name="why-use-ha-ports"></a>Varför använda HA-portar?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Virtuella nätverksinstallationer

Du kan använda NVA:er för att skydda din Azure-arbetsbelastning från flera typer av säkerhetshot. När du använder NVA:er i dessa scenarier måste de vara tillförlitliga och högtillgängliga, och de måste skalas ut efter behov.

Du kan uppnå dessa mål genom att helt enkelt lägga till NVA-instanser i backend-poolen för din interna belastningsutjämnare och konfigurera en HA-portar belastningsutjämnareregel.

För NVA HA-scenarier erbjuder HA-portar följande fördelar:
- Ge snabb redundans till felfria instanser, med hälsoavsökningar per instans
- Säkerställa högre prestanda med utskalning till *n*-aktiva instanser
- Tillhandahålla *n*-aktiva och aktiva och aktiva passiva scenarier
- Eliminera behovet av komplexa lösningar, till exempel Apache ZooKeeper-noder för övervakning av apparater

I följande diagram visas en virtuell nätverksdistribution av nav och ekrar. Ekrarna kraft-tunnel sin trafik till navet virtuella nätverket och genom NVA, innan du lämnar det betrodda utrymmet. NVA:erna ligger bakom en intern standardbelastningsutdelning med ha-portar. All trafik kan bearbetas och vidarebefordras därefter. När den konfigureras som visas i följande diagram ger en HA Ports belastningsutjämningsregel dessutom flödessymmetri för inkommande och utgående trafik.

<a node="diagram"></a>
![Diagram över virtuella nav- och ekrar, med NVA:er distribuerade i HA-läge](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Om du använder NVA: er, bekräfta med sina leverantörer hur man bäst använder HA-portar och ta reda på vilka scenarier som stöds.

### <a name="load-balancing-large-numbers-of-ports"></a>Belastningsutjämning stort antal portar

Du kan också använda HA-portar för program som kräver belastningsutjämning av ett stort antal portar. Du kan förenkla dessa scenarier genom att använda en intern [standardbelastningsutjämning](load-balancer-standard-overview.md) med HA-portar. En enda belastningsutjämningsregel ersätter flera individuella belastningsutjämningsregler, en för varje port.

## <a name="region-availability"></a>Regional tillgänglighet

HA-portar-funktionen är tillgänglig i alla globala Azure-regioner.

## <a name="supported-configurations"></a>Konfigurationer som stöds

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Ha-portar med en enda, icke-flytande IP-port (icke-Direkt serverretur) på en intern standardbelastningsbalanser

Den här konfigurationen är en grundläggande HA-portkonfiguration. Du kan konfigurera en HA-portars belastningsutjämningsregel på en enda front-end IP-adress genom att göra följande:
1. När du konfigurerar standardbelastningsutjämning markerar du kryssrutan **HA-portar** i regelkonfigurationen för belastningsutjämnare.
2. För **Flytande IP**väljer du **Inaktiverad**.

Den här konfigurationen tillåter inte någon annan belastningsutjämningsregelkonfiguration på den aktuella belastningsutjämnarens resurs. Det tillåter inte heller någon annan intern belastningsutjämnad resurskonfiguration för den angivna uppsättningen backend-instanser.

Du kan dock konfigurera en offentlig standardbelastningsutjämningsfaktor för backend-instanser utöver den här HA-portregeln.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Ha-portar med en enda flytande IP-port (Direct Server Return) på en intern standardbelastningsbalanserare

Du kan på samma sätt konfigurera belastningsutjämnaren så att den använder en belastningsutjämningsregel med **HA-port** med en enda frontdel genom att ställa in **den flytande** **IP:en till Aktiverad**. 

Genom att använda den här konfigurationen kan du lägga till fler flytande IP-belastningsutjämningsregler och/eller en offentlig belastningsutjämnare. Du kan dock inte använda en icke-flytande IP-konfiguration för belastningsutjämning för HA-portar ovanpå den här konfigurationen.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Flera HA-portar konfigurationer på en intern standardbelastningsbalanserare

Om ditt scenario kräver att du konfigurerar mer än en HA-port frontend för samma backend-pool kan du göra följande: 
- Konfigurera mer än en klientsluts privat IP-adress för en enda intern standardbelastningsutjämningsresurs.
- Konfigurera flera belastningsutjämningsregler, där varje regel har valt en unik ip-adress i frontend.
- Välj alternativet **HA-portar** och ange sedan **flytande IP** till Aktiverad för alla **belastningsutjämningsregler.**

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>En intern belastningsutjämnare med HA-portar och en offentlig belastningsutjämnare på samma backend-instans

Du kan konfigurera *en* offentlig standardbelastningsutjämningsresurs för backend-resurser, tillsammans med en enda intern standardbelastningsutdelningshanterare med HA-portar.

>[!NOTE]
>Den här funktionen är för närvarande tillgänglig via Azure Resource Manager-mallar, men den är inte tillgänglig via Azure-portalen.

## <a name="limitations"></a>Begränsningar

- HA-portars belastningsutjämningsregler är endast tillgängliga för interna standardbelastningsutjämningsmedel.
- Kombinationen av en HA-portar belastningsutjämningsregel och en belastningsutjämningsregel som inte är HA-portar som pekar på samma serverd ipkonfigurationer stöds inte.
- Befintliga IP-fragment vidarebefordras av HA Ports belastningsutjämningsregler till samma mål som det första paketet.  IP-fragmentering av ett UDP- eller TCP-paket stöds inte.
- Flödessymmetri (främst för NVA-scenarier) stöds med serverdainstans och en enda nätverkskort (och en enda IP-konfiguration) endast när den används som visas i diagrammet ovan och med hjälp av HA Ports belastningsutjämningsregler. Det finns inte i något annat scenario. Detta innebär att två eller flera belastningsutjämna resurser och deras respektive regler fattar självständiga beslut och aldrig samordnas. Se beskrivningen och diagrammet för [virtuella nätverksinstallationer](#nva). När du använder flera nätverkskort eller sandwiching NVA mellan en offentlig och intern load balancer, flöde symmetri är inte tillgänglig.  Du kanske kan komma runt detta genom att köpa NAT'ing ingressflödet till IP för enheten så att svar kan komma fram på samma NVA.  Vi rekommenderar dock starkt att du använder ett enda nätverkskort och använder referensarkitekturen som visas i diagrammet ovan.


## <a name="next-steps"></a>Nästa steg

- [Konfigurera HA-portar på en intern standardbelastningsutjämning](load-balancer-configure-ha-ports.md)
- [Läs mer om standardbelastningsjämningsapparaten](load-balancer-standard-overview.md)
