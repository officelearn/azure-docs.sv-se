---
title: Nätverksöverväganden – Azure Dedikerad HSM | Microsoft-dokument
description: Översikt över nätverksöverväganden som gäller för Azure Dedikerad HSM-distributioner
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 044930c9df7b54515b9b66426a6b05aa9517a3a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881286"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure dedikerad HSM-nätverk

Azure Dedikerad HSM kräver en mycket säker nätverksmiljö. Detta gäller oavsett om det är från Azure-molnet tillbaka till kundens IT-miljö (lokalt), med distribuerade program eller för scenarier med hög tillgänglighet. Azure Networking tillhandahåller detta och det finns fyra olika områden som måste åtgärdas.

- Skapa HSM-enheter i ditt virtuella nätverk (VNet) i Azure
- Ansluta lokalt till molnbaserade resurser för konfiguration och hantering av HSM-enheter
- Skapa och ansluta virtuella nätverk för sammankoppling av programresurser och HSM-enheter
- Ansluta virtuella nätverk mellan regioner för interkommunikation och även för att möjliggöra scenarier med hög tillgänglighet

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtuellt nätverk för dina dedikerade HSM-tillverkare

Dedikerade HSM-moduler är integrerade i ett virtuellt nätverk och placeras i kundernas eget privata nätverk i Azure. Detta ger åtkomst till enheter från virtuella datorer eller beräkningsresurser i det virtuella nätverket.  
Mer information om hur du integrerar Azure-tjänster i det virtuella nätverket och de funktioner som finns i dokumentationen [till virtuella nätverk för Azure-tjänster.](../virtual-network/virtual-network-for-azure-services.md)

### <a name="virtual-networks"></a>Virtuella nätverk

Innan du etablerar en dedikerad HSM-enhet måste kunderna först skapa ett virtuellt nätverk i Azure eller använda en som redan finns i kundens prenumeration. Det virtuella nätverket definierar säkerhetsperimetern för den dedikerade HSM-enheten. Mer information om hur du skapar virtuella nätverk finns i [dokumentationen för virtuella nätverk](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Undernät

Undernät segmenterar det virtuella nätverket i separata adressutrymmen som kan kan hanteras av de Azure-resurser som du placerar i dem. Dedikerade HSM:er distribueras till ett undernät i det virtuella nätverket. Varje dedikerad HSM-enhet som distribueras i kundens undernät får en privat IP-adress från det här undernätet. Undernätet där HSM-enheten distribueras måste uttryckligen delegeras till tjänsten: Microsoft.HardwareSecurityModules/dedicatedHSMs. Detta ger vissa behörigheter till HSM-tjänsten för distribution till undernätet. Delegering till särskilda HSM-tillverkare medför vissa policybegränsningar för undernätet. Nätverkssäkerhetsgrupper (NSG) och användardefinierade vägar (UDR) stöds för närvarande inte i delegerade undernät. När ett undernät har delegerats till dedikerade HSM-datorer kan det därför bara användas för att distribuera HSM-resurser. Distribution av andra kundresurser i undernätet misslyckas.


### <a name="expressroute-gateway"></a>ExpressRoute-gateway

Ett krav för den aktuella arkitekturen är konfigurationen av en ER-gateway i kundens undernät där en HSM-enhet måste placeras för att möjliggöra integrering av HSM-enheten i Azure. Den här ER-gatewayen kan inte användas för att ansluta lokala platser till kundernas HSM-enheter i Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Ansluta din lokala IT till Azure

När du skapar molnbaserade resurser är det ett typiskt krav för en privat anslutning tillbaka till lokala IT-resurser. När det gäller dedikerad HSM, detta kommer främst att vara för HSM klientprogramvara för att konfigurera HSM-enheter och även för aktiviteter som säkerhetskopior och dra loggar från HSMs för analys. En viktig beslutspunkt här är arten av anslutningen eftersom det finns alternativ.  Det mest flexibla alternativet är Site-to-Site VPN eftersom det sannolikt kommer att finnas flera lokala resurser som kräver säker kommunikation med resurser (inklusive HSM) i Azure-molnet. Detta kräver att en kundorganisation har en VPN-enhet för att underlätta anslutningen. En Punkt-till-plats VPN-anslutning kan användas om det bara finns en enda slutpunkt lokalt, till exempel en enda administrationsarbetsstation.
Mer information om anslutningsalternativ finns i [planeringsalternativ för VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> För närvarande är ExpressRoute inte ett alternativ för anslutning till lokala resurser. Det bör också noteras att ExpressRoute Gateway som används enligt beskrivningen ovan inte är för anslutningar till lokal infrastruktur.

### <a name="point-to-site-vpn"></a>Punkt-till-plats-VPN

Ett virtuellt privat nätverk från punkt till plats är den enklaste formen av säker anslutning till en enskild slutpunkt lokalt. Detta kan vara relevant om du bara tänker ha en enda administrationsarbetsstation för Azure-baserade dedikerade HSM:er.

### <a name="site-to-site-vpn"></a>Plats-till-plats-VPN

Ett virtuellt privat nätverk för plats till plats möjliggör säker kommunikation mellan Azure-baserade dedikerade HSM:er och din lokala IT. En anledning att göra detta är att ha en backup-anläggning för HSM: s lokala och behöver en anslutning mellan de två för att köra säkerhetskopian.

## <a name="connecting-virtual-networks"></a>Ansluta virtuella nätverk

En typisk distributionsarkitektur för dedikerad HSM börjar med ett enda virtuellt nätverk och motsvarande undernät där HSM-enheterna skapas och etableras. Inom samma region kan det mycket väl finnas ytterligare virtuella nätverk och undernät för programkomponenter som skulle använda sig av den särskilda HSM. För att möjliggöra kommunikation mellan dessa nätverk använder vi Virtual Network Peering.

### <a name="virtual-network-peering"></a>Virtuell nätverkspeering

När det finns flera virtuella nätverk inom en region som behöver komma åt varandras resurser kan Virtual Network Peering användas för att skapa säkra kommunikationskanaler mellan dem.  Virtuell nätverks peering ger inte bara säker kommunikation utan säkerställer också en låg latens och hög bandbredd anslutningar mellan resurserna i Azure.

![nätverks peering](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Ansluta över Azure-regioner

HSM-enheterna har möjlighet att via programvarubibliotek dirigera om trafiken till en alternativ HSM. Trafikomdirigering är användbart om enheter misslyckas eller åtkomst till en enhet går förlorad. Felscenarier på regional nivå kan minskas genom att distribuera HSM:er i andra regioner och möjliggöra kommunikation mellan virtuella nätverk mellan regioner.

### <a name="cross-region-ha-using-vpn-gateway"></a>Ha för flera regioner med VPN-gateway

För globalt distribuerade program eller för regionala redundansscenarier med hög tillgänglighet krävs att virtuella nätverk kopplas samman mellan regioner. Med Azure Dedicated HSM kan hög tillgänglighet uppnås med hjälp av en VPN-gateway som ger en säker tunnel mellan de två virtuella nätverken. Mer information om Vnet-to-Vnet-anslutningar med VPN Gateway finns i artikeln [What is VPN Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

> [!NOTE]
> Global Vnet-peering är inte tillgänglig i anslutningsscenarier mellan regioner med dedikerade HSM:er just nu och VPN-gatewayen bör användas i stället. 

![globalt vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar](faq.md)
- [Support](supportability.md)
- [Hög tillgänglighet](high-availability.md)
- [Fysisk säkerhet](physical-security.md)
- [Övervakning](monitoring.md)
- [Distributionsarkitektur](deployment-architecture.md)