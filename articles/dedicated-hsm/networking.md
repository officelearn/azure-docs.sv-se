---
title: Nätverksöverväganden – Azure dedikerad HSM | Microsoft Docs
description: Översikt över Nätverksöverväganden som är tillämpliga för distributioner som Azure dedikerad HSM
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: barclayn
ms.openlocfilehash: d6672827a87fbb949237d51310f1a9febc192ff2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58886359"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure dedikerad HSM-nätverk

Azure dedikerad HSM kräver en mycket säker nätverksmiljö. Detta gäller oavsett om det är från Azure cloud tillbaka till kundens IT-miljö (lokalt), med hjälp av distribuerade program eller för scenarier med hög tillgänglighet. Azure-nätverket innehåller detta och det finns fyra olika områden som måste åtgärdas.

- Skapa HSM-enheter i ditt virtuella nätverk (VNet) i Azure
- Anslutning mellan lokala och molnbaserade resurser för konfiguration och hantering av HSM-enheter
- Skapa och ansluta virtuella nätverk för kommunikation mellan anslutande programresurser och HSM-enheter
- Ansluta virtuella nätverk i flera regioner för kommunikation mellan kommunikation och aktivera scenarier med hög tillgänglighet

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtuellt nätverk för dina dedikerade HSM: er

Dedikerade HSM: er är integrerad i ett virtuellt nätverk och placeras i kunder egna privata nätverk i Azure. Detta möjliggör åtkomst till enheter från virtuella datorer eller beräkningsresurser i det virtuella nätverket.  
Läs mer om integrering av Azure-tjänster i det virtuella nätverket och de funktioner den erbjuder [virtuellt nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md) dokumentation.

### <a name="virtual-networks"></a>Virtuella nätverk

Innan du etablerar en dedikerad HSM-enhet, måste kunder först skapa ett virtuellt nätverk i Azure eller använda en som redan finns i prenumerationen för kunder. Det virtuella nätverket definierar säkerhetsperimeter för dedikerad HSM-enheten. Mer information om hur du skapar virtuella nätverk finns i [dokumentation om virtual network](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Undernät

Undernät segmentera det virtuella nätverket i separata adressutrymmen som kan användas av Azure-resurserna du placerar i dem. Dedikerade HSM: er har distribuerats till ett undernät i det virtuella nätverket. Varje dedikerad HSM-enhet som har distribuerats i kundens undernät får en privat IP-adress från det här undernätet. Undernätet där HSM-enheten distribueras måste uttryckligen delegeras till tjänsten: Microsoft.HardwareSecurityModules/dedicatedHSMs. Detta ger vissa behörigheter för HSM-tjänst för distribution till undernätet. Delegering till dedikerade HSM: er begränsningar princip i undernätet. Nätverkssäkerhetsgrupper (NSG) och användardefinierade vägar (Udr) stöds för närvarande inte i delegerade undernät. Därför när ett undernät har delegerats till dedikerade HSM: er kan kan det bara användas för att distribuera HSM-resurser. Misslyckas distributionen av andra kundresurser i undernätet.


### <a name="expressroute-gateway"></a>ExpressRoute-gateway

Ett krav i den aktuella arkitekturen är konfigurationen av en ER gateway i kunder undernätet där en HSM-enhet behöver placeras om du vill aktivera integrering av HSM-enheten i Azure. Den här ER-gatewayen kan inte användas för att ansluta lokala platser till HSM-enheter för kunder i Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Ansluta din lokala IT till Azure

När du skapar molnbaserade resurser, det är ett vanliga krav för en privat anslutning tillbaka till den lokala IT-resurser. När det gäller dedikerad HSM blir främst för HSM-klientprogrammet att konfigurera HSM-enheter och för aktiviteter, till exempel säkerhetskopior och hämta loggar från HSM: er för analys. En viktig beslutspunkten här är typen av anslutningen som det finns alternativ.  Det mest flexibla alternativet är VPN för plats-till-plats som kommer troligen att flera lokala resurser som kräver säker kommunikation med resurser (inklusive HSM: er) i Azure-molnet. Detta kräver en kundens organisation att ha en VPN-enhet för att underlätta anslutningen. En punkt-till-plats-VPN-anslutning kan användas om det är bara en enda slutpunkt på plats, till exempel en enkel administration-arbetsstation.
Mer information om alternativ för nätverksanslutning finns [VPN Gateway planning alternativ](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> ExpressRoute är inte ett alternativ för anslutning till lokala resurser för tillfället. Det bör också noteras att ExpressRoute-gatewayen används enligt beskrivningen ovan, är inte för anslutningar till den lokala infrastrukturen.

### <a name="point-to-site-vpn"></a>Punkt-till-plats-VPN

Ett virtuellt privat nätverk för punkt-till-plats är den enklaste formen av säker anslutning till en enda slutpunkt på plats. Detta kan vara användbart om du planerar att bara ha en enda administration arbetsstation för alla Azure-baserade dedikerade HSM: er.

### <a name="site-to-site-vpn"></a>Plats-till-plats-VPN

En plats-till-plats virtuellt privat nätverk möjliggör säker kommunikation mellan Azure-baserade dedikerade HSM: er och din lokala IT. En orsak till att göra detta är att ha en säkerhetskopiering anläggning för HSM på plats och behöver en anslutning mellan två för att köra säkerhetskopieringen.

## <a name="connecting-virtual-networks"></a>Ansluta virtuella nätverk

En typisk distributionsarkitektur för dedikerad HSM startar med ett enda virtuellt nätverk och motsvarande undernät där HSM-enheter skapas och etableras. Inom samma regionen, kan det också finnas ytterligare virtuella nätverk och undernät för programkomponenter som skulle använder dedikerade HSM. Om du vill aktivera kommunikation i dessa nätverk, använder vi Peering för virtuellt nätverk.

### <a name="virtual-network-peering"></a>Virtuell nätverkspeering

När det finns flera virtuella nätverk i en region som behöver åtkomst till varandras resurser, kan Peering för virtuellt nätverk användas för att skapa kanaler för säker kommunikation mellan dem.  Virtuell nätverkspeering ger inte bara skydda kommunikationen men innebär också en låg fördröjning och hög bandbredd anslutningar mellan resurser i Azure.

![Nätverks-peering](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Ansluta i Azure-regioner

HSM-enheter har möjlighet, via programvara bibliotek, att omdirigera trafik till en annan HSM. Omdirigering av trafik är användbart om enheter misslyckas eller åtkomst till en enhet tappas bort. Regionala nivå felscenarion kan undvikas genom att distribuera HSM: er i andra regioner och aktivera kommunikation mellan virtuella nätverk i olika regioner.

### <a name="cross-region-ha-using-vpn-gateway"></a>Mellan regioner hög tillgänglighet med hjälp av VPN-gateway

För globalt distribuerade program eller för scenarier med hög tillgänglighet regional redundans krävs det att ansluta virtuella nätverk i olika regioner. Med Azure dedikerad HSM uppnås hög tillgänglighet med hjälp av en VPN-Gateway som ger en säker tunnel mellan de två virtuella nätverken. Mer information om Vnet-till-Vnet-anslutningar med hjälp av VPN-Gateway finns i artikeln [vad är VPN-Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

> [!NOTE]
> Global Vnet-peering är inte tillgänglig i interregionala anslutningen scenarier med dedikerade HSM: er vid denna tidpunkt och VPN-gateway som ska användas istället. 

![Global vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar](faq.md)
- [Support](supportability.md)
- [Hög tillgänglighet](high-availability.md)
- [Fysisk säkerhet](physical-security.md)
- [Övervakning](monitoring.md)
- [Distributionsarkitektur för](deployment-architecture.md)