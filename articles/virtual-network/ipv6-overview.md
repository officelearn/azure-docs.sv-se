---
title: Översikt över IPv6 för Virtuellt Azure-nätverk (förhandsversion)
titlesuffix: Azure Virtual Network
description: IPv6-beskrivning av IPv6-slutpunkter och datasökvägar i ett virtuellt Azure-nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/19/2019
ms.author: kumud
ms.openlocfilehash: 9214886f468a4a052328a99289845361a059b650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780087"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Vad är IPv6 för Azure Virtual Network? (Förhandsversion)

Med IPv6 för Azure Virtual Network (VNet) kan du vara värd för program i Azure med IPv6- och IPv4-anslutning både i ett virtuellt nätverk och till och från Internet. På grund av utmattningen av offentliga IPv4-adresser bygger nya nätverk för rörlighet och Sakernas Internet (IoT) ofta på IPv6. Även sedan länge etablerade INTERNET-och mobilnät håller på att omvandlas till IPv6. IPv4-tjänster kan hamna i en verklig nackdel på både befintliga och framväxande marknader. IPv4/IPv6-anslutning med dubbla staplar gör det möjligt för Azure-värdbaserade tjänster att gå igenom den här teknikklyftan med globalt tillgängliga, dual-stacked-tjänster som enkelt ansluter till både befintliga IPv4 och dessa nya IPv6-enheter och -nätverk.

Azures ursprungliga IPv6-anslutning gör det enkelt att tillhandahålla internetanslutning med dubbla stackar (IPv4/IPv6) för program som finns i Azure. Det möjliggör enkel distribution av virtuella datorer med belastningsbalanserad IPv6-anslutning för både inkommande och utgående initierade anslutningar. Den här funktionen är fortfarande tillgänglig och mer information finns [här](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 för virtuella Azure-nätverk är mycket mer komplett- vilket gör att fullständiga IPv6-lösningsarkitekturer kan distribueras i Azure.

> [!Important]
> IPv6 för Azure Virtual Network är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I följande diagram visas en enkel IPv4/IPv6-distribution (IPv4/IPv6) i Azure:

![IPv6-nätverksdistributionsdiagram](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Fördelar

IPv6 för Azure VNET-fördelar:

- Hjälper dig att utöka räckvidden för dina Azure-värdbaserade program till de växande marknaderna för mobil och Sakernas Internet.
- Dubbla staplade IPv4/IPv6 virtuella datorer ger maximal flexibilitet för distribution av tjänst. En enda tjänstinstans kan ansluta till både IPv4- och IPv6-kompatibla Internetklienter.
- Bygger på sedan länge etablerad, stabil Azure VM-till-Internet IPv6-anslutning.
- Säker som standard eftersom IPv6-anslutning till Internet endast upprättas när du uttryckligen begär den i distributionen.

## <a name="capabilities"></a>Funktioner

IPv6 för Azure VNet innehåller följande funktioner:

- Azure-kunder kan definiera sitt eget IPv6 virtuella nätverksadressutrymme för att uppfylla behoven hos deras program, kunder eller sömlöst integreras i sitt lokala IP-utrymme.
- Virtuella nätverk med dubbla stacker (IPv4 och IPv6) med undernät med dubbla staplar gör det möjligt för program att ansluta till både IPv4- och IPv6-resurser i sitt virtuella nätverk eller - Internet.
    > [!IMPORTANT]
    > Undernäten för IPv6 måste vara exakt /64 i storlek.  Detta säkerställer framtida kompatibilitet om du bestämmer dig för att aktivera routning av undernätet till ett lokalt nätverk eftersom vissa routrar bara kan acceptera /64 IPv6-vägar.  
- Skydda dina resurser med IPv6-regler för nätverkssäkerhetsgrupper.
    - Och Azure-plattformens DDoS-skydd (Distributed Denial of Service) utvidgas till att omfatta offentliga IP-er som står inför Internet
- Anpassa routningen av IPv6-trafik i det virtuella nätverket med användardefinierade vägar, särskilt när du utnyttjar virtuella nätverksinstallationer för att utöka ditt program.
- Linux- och Windows-virtuella datorer kan alla använda IPv6 för Azure VNET
- Stöd för [iPv6-standardbelastningsutjämning](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) för att skapa elastiska, skalbara program, som omfattar:
    - Valfri IPv6-hälsoavsökning för att avgöra vilka servergruppsinstanser som är hälsotillstånd och därmed kan ta emot nya flöden.
    - Valfria utgående regler som ger fullständig deklarativ kontroll över utgående anslutning för att skala och justera den här möjligheten till dina specifika behov.
    - Valfria frontend-konfigurationer som gör det möjligt för en enda belastningsutjämnare att använda flera offentliga IPv6-IP-adresser– samma frontend-protokoll och port kan återanvändas över frontend-adresser.
    - Valfria IPv6-portar kan återanvändas på serverdinstanser med hjälp av *funktionen Flytande IP* i belastningsutjämningsregler 
- [Standard IPv6 internt belastningsutjämningsstöd](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) för att skapa elastiska program på flera nivåer i Azure VNETs.  
- Grundläggande IPv6-stöd för offentlig belastningsutjämning för kompatibilitet med äldre distributioner
- [Reserverade IPv6-offentliga IP-adresser och adressintervall](ipv6-public-ip-address-prefix.md) ger stabila, förutsägbara IPv6-adresser som underlättar vitlistning av dina azure-hostade program för ditt företag och dina kunder.
- Public IP på instansnivå ger IPv6 Internet-anslutning direkt till enskilda virtuella datorer.
- [Lägg till IPv6 i befintliga IPv4-distributioner](ipv6-add-to-existing-vnet-powershell.md)– med den här funktionen kan du enkelt lägga till IPv6-anslutning till befintliga IPv4-distributioner utan att behöva återskapa distributioner.  IPv4-nätverkstrafiken påverkas inte under den här processen så beroende på ditt program och operativsystem kan du kanske lägga till IPv6 även i live-tjänster.    
- Låt Internet-klienter sömlöst komma åt ditt dual stack-program med hjälp av sina protokoll val med Azure DNS-stöd för IPv6 (AAAA) poster. 
- Skapa program med dubbla staplar som automatiskt skalas till din belastning med skaluppsättningar för virtuella datorer med IPv6.
- [Virtual Network (VNET) Peering](virtual-network-peering-overview.md) - både inom-regional och global peering - gör att du kan seemlessly ansluta dual stack VNETs-både IPv4 och IPv6 slutpunkter på virtuella datorer i peered-nätverk kommer att kunna kommunicera med varandra. Du kan även peer dual stack med IPv4-bara VNETs som du övergår dina distributioner till dual stack. 
- IPv6 Felsökning och diagnostik är tillgängliga med belastningsutjämnad mått/aviseringar och nätverksbevakningsfunktioner som paketfångst, NSG-flödesloggar, felsökning av anslutningar och anslutningsövervakning.   

## <a name="scope"></a>Omfång
IPv6 för Azure VNET är en grundläggande funktionsuppsättning som gör det möjligt för kunder att vara värdar för IPv4+IPv6-program (Dual Stack) i Azure.  Vi har för avsikt att lägga till IPv6-stöd till fler Azure-nätverksfunktioner över tid och så småningom erbjuda dubbla stackversioner av Azure PaaS-tjänster, men under tiden kan alla Azure PaaS-tjänster nås via IPv4-slutpunkterna på virtuella datorer med dubbla stackar.   

## <a name="limitations"></a>Begränsningar
Den aktuella versionen av IPv6 för Azure-virtuella nätverk har följande begränsningar:
- IPv6 för Azure virtuellt nätverk (Preview) är tillgängligt i alla globala Azure-regioner, men bara i Global Azure- ännu inte i myndighetsmoln.
- ExpressRoute- och VPN-gateways kan inte användas i ett VNET med IPv6 aktiverat, antingen direkt eller peered med "UseRemoteGateway". 
- Azure-plattformen (AKS, etc.) stöder inte IPv6-kommunikation för behållare.  

## <a name="pricing"></a>Prissättning

IPv6 Azure-resurser och bandbredd debiteras med samma priser som IPv4. Det finns inga extra eller olika avgifter för IPv6. Du hittar information om priser för [offentliga IP-adresser,](https://azure.microsoft.com/pricing/details/ip-addresses/) [nätverksbandbredd](https://azure.microsoft.com/pricing/details/bandwidth/)eller [belastningsutjämnare](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar ett IPv6-program med dubbla stackar med Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Lär dig hur du [distribuerar ett IPv6-program med dubbla stackar med Azure CLI](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Lär dig hur du [distribuerar ett IPv6-program med dubbla stackar med Hjälp av Resource Manager-mallar (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
