---
title: Offentligt IPv6-adressprefix i virtuella Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig mer om offentliga IPv6-adressprefix i virtuella Azure-nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 8254a7d86d5cadc2ddc03940f4ab2d08de74bd86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965180"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>Reserverat offentligt IPv6-adressprefix (förhandsversion)

I Azure är virtuella nätverk (IPv4+IPv6) (IPv4+IPv6) och virtuella datorer som standard säkra eftersom de inte har någon Internet-anslutning. Du kan enkelt lägga till en IPv6-Internetanslutning till dina Azure Load Balancers och virtuella datorer med offentliga IPv6-adresser som du hämtar från Azure.

Alla offentliga IPs som du reserverar är associerade med en Azure-region som du väljer och med din Azure-prenumeration. Du kan flytta en reserverad (statisk) IPv6-offentlig IP mellan någon av Azure Load Balancers eller virtuella datorer i din prenumeration. Du kan ta bort IPv6 offentliga IP helt och det kommer att hållas för din användning när du är redo.

> [!WARNING]
> Var försiktig så att du inte tar bort dina offentliga IP-adresser av misstag. Om du tar bort en offentlig IP tas den bort från din prenumeration och du kan inte återställa den (inte ens med hjälp av Azure-support).

Förutom att reservera enskilda IPv6-adresser kan du reservera sammanhängande intervall för Azure IPv6-adresser (kallas IP-prefix) för din användning.  I likhet med enskilda IP-adresser associeras reserverade prefix med en Azure-region som du väljer och med din Azure-prenumeration. Reservera ett förutsägbart, sammanhängande intervall av adresser har många användningsområden. Du kan till exempel förenkla *IP-vitlistning* av dina Azure-värdprogram av ditt företag och dina kunder, eftersom dina statiska IP-intervall lätt kan programmeras in i lokala brandväggar.  Du kan skapa enskilda offentliga IP-adresser från ip-prefixet efter behov och när du tar bort dessa enskilda offentliga IP-adresser *returneras* de till ditt reserverade intervall så att du kan återanvända dem senare. Alla IP-adresser i IP-prefixet är reserverade för din exklusiva användning tills du tar bort prefixet.

> [!Important]
> IPv6 för Azure Virtual Network är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ipv6-prefix-sizes"></a>IPv6-prefixstorlekar
Följande offentliga IP-prefixstorlekar är tillgängliga:

-  Minsta IPv6-prefixstorlek: /127 = 2 adresser
-  Maximal IPv6-prefixstorlek: /124 = 16 adresser

Prefixstorlek anges som en CIDR-maskstorlek (Classless Inter-Domain Routing). En mask på /128 representerar till exempel en enskild IPv6-adress när IPv6-adresser består av 128 bitar.

## <a name="pricing"></a>Prissättning
 
Kostnader som är kopplade till att använda Offentliga Azure-IP-adresser, både enskilda IP-adresser och IP-intervall, finns i [prissättningen för offentlig IP-adress](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Begränsningar
IPv6 stöds endast på grundläggande offentliga IP-adresser med "dynamisk" allokering, vilket innebär att IPv6-adressen ändras om du tar bort och distribuerar om ditt program (VM eller belastningsutjämnare) i Azure. Standard IPv6 Public IP:s stöd endast statisk (reserverad) allokering, men standardintern belastningsutjämnare kan också stödja dynamisk allokering inifrån undernätet som de är tilldelade till.  

Vi rekommenderar att du använder offentliga standard-IPs och standardbelastningsutjämning för dina IPv6-program.

## <a name="next-steps"></a>Nästa steg
- Reservera ett offentligt [IPv6-adressprefix](ipv6-reserve-public-ip-address-prefix.md).
- Läs mer om [IPv6-adresser](ipv6-overview.md).
- Lär dig mer om hur du [skapar och använder offentliga IPs](virtual-network-public-ip-address.md) (både IPv4 och IPv6) i Azure.
