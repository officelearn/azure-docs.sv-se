---
title: Översikt över IPv6 för Azure Virtual Network (för hands version)
titlesuffix: Azure Virtual Network
description: IPv6-Beskrivning av IPv6-slutpunkter och data Sök vägar i ett virtuellt Azure-nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5093b74484cd04a0c0c7afed8e2ebc725af033f5
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249837"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Vad är IPv6 för Azure Virtual Network? (Förhandsversion)

Med IPv6 för Azure Virtual Network (VNet) kan du vara värd för program i Azure med IPv6-och IPv4-anslutning både i ett virtuellt nätverk och till och från Internet. På grund av belastningen på offentliga IPv4-adresser bygger nya nätverk för mobilitet och Sakernas Internet (IoT) ofta på IPv6. Även lång upprättade ISP och mobila nätverk omvandlas till IPv6. Endast IPv4-tjänster kan få en riktig nackdel på både befintliga och nya marknader. Med IPv4/IPv6-anslutning med dubbla stackar kan Azure-värdbaserade tjänster förflytta sig i den här tekniken med globalt tillgängliga, dubbla tjänster som enkelt kan ansluta till både befintliga IPv4 och dessa nya IPv6-enheter och-nätverk.

Azures ursprungliga IPv6-anslutning gör det enkelt att tillhandahålla en Internet anslutning med dubbla stackar (IPv4/IPv6) för program som finns i Azure. Det möjliggör enkel distribution av virtuella datorer med belastningsutjämnad IPv6-anslutning för både inkommande och utgående initierade anslutningar. Den här funktionen är fortfarande tillgänglig och mer information finns [här](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 för Azure Virtual Network är mycket mer komplett – så att fullständiga IPv6-lösnings arkitekturer kan distribueras i Azure.

> [!Important]
> IPv6 för Azure Virtual Network är för närvarande en offentlig för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Följande diagram visar en enkel distribution med dubbla stackar (IPv4/IPv6) i Azure:

![Diagram över IPv6-nätverks distribution](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Fördelar

Fördelar med Azure Virtual Network IPv6:

- Hjälper till att utöka räckvidden för dina Azure-värdbaserade program till de växande mobila och Sakernas Internet marknaderna.
- Dubbla stackade IPv4/IPv6-datorer ger maximal flexibilitet för tjänst distribution. En enskild tjänst instans kan ansluta till både IPv4-och IPv6-kompatibla Internet klienter.
- Bygger på lång upprättade, stabila Azure VM-till-Internet IPv6-anslutning.
- Skydda som standard eftersom IPv6-anslutning till Internet endast upprättas när du uttryckligen begär det i distributionen.

## <a name="capabilities"></a>Funktioner

IPv6 för VNet innehåller följande funktioner:

- Azure-kunder kan definiera sina egna adress utrymmen för det virtuella IPv6-nätverket för att möta behoven hos sina program, kunder eller sömlöst integrera dem i det lokala IP-utrymmet.
- Virtuella nätverk med dubbla stackar (IPv4 och IPv6) med dubbla stack-undernät gör det möjligt för program att ansluta till både IPv4-och IPv6-resurser i sitt virtuella nätverk eller Internet.
- Skydda dina resurser med IPv6-regler för nätverks säkerhets grupper
- Anpassa routningen av IPv6-trafik i det virtuella nätverket med användardefinierade vägar – särskilt när du använder virtuella nätverks installationer för att förstärka ditt program.
- Låt Internet klienter sömlöst få åtkomst till ditt program med dubbla stackar med hjälp av det protokoll du väljer med Azure DNS stöd för IPv6-poster (AAAA). 
- Standard IPv6 offentlig Load Balancer stöd för att skapa elastiska, skalbara program som innehåller:
    - Valfri IPv6-hälso avsökning för att avgöra vilka instanser av backend-poolen som är hälso tillstånd och kan därmed ta emot nya flöden. .  
    - Valfria utgående regler som ger fullständig deklarativ kontroll över utgående anslutning för skalning och justering av den här funktionen för dina speciella behov.
    - Valfria flera klient konfigurations inställningar som gör att en enkel belastningsutjämnare kan använda flera offentliga IPv6-IP-adresser, samma klient dels protokoll och port kan återanvändas över klient dels adresser.
- Offentlig IP-adress på instans nivå tillhandahåller IPv6-Internet anslutning direkt till enskilda virtuella datorer.
- Lägg enkelt till IPv6-anslutning till befintliga IPv4-distributioner med uppgradering på plats.
- Skapa dubbla stack-program som automatiskt skalar till din belastning med skalnings uppsättningar för virtuella datorer.
- Portal stöd för förhands granskningen innehåller interaktiva skapa/redigera/ta bort virtuella nätverk (IPv4 + IPv6) och undernät, IPv6-regler för nätverks säkerhets grupper, IPv6-användardefinierade vägar och offentliga IPv6-IP-adresser.  

## <a name="limitations"></a>Begränsningar
För hands versionen av IPv6 för Azure Virtual Network har följande begränsningar:
- IPv6 för Azure Virtual Network (för hands version) är tillgängligt i alla globala Azure-regioner, men endast i globala Azure – inte i de offentliga molnen.
- Portal stöd för Standard Load Balancer-komponenter är endast skrivskyddad.  Fullständig support och dokumentation (med exempel) är tillgänglig för Standard Load Balancer-distributioner med hjälp av Azure PowerShell och kommando rads gränssnittet (CLI).   
- Network Watcher stöd för förhands granskningen är begränsat till NSG flödes loggar och infångade nätverks paket.
- Peering för virtuella nätverk (regionalt eller globalt) stöds inte i för hands versionen.
- När du använder standard för externa IPv6-Load Balancer gäller följande begränsningar: 
  - Utgående regler kan referera till flera offentliga IP-adresser på klient sidan, men kan **inte** referera till ett offentligt IPv6-prefix. IP-publikt prefix stöder endast IPv4-prefix.
  - Belastnings Utjämnings regler för IPv6 får **inte** använda den *flytande IP-* funktionen. Åter användning av portar på Server dels instanser stöds bara med IPv4.
- Reservering av ett block med IPv6-adresser som riktas mot Internet stöds inte av funktionen för Azures offentliga IP-adressprefix.

## <a name="pricing"></a>Prissättning

IPv6 Azure-resurser och bandbredd debiteras enligt samma taxa som IPv4. Det finns inga ytterligare eller andra avgifter för IPv6. Du kan hitta information om priser för [offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/), [nätverks bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/)eller [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar ett IPv6-program med dubbla stackar med Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Lär dig hur du [distribuerar ett IPv6-program med dubbla stackar med hjälp av Azure CLI](virtual-network-ipv4-ipv6-dual-stack-cli.md).
