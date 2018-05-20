---
title: 'Azure Stack nätverk: Skillnader och överväganden'
description: Läs mer om skillnader och överväganden när du arbetar med nätverk i Azure-stacken.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 05/14/2018
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 2a4c5bce072970f158a89763ebdf4132eafe9cbe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="considerations-for-azure-stack-networking"></a>Överväganden för Azure-stacken nätverk

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure Stack nätverk innehåller många av funktionerna som tillhandahålls av Azure-nätverk. Det finns dock några huvudsakliga skillnader som du bör känna till innan du distribuerar ett Azure Stack-nätverk.

Den här artikeln innehåller en översikt över unika överväganden för Azure-stacken nätverks- och dess funktioner. Mer information om övergripande skillnader mellan Azure-stacken och Azure, finns det [nyckeln överväganden](azure-stack-considerations.md) avsnittet.

## <a name="cheat-sheet-networking-differences"></a>Cheat blad: nätverk skillnader

|Tjänst | Funktion | Azure (global) | Azure Stack |
| --- | --- | --- | --- |
| DNS | Flera innehavare DNS | Stöds| Stöds inte än|
| |DNS AAAA-poster|Stöds|Stöds inte|
| |DNS-zoner per prenumeration|100 (standard)<br>Kan ökas på begäran.|100|
| |DNS-post anger per zon|5000 (standard)<br>Kan ökas på begäran.|5000|
||Namnservrar för zondelegering|Azure tillhandahåller fyra namnservrar för varje zon som användaren (klient) som har skapats.|Azure-stacken innehåller två namnservrar för varje användare (klient)-zon som har skapats.|
| Virtuellt nätverk|Virtuell nätverkspeering|Ansluta två virtuella nätverk i samma region via Azure stamnät nätverket.|Stöds inte än|
| |IPv6-adresser|Du kan tilldela en IPv6-adress som en del av den [konfiguration nätverksgränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|Endast IPv4 stöds.|
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
