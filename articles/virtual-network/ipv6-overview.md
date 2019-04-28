---
title: Översikt över IPv6 för Azure-nätverk (förhandsversion)
titlesuffix: Azure Virtual Network
description: IPv6-beskrivning av sökvägar för IPv6-slutpunkter och data i Azure-nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 0ec650880a45f6383b24b5ac810fc2ee745806b7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62131038"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Vad är IPv6 för Azure Virtual Network? (Förhandsversion)

IPv6 för Azure Virtual Network (VNET) kan du hantera program i Azure med IPv6 och IPv4-anslutningen både inom ett virtuellt nätverk och till och från Internet. På grund av överbelastning av offentliga IPv4-adresser skapas ofta nya nätverk för mobilitet och Internet of Things (IoT) på IPv6. Även långa upprätta ISP- och mobila nätverk som omvandlas till IPv6. Endast IPv4-tjänster hittar sig på en verklig nackdel på både befintliga och nya marknader. Dual stack IPv4/IPv6-anslutningar kan Azure-baserade tjänster att bläddra bland luckan teknik med globalt tillgängliga, dual-liggande tjänster som lätt ansluter med både befintliga IPv4 och dessa nya IPv6-enheter och nätverk.

Azures ursprungliga IPv6-anslutning gör det enkelt att tillhandahålla dual stack (IPv4/IPv6) Internetanslutning för program på Azure. Det möjliggör enkel distribution av virtuella datorer med belastningsutjämnade IPv6-anslutningar för både inkommande och utgående anslutningar. Den här funktionen är fortfarande tillgängliga och mer information finns [här](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 för Azure-nätverket är mycket mer fullständig aktuella-aktivera fullständig lösningsarkitekturer för IPv6 distribueras i Azure.

> [!Important]
> IPv6 för Azure Virtual Network är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Följande diagram visar en enkel dual stack (IPv4/IPv6)-distribution i Azure:

![IPv6-nätverksdiagram för distribution](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Fördelar

Fördelarna med Azure virtuella nätverk IPv6:

- Hjälper dig att utöka räckvidden för dina Azure-baserade program till den växande mobila och IOT-marknader.
- Dubbel stående IPv4/IPv6-virtuella datorer ger flexibilitet för distribution av högsta service. En enda tjänstinstans kan ansluta med både IPv4 och IPv6-kompatibelt Internet-klienter.
- Bygger på lång tid, stabil Azure VM-till-Internet IPv6-anslutningar.
- Säker som standard eftersom upprättas endast IPv6-anslutning till Internet när du uttryckligen begär det i din distribution.

## <a name="capabilities"></a>Funktioner

IPv6-stöd för virtuella datorer innehåller följande funktioner:

- Azure-kunder kan definiera sina egna adressutrymmen för IPv6-nätverk för att uppfylla behoven i deras program, kunder, eller integrera fullständigt i sina lokala IP-adressutrymme.
- Dual stack (IPv4 och IPv6) virtuella nätverk med dual stack-undernät kan programmen kan ansluta till resurser för både IPv4 och IPv6 i det virtuella nätverket eller -Internet.
- Skydda dina resurser med IPv6-regler för Nätverkssäkerhetsgrupper
- Anpassa routning av IPv6-trafik i ditt virtuella nätverk med användardefinierade vägar – särskilt när möjligheterna med virtuella nätverksinstallationer för att utöka ditt program.
- IPv6-belastningsutjämnare stöd för att skapa flexibla, skalbara program med Azure DNS-stöd för AAAA-poster för IPv6 offentliga IP-adresser.
- Lägg enkelt till IPv6-anslutningar till befintliga endast IPv4-distributioner med uppgradering på plats.
- Skapa dual stack-program som skalas automatiskt till inläsningen med VM-skalningsuppsättningar.

## <a name="limitations"></a>Begränsningar
Förhandsversionen av IPv6 för Azure-nätverket har följande begränsningar:
- IPv6 för Azure-nätverk (förhandsversion) är tillgänglig i alla Azure-regioner globalt, men endast i globala Azure - inte government-moln.   
- Portal-support för förhandsversionen är begränsad till att visa endast för mycket men inte alla IPv6-konfigurationen, men IPv6 för virtuella nätverk har fullständigt stöd och dokumentation (med exempel) för IPv6-distribution med Azure Powershell och kommandoradsgränssnittet (CLI).
- Network Watcher-stöd för förhandsversionen är begränsat till NSG-flödesloggar och nätverkspaket.
- Belastningen belastningsutjämning support för förhandsversionen är inledningsvis begränsad till Basic Load Balancer.
- Offentliga IP-adresser på instansnivå (offentliga IP-adresser direkt på en virtuell dator) stöds inte i en förhandsversion.  
- Virtuell nätverkspeering stöds (regionalt eller globalt) inte i en förhandsversion. 

## <a name="pricing"></a>Prissättning

IPv6-Azure-resurser och bandbredd debiteras enligt samma avgifter som för IPv4. Det finns inga ytterligare eller andra avgifter för IPv6. Du hittar information om priser för [offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/), [nätverkets bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/), eller [belastningsutjämnaren](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuera ett IPv6-dual stack-program med Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-powershell.md).
- Lär dig hur du [distribuera ett IPv6-dual stack-program med Azure CLI](virtual-network-ipv4-ipv6-dual-stack-cli.md).
