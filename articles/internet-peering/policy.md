---
title: Microsoft-peering-policy
titleSuffix: Azure
description: Microsoft-peering-policy
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775230"
---
# <a name="peering-policy"></a>Peering-policy
Microsofts allmänna krav från nätverket förklaras i avsnitten nedan. Dessa gäller både direkt peering och Exchange peering-begäranden.

## <a name="technical-requirements"></a>Tekniska krav

* Ett helt redundant nätverk med tillräcklig kapacitet för att utbyta trafik utan trafikstockningar.
* Peer kommer att ha ett offentligt dirigerbart autonomt systemnummer (ASN).
* Både IPv4 och IPv6 stöds och Microsoft förväntar sig att upprätta sessioner av båda typerna på varje peering-plats.
* MD5 stöds inte.
* **ASN-detaljer:**
    * Microsoft hanterar AS8075 tillsammans med följande ASN: AS8068, AS8069, AS12076. En fullständig lista över ASN med AS8075-peering finns i AS-SET MICROSOFT.
    * Alla parter som peering med Microsoft samtycker till att inte acceptera rutter från AS12076 (Express Route) under några omständigheter, och bör filtrera bort AS12076 på alla peer-datorer.
* **Routningsprincip:**
    * Peer kommer att ha minst en offentligt dirigerbar / 24.
    * Microsoft kommer att skriva över mottagna Multi-Exit Discriminators (MED).
    * Microsoft föredrar att ta emot BGP community-taggar från peer-datorer för att ange dirigera originering.
    * Peer förväntas registrera sina vägar i en offentlig Internet Routing Registry (IRR) databas, i syfte att filtrera, och kommer att göra goda ansträngningar för att hålla denna information uppdaterad.
    * Vi föreslår att peer-datorer anger ett maxprefix på 1000 (IPv4) och 100 (IPv6) på peering-sessioner med Microsoft.
    * Om inte specifikt överenskommits i förväg förväntas peer-datorer att meddela konsekventa rutter på alla platser där de peer med Microsoft.
    * I allmänhet kommer peering sessioner med AS8075 annonsera alla AS-MICROSOFT rutter. AS8075 sammanlänkningar i Afrika och Asien kan begränsas till linjer som är relevanta för respektive region.
    * Ingen av parterna kommer att upprätta en statisk rutt, en sista utväg eller på annat sätt skicka trafik till den andra parten för en rutt som inte meddelats via BGP.
    * Peers förväntas följa [MANRS](https://www.manrs.org/) branschstandarder för ruttsäkerhet.

## <a name="operational-requirements"></a>Operativa krav
* En fullt bemannad 24x7 Network Operations Center (NOC), som kan hjälpa till att lösa alla tekniska problem och prestandaproblem, säkerhetsöverträdelser, denial of service-attacker eller andra missbruk som kommer från peer eller deras kunder.
* Peers förväntas ha en komplett och aktuell profil på [PeeringDB](https://www.peeringdb.com) inklusive en 24x7 NOC e-post från företagets domän och telefonnummer. Vi använder denna information för att validera peer-uppgifter såsom NOC-information, teknisk kontaktinformation och deras närvaro på peering-anläggningar etc. Personliga Yahoo-, Gmail- och hotmail-konton accepteras inte.

## <a name="physical-connection-requirements"></a>Krav på fysisk anslutning
* De platser där du kan ansluta till Microsoft för direkt peering eller Exchange-peering visas i [PeeringDB](https://www.peeringdb.com/net/694)
* **Exchange peering:**
    * Sammankopplingen måste vara över enlägesfiber med lämplig 10 Gbit/s optik.
    * Peer-datorer förväntas uppgradera sina portar när topputnyttjandet överstiger 50 %.
* **Direkt peering:**
    * Sammankopplingen måste vara över enlägesfiber med lämplig 10 Gbit/s- eller 100 Gbit/s-optik.
    * Microsoft upprättar endast direkt peering med Isp- eller Network Service-leverantörer.
    * Peers förväntas uppgradera sina portar när topputnyttjandet överstiger 50% och upprätthålla olika kapacitet i varje tunnelbana, antingen inom en enda plats eller över flera platser i en tunnelbana.
    * Varje Direkt peering består av två anslutningar till två Microsoft-edge-routrar från peer-routrar som finns i Peer-gränsen. Microsoft kräver dubbla BGP-sessioner över dessa anslutningar. Peer kan välja att inte distribuera redundanta enheter i slutet.

## <a name="traffic-requirements"></a>Trafikkrav
* Peer över Exchange peering måste ha minst 200 Mb trafik och mindre än 2Gb.  För trafik som överstiger 2Gb Direct peering bör ses över.
* För direkt peering måste trafiken från nätverket till Microsoft uppfylla minimikravet nedan.

    | Geografi                      | Minsta trafik till Microsoft   |
    | :----------------------- |:-------------------------------|
    | Afrika                   | 500 Mbps                       |
    | APAC (utom Indien)      |   2 Gbit/s                       |
    | APAC (endast Indien)        | 500 Mbps                       |
    | Europa                   |   2 Gbit/s                       |
    | LATAM (LATAM)                    |   2 Gbit/s                       |
    | Mellanöstern              | 500 Mbps                       |
    | Ej tillämpligt                       |   2 Gbit/s                       |

* **Mångfald:**
    * I NA, Europa, APAC och LATAM, samtrafik på minst tre geografiskt olika platser om möjligt och upprätthålla olika kapacitet för att möjliggöra trafik till redundans inom varje tunnelbana.
    * I Afrika, Mellanöstern och Indien, samtrafik på så många olika platser som möjligt. Måste upprätthålla tillräcklig mångsidig kapacitet för att säkerställa att trafiken förblir i regionen.

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om hur du konfigurerar Direkt peering med Microsoft följer du [Direktgenomspelning](walkthrough-direct-all.md).
* Om du vill veta mer om hur du konfigurerar Exchange-peering med Microsoft följer du [exchange-peering-genomgång](walkthrough-exchange-all.md).