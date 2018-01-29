---
title: "Nätverk integration överväganden för Azure-stacken integrerat system | Microsoft Docs"
description: "Lär dig vad du kan göra för att planera för datacenter nätverksintegration med flera noder Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 43d79a8c14751ee25eaca7a3b50649702d159d76
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
## <a name="network-connectivity"></a>Nätverksanslutning
Den här artikeln innehåller information om Azure-stacken nätverk infrastruktur som hjälper dig att bestämma hur du ska integrera Azure Stack bäst i din befintliga nätverksmiljö. 

> [!NOTE]
> För att lösa externa DNS-namn från Azure-stacken (till exempel www.bing.com), måste du ange DNS-servrar för att vidarebefordra DNS-förfrågningar. Mer information om krav för Azure-stacken DNS finns [Azure Stack-integrering för datacenter - DNS-](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Fysiska nätverksdesign
Azure Stack-lösning kräver en flexibel och högtillgänglig fysisk infrastruktur till stöd för dess drift och tjänster. Följande diagram representerar vår rekommenderade designen:

![Rekommenderade Azure Stack nätverksdesign](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logiska nätverk
Logiska nätverk är en abstraktion av den underliggande fysiska nätverksinfrastrukturen. De används för att ordna och förenkla nätverkstilldelningar för värdar, virtuella datorer och tjänster. Som en del av skapandet av logiskt nätverk skapas nätverksplatser för att definiera virtuella lokala nätverk (VLAN), IP-undernät och IP-undernät och VLAN-par som är associerade med det logiska nätverket på varje fysisk plats.

I följande tabell visas de logiska nätverken och associerade IPv4-undernät områden som måste du planera för:

| Logiskt nätverk | Beskrivning | Storlek | 
| -------- | ------------- | ------------ | 
| Offentliga VIP | Offentliga IP-adresser för en liten uppsättning Azure Stack-tjänster med resten används av virtuella datorer. Infrastrukturen i Azure-stacken använder 32-adresser från det här nätverket. Om du planerar att använda App Service och SQL-resursprovidrar används 7 flera adresser. | / 26 (62 värdar) - /22 (1022 värdar)<br><br>Rekommenderat = /24 (254 värdar) | 
| Växeln-infrastruktur | Point-to-Point IP-adresser för routning, dedikerad växla hanteringsgränssnitt och loopback-adresser som är tilldelad till växeln. | /26 | 
| Infrastruktur | Används för Azure-stacken interna komponenter för att kommunicera. | /24 |
| Privat | Används för lagringsnätverk och privata virtuella IP-adresser. | /24 | 
| BMC | Används för att kommunicera med bmc på de fysiska värdarna. | /27 | 
| | | |

## <a name="network-infrastructure"></a>Nätverksinfrastruktur
Nätverkets infrastruktur för Azure-Stack består av flera logiska nätverk som har konfigurerats med växlar. Följande diagram visar dessa logiska nätverk och hur de samverkar med det top-of-racket (TOR) hanteringsstyrenhet för baskort (BMC) och border (kunden) nätverksväxlar.

![Logiska nätverksanslutningar för diagram och växel](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>BMC-nätverk
Det här nätverket är dedikerad till att ansluta alla nodernas hanteringsstyrenheter för baskort (även kallat service processorer, till exempel iDRAC, iLO, iBMC, etc.) i hanteringsnätverket. Om den finns (HLH) maskinvara livscykel värden finns på nätverket och kan ge OEM-specifik programvara för maskinvara underhåll och övervakning. 

### <a name="private-network"></a>Privat nätverk
Den här /24 (254 värd-IP)-nätverk är privat för stacken för Azure-region (inte expanderas utöver kantlinje växeln enheter i Azure Stack-region) och är uppdelad i två undernät:

- **Lagringsnätverk**. Direktmigrering för en /25 (126 värd-IP) för att användas med lagringsutrymmen och Server Message Block (SMB) lagringsrelaterad trafik och den virtuella datorn. 
- **Interna virtuella IP-nätverket**. A/25 nätverket att endast internt VIP för belastningsutjämnare för programvara.

### <a name="azure-stack-infrastructure-network"></a>Azure Stack infrastruktur för nätverk
Detta/24 nätverk som är dedikerad för interna Azure Stack-komponenter så att de kan kommunicera och utbyta data med varandra. Det här undernätet kräver dirigerbara IP-adresser, men behålls privata till lösningen med hjälp av åtkomstkontrollistor (ACL), den förväntas inte dirigeras utöver kantlinje växlar förutom en liten intervallet samma storlek som en minst/27 nätverk som används av några av dessa tjänster när de behöver komma åt externa resurser och/eller internet. 

### <a name="public-infrastructure-network"></a>Infrastruktur för offentliga nätverk
Detta/27 nätverk är det mycket små intervallet från Azure Stack infrastruktur undernät som tidigare nämnts, kräver inte offentliga IP-adresser, men det kräver tillgång till internet via en NAT-enhet eller en Transparent Proxy. Det här nätverket allokeras för nödfall Recovery konsolen System (ERCS), ERCS VM kräver tillgång till internet under registreringen till Azure och ska vara dirigerbara till nätverket för felsökning.

### <a name="public-vip-network"></a>Offentligt VIP-nätverk
Det offentliga VIP-nätverket har tilldelats nätverksstyrenhet i Azure-stacken. Det är inte ett logiskt nätverk på växeln. SLB använder poolen med adresser och tilldelar/32 nätverk för klienternas arbetsbelastningar. På routningstabellen växeln annonseras dessa 32 IP-adresser som en tillgänglig väg via BGP. Det här nätverket innehåller externt tillgänglig eller offentlig IP-adresser. Infrastrukturen i Azure-stacken använder minst 8 adresser från den här offentliga VIP-nätverket medan resten används av klient virtuella datorer. Nätverket storleken på det här undernätet kan variera från minst /26 (64 värdar) till maximalt /22 (1022 värdar) rekommenderar vi att du planerar för ett/24 nätverk.

### <a name="switch-infrastructure-network"></a>Växeln infrastrukturnätverk
Detta/26 nätverk är det undernät som innehåller dirigerbara point-to-point IP /30 (2 värd-IP) undernät och loopbacks vilket är dedikerade/32 undernät för in-band-växla hanterings- och BGP-router-ID. Detta intervall med IP-adresser måste vara dirigerbara externt av Azure Stack-lösningen för att ditt datacenter, kan de vara privat eller offentlig IP-adresser.

### <a name="switch-management-network"></a>Växeln hanteringsnätverk
Den här /29 (6 värd IP-adresser) nätverk är dedikerad till att ansluta hanteringsportar växlar. Den ger out-of-band-åtkomst för distribution, hantering och felsökning. Den beräknas från växeln infrastrukturnätverk som nämns ovan.

## <a name="publish-azure-stack-services"></a>Publicera Azure Stack-tjänster
Du behöver göra Azure Stack tjänster tillgängliga för användare från utanför Azure-stacken. Azure-stacken ställer in olika slutpunkter för dess infrastrukturroller. Dessa slutpunkter tilldelas VIP-adresser från den offentliga IP-adresspoolen. En DNS-post skapas för varje slutpunkt i externa DNS-zonen som angavs vid tidpunkten för distribution. Användarportalen tilldelas till exempel DNS-värdpost i portalen.  *&lt;region >.&lt; FQDN >*.

### <a name="ports-and-urls"></a>URL: er och portar
Se Azure Stack-tjänster (till exempel portaler, Azure Resource Manager, DNS, osv) tillgängliga till externa nätverk, måste du tillåta inkommande trafik till dessa slutpunkter för specifika URL-adresser, portar och protokoll.
 
I en distribution där en transparent proxy överordnad länk till en traditionell proxyserver måste du tillåta specifika portar och URL: er för utgående kommunikation. Dessa inkluderar portar och URL: er för identitet, marketplace syndikering, korrigeringsfil och uppdateringen, registrering och användningsdata.

Mer information finns i:
- [Inkommande portar och protokoll](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [Utgående portar och URL: er](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)


## <a name="next-steps"></a>Nästa steg
[Azure Stack kantlinje anslutning](azure-stack-border-connectivity.md)