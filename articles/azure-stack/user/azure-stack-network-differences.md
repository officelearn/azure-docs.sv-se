---
title: "Azure Stack nätverk: Skillnader och överväganden"
description: "Läs mer om skillnader och överväganden när du arbetar med nätverk i Azure-stacken."
services: azure-stack
keywords: 
author: ScottNapolitan
ms.author: victorh
ms.date: 9/25/2017
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 7b7bac508a759a1367ac7328840848efe17ea3c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="considerations-for-azure-stack-networking"></a>Överväganden för Azure-stacken nätverk

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Nätverk i Azure-stacken innehåller många av de funktioner som du hittar i Azure, med vissa skillnader bör du känna till innan du börjar distribuera.


Den här artikeln innehåller en översikt över unika överväganden för nätverk och dess funktioner i Azure-stacken. Mer information om övergripande skillnader mellan Azure-stacken och Azure, finns det [nyckeln överväganden](azure-stack-considerations.md) avsnittet.


## <a name="cheat-sheet-networking-differences"></a>Cheat blad: nätverk skillnader

|Tjänst | Funktion | Azure (global) | Azure Stack |
| --- | --- | --- | --- |
| DNS | Flera innehavare DNS | Stöds| Stöds inte än|
| |DNS AAAA-poster|Stöds|Stöds inte|
| |DNS-zoner per prenumeration|100 (standard)<br>Kan ökas på begäran.|100|
| |DNS-post anger per zon|5000 (standard)<br>Kan ökas på begäran.|5000|
||Namnservrar för zondelegering|Azure ange fyra namnservrar för varje användare (klient)-zon som har skapats.|Azure-stacken innehåller två namnservrar för varje användare (klient)-zon som har skapats.|
| Virtuellt nätverk|Virtuell nätverkspeering|Ansluta två virtuella nätverk i samma region via Azure stamnät nätverket.|Stöds inte än|
| |IPv6-adresser|Du kan tilldela en IPv6-adress som en del av den [konfiguration nätverksgränssnitt](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|Endast IPv4 stöds.|
|VPN-gateways|Punkt-till-plats VPN-Gateway|Stöds|Stöds inte än|
| |Vnet-till-Vnet-Gateway|Stöds|Stöds inte än|
| |VPN Gateway-SKU: er|Stöd för Basic, GW1, GW2, GW3, Standard hög prestanda, mycket hög prestanda. |Stöd för Basic, Standard och högpresterande SKU: er.|
|Belastningsutjämnare|IPv6-offentliga IP-adresser|Stöd för att tilldela en offentlig IP-adress för IPv6 till en belastningsutjämnare.|Endast IPv4 stöds.|
|Network Watcher|Nätverk Watcher klientnätverket övervakningsfunktionerna|Stöds|Stöds inte än|
|CDN|Content Delivery Network-profiler|Stöds|Stöds inte än|
|Programgateway|Layer 7 för belastningsutjämning|Stöds|Stöds inte än|
|Traffic Manager|Dirigera inkommande trafik för optimala prestanda och tillförlitlighet.|Stöds|Stöds inte än|
|Express Route|Konfigurera en snabb, privat anslutning till Microsofts molntjänster från din lokala infrastruktur eller samordning anläggning.|Stöds|Stöd för att ansluta Azure Stack till en Express Route-krets.|

## <a name="next-steps"></a>Nästa steg

[DNS i Azure Stack](azure-stack-dns.md)
