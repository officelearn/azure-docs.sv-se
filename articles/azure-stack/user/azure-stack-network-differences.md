---
title: Azure Stack nätverk skillnader och överväganden | Microsoft Docs
description: Läs mer om skillnader och överväganden när du arbetar med nätverk i Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 10/22/2018
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: de98387b0c7d5eb3c5ca99f9aa31619397e2aadf
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944587"
---
# <a name="considerations-for-azure-stack-networking"></a>Överväganden för nätverk i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack-nätverk har många av funktionerna som tillhandahålls av Azure-nätverk. Det finns dock några huvudsakliga skillnader som du bör känna till innan du distribuerar ett Azure Stack-nätverk.

Den här artikeln innehåller en översikt över unika överväganden för nätverk i Azure Stack och dess funktioner. Läs mer om övergripande skillnader mellan Azure Stack och Azure, i den [viktiga överväganden](azure-stack-considerations.md) artikeln.

## <a name="cheat-sheet-networking-differences"></a>Lathund: nätverk skillnader

| Tjänst | Funktion | Azure (global) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | DNS för flera innehavare | Stöds | Stöds inte än |
|  | AAAA DNS-poster | Stöds | Stöds inte |
|  | DNS-zoner per prenumeration | 100 (standard)<br>Kan ökas på begäran. | 100 |
|  | DNS-postuppsättningar per zon | 5000 (standard)<br>Kan ökas på begäran. | 5000 |
|  | Namnservrarna för zondelegering | Azure erbjuder fyra namnservrarna för varje användare (klient)-zon som har skapats. | Azure Stack tillhandahåller två namnservrar för varje användare (klient)-zon som har skapats. |
| Virtual Network | Virtuell nätverkspeering | Ansluta två virtuella nätverk i samma region via Azures stamnätverk. | Stöds inte än |
|  | IPv6-adresser | Du kan tilldela en IPv6-adress som en del av den [Nätverksgränssnittskonfigurationen](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Endast IPv4 stöds. |
|  | DDoS-skyddsplanen | Stöds | Stöds inte än. |
|  | Scale Sets IP-konfigurationer | Stöds | Stöds inte än. |
|  | Privat åtkomst-tjänster (undernät) | Stöds | Stöds inte än. |
|  | Serviceslutpunkter | Stöd för intern (icke-) Internetanslutning till Azure-tjänster. | Stöds inte än. |
| Endast IPv4 stöds. | Principer för tjänstslutpunkter | Stöds | Stöds inte än. |
|  | Tjänsten tunnlar | Stöds | Stöds inte än.  |
| Nätverkssäkerhetsgrupper | Förhöjda säkerhetsregler | Stöds | Stöds inte än. |
|  | Gällande säkerhetsregler | Stöds | Stöds inte än. |
|  | Programsäkerhetsgrupper | Stöds | Stöds inte än. |
| Virtuella nätverksgatewayer | Punkt-till-plats-VPN-Gateway | Stöds | Stöds inte än. |
|  | Vnet-till-Vnet-Gateway | Stöds | Stöds inte än. |
|  | Typ av virtuellt nätverksgateway | Azure stöder VPN<br> Express Route <br> Hyper Net | Azure Stack har stöd för VPN-typ just nu. |
|  | VPN-Gateway SKU: er | Stöd för Basic, GW1, GW2, GW3, Standard, höga prestanda, mycket hög prestanda. | Stöd för Basic, Standard och högpresterande SKU: er. |
|  | VPN-typ | Azure stöder både principbaserad och Vägbaserad. | Azure Stack stöder Vägbaserad endast. |
|  | BGP-inställningar | Azure stöder konfiguration av adress för BGP-Peering och Peervikt. | Adress för BGP-Peering och Peervikt är automatiskt konfigurerade i Azure Stack. Det finns inget sätt för att konfigurera de här inställningarna med egna värden. |
|  | Standard-Gateway plats | Azure stöder konfiguration av en standardplats för Tvingad tunneltrafik. | Stöds inte än. |
|  | Ändra storlek på gateway | Azure har stöd för storleksändring gatewayen efter distributionen. | Nytt storlek inte stöds. |
|  | Aktiv/aktiv-konfiguration | Stöds | Stöds inte än. |
|  | IKE/IPSec-principer | Azure har stöd för anpassade IPSec-principkonfigurationer. | Stöds inte än. |
|  | UsePolicyBasedTrafficSelectors | Azure stöder användningen av principbaserade trafikväljare med routningsbaserad gateway-anslutningar. | Stöds inte än. |
| Lastbalanserare | SKU | Grundläggande och Standard belastningsutjämnare som stöds | Endast den grundläggande belastningsutjämnaren stöds.  SKU-egenskapen stöds inte. |
|  | Zoner | Tillgänglighetszoner stöds. | Stöds inte än |
|  | Ingående NAT-regler som har stöd för tjänstslutpunkter | Azure har stöd för att ange Tjänsteslutpunkter för inkommande NAT-regler. | Azure Stack stöder ännu inte tjänstslutpunkter, så att de inte kan anges. |
|  | Protokoll | Azure har stöd för att ange GRE eller ESP. | Protokollklass stöds inte i Azure Stack. |
| Offentlig IP-adress | Offentliga IP-adressversion | Azure har stöd för både IPv6 och IPv4 | Endast IPv4 stöds. |
| Nätverksgränssnitt | Hämta effektiva routningstabellen | Stöds | Stöds inte än. |
|  | Hämta effektiva ACL: er | Stöds | Stöds inte än. |
|  | Aktivera Accelererat nätverk | Stöds | Stöds inte än. |
|  | IP-vidarebefordran | Inaktiverad som standard.  Du kan aktivera. | Växla mellan den här inställningen stöds inte.  På som standard. |
|  | Flera IP-konfigurationer per gränssnitt | Stöds | Stöds inte än. |
|  | Programsäkerhetsgrupper | Stöds | Stöds inte än. |
|  | Interna DNS-namnetikett | Stöds | Stöds inte än. |
|  | Privata IP-adressversion | Både IPv6 och IPv4 stöds. | Endast IPv4 stöds. |
|  | Primär IP-konfiguration | Stöds. Identifierar den primära IP-konfigurationen på gränssnittet. | Stöds inte än. |
| Network Watcher | Network Watcher klientnätverket funktioner för övervakning | Stöds | Stöds inte än. |
| CDN | Content Delivery Network-profiler | Stöds | Stöds inte än. |
| Programgateway | Layer 7-belastningsutjämning | Stöds | Stöds inte än. |
| Traffic Manager | Dirigera inkommande trafik för optimal programprestanda och pålitlighet. | Stöds | Stöds inte än. |
| Express Route | Konfigurera en snabb, privat anslutning till Microsofts molntjänster från din lokala infrastruktur eller delade miljö. | Stöds | Stöd för att ansluta Azure Stack till en Expressroute-krets. |

## <a name="next-steps"></a>Nästa steg

[DNS i Azure Stack](azure-stack-dns.md)