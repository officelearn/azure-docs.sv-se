---
title: Network integration överväganden för integrerade Azure Stack-system | Microsoft Docs
description: Lär dig vad du kan göra för att planera för datacenter nätverksintegrering med flera noder Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 2d16d1dc7a53ca388b00ba02b6447e178a9f6edb
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989245"
---
# <a name="network-connectivity"></a>Nätverksanslutning
Den här artikeln innehåller information om hur du bestämmer hur du integrerar Azure Stack bäst i din befintliga nätverksmiljö för Azure Stack nätverk infrastruktur. 

> [!NOTE]
> För att lösa externa DNS-namn från Azure Stack (till exempel www.bing.com), måste du ange DNS-servrar för att vidarebefordra DNS-förfrågningar. Läs mer om Azure Stack DNS-krav, [Azure Stack datacenter-integrering - DNS-](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Fysiska nätverksdesign
Azure Stack-lösning kräver en flexibel och högtillgänglig fysisk infrastruktur som stöd för dess drift och tjänster. Överordnade länkar från ToR till kantlinje växlar är begränsade till SFP + media och 1 GB eller 10 GB hastigheter. Kontrollera med maskinvaruleverantören OEM-tillverkare (original equipment manufacturer) för tillgänglighet. Följande diagram visar våra rekommenderade design:

![Rekommenderade nätverksdesign för Azure Stack](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logiska nätverk
Logiska nätverk är en abstraktion av den underliggande fysiska nätverksinfrastrukturen. De används för att ordna och förenkla nätverksallokeringarna för värdar, virtuella datorer och tjänster. Som en del av skapandet av logiskt nätverk skapas nätverksplatser för att definiera de virtuella lokala nätverk (VLAN), IP-undernät och IP-undernät/VLAN-par som är associerade med det logiska nätverket på varje fysisk plats.

I följande tabell visas de logiska nätverken och associerade IPv4-undernät-intervall som du måste planera för:

| Logiskt nätverk | Beskrivning | Storlek | 
| -------- | ------------- | ------------ | 
| Offentlig VIP | Azure Stack använder Totalt 32 adresser från det här nätverket. Åtta offentliga IP-adresser som används för en liten uppsättning Azure Stack-tjänster och resten som används av virtuella datorer. Om du planerar att använda App Service och SQL-resursprovidrar används 7 fler adresser. | / 26 (62 värdar) - /22 (1022 värdar)<br><br>Rekommenderade = /24 (254 värdar) | 
| Växeln infrastruktur | Point-to-Point IP-adresser för routningen, dedikerad växla hanteringsgränssnitt och loopback-adresser tilldelade till växeln. | /26 | 
| Infrastruktur | Används för Azure Stack interna komponenter för att kommunicera. | /24 |
| Privat | Används för nätverkslagring och privata virtuella IP-adresser. | /24 | 
| BMC | Används för att kommunicera med bmc på de fysiska värdarna. | /27 | 
| | | |

## <a name="network-infrastructure"></a>Nätverksinfrastruktur
Nätverksinfrastruktur för Azure Stack består av flera logiska nätverk som är konfigurerade med växlar. I följande diagram visas dessa logiska nätverk och hur de integreras med det top-of-racket (TOR) hanteringsstyrenhet för baskort (BMC) och border (kunden) nätverksväxlar.

![Logiska nätverksanslutningar för diagram och växel](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC-nätverk
Det här nätverket är dedikerad att ansluta alla hanteringsstyrenheter för baskort (även känt som service processorer, till exempel iDRAC, iLO, iBMC osv) till hanteringsnätverket. Om det finns maskinvara livscykel värden (HLH) finns i det här nätverket och kan ha OEM-specifik programvara för maskinvaruunderhåll och övervakning. 

HLH är också värd för distribution av virtuell dator (DVM). DVM används under distributionen av Azure Stack och tas bort när distributionen är klar. DVM kräver tillgång till internet i anslutna scenarier för att testa, verifiera och få åtkomst till flera komponenter. De här komponenterna kan vara i och utanför företagets nätverk; NTP, DNS och Azure. Mer information om krav för anslutning finns i den [NAT-avsnittet i integrering med Azure Stack-brandväggen](azure-stack-firewall.md#network-address-translation). 

### <a name="private-network"></a>Privat nätverk
Den här /24 (254 värd IP-adresser) nätverk är privat till Azure Stack-region (inte expanderas utöver kantlinje växel enheter för Azure Stack-region) och är uppdelad i två undernät:

- **Lagringsnätverk**. /25 (126 värd IP-adresser) används för att ge stöd åt lagringstrafik för Lagringsdirigering och Server Message Block (SMB) och Direktmigrering för virtuella datorer. 
- **Interna virtuella IP-nätverket**. A/25 nätverk dedikerad till interna virtuella IP-adresser för belastningsutjämnare för programvara.

### <a name="azure-stack-infrastructure-network"></a>Nätverket för Azure Stack-infrastruktur
Det/24 nätverk som är dedikerad till interna Azure Stack-komponenter så att de kan kommunicera och utbyta data med varandra. Det här undernätet kräver dirigerbara IP-adresser, men förblir privata till lösningen med hjälp av åtkomstkontrollistor (ACL). Det är inte förväntas att dirigeras utanför kantlinje växlar utom ett mindre intervall som är samma storlek som en/27 nätverk som används av några av dessa tjänster när de behöver komma åt externa resurser och/eller internet. 

### <a name="public-infrastructure-network"></a>Infrastruktur för offentliga nätverk
Detta/27 nätverket är liten mellan Azure Stack-infrastruktur-undernätet som nämnts tidigare, kräver inte offentliga IP-adresser, men det kräver Internetåtkomst via en NAT eller Transparent Proxy. Det här nätverket ska allokeras för nödadministration Recovery konsolen System (ERCS), ERCS VM kräver Internetåtkomst under registreringen till Azure och under infrastruktur för säkerhetskopiering. ERCS VM ska vara dirigerbara till nätverket i felsökningssyfte.

### <a name="public-vip-network"></a>Offentliga VIP-nätverk
Den offentliga VIP-nätverket har tilldelats nätverksstyrenheten i Azure Stack. Det är inte ett logiskt nätverk på växeln. SLB använder poolen med adresser och tilldelar/32 nätverk för klienternas arbetsbelastningar. Dessa 32 IP-adresser annonseras på växeln routningstabell, som en väg som är tillgängliga via BGP. Det här nätverket innehåller externt-tillgängliga eller offentliga IP-adresser. Azure Stack-infrastruktur reserverar de första 31 adresserna från den här offentliga VIP-nätverk medan resten används av virtuella klientdatorer. Nätverket storleken på det här undernätet kan variera mellan minst /26 (64 värdar) till högst /22 (1022 värdar) rekommenderar vi att du planerar för ett/24 nätverk.

### <a name="switch-infrastructure-network"></a>Byt infrastruktur för nätverk
Detta/26 nätverk är det undernät som innehåller dirigerbara point-to-point IP/30 (IP-adresser med 2 host)-undernät och loopbacks som är dedikerade/32 undernät för in-band-växla hanterings- och BGP-router-ID. Den här IP-adressintervall måste vara dirigerbara externt av Azure Stack-lösningen till ditt datacenter, de kan vara privat eller offentlig IP-adresser.

### <a name="switch-management-network"></a>Växeln hanteringsnätverk
Den här /29 (6 värd IP-adresser) nätverk som är dedikerad att ansluta hanteringsportar växlar. Det gör out-of-band-åtkomst för distribution, hantering och felsökning. Det beräknas från växeln nätverkets infrastruktur som nämns ovan.

## <a name="publish-azure-stack-services"></a>Publicera Azure Stack-tjänster
Du behöver göra Azure Stack-tjänster tillgängliga för användare från utanför Azure Stack. Azure Stack ställer in olika slutpunkter för dess infrastrukturroller. De här slutpunkterna tilldelas virtuella IP-adresser från den offentliga IP-adresspoolen. En DNS-post skapas för varje slutpunkt i externa DNS-zon som angavs vid tidpunkten för distribution. Till exempel tilldelas användarportalen DNS-värdpost i portalen.  *&lt;region >.&lt; FQDN >*.

### <a name="ports-and-urls"></a>Portar och URL: er
Att göra Azure Stack-tjänster (till exempel portaler, Azure Resource Manager, DNS, osv.) tillgängliga för externa nätverk, måste du tillåta inkommande trafik till dessa slutpunkter för specifika URL: er, portar och protokoll.
 
I en distribution där en transparent proxy överordnade länkar till en traditionell proxyserver måste du tillåta specifika portar och URL: er för både [inkommande](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound) och [utgående](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound) kommunikation. Dessa inkluderar portar och URL: er för identitet, marketplace syndikering, patch och uppdatering, registrering och användningsdata.

## <a name="next-steps"></a>Nästa steg
[Kantlinje anslutning](azure-stack-border-connectivity.md)