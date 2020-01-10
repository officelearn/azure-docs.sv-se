---
title: Microsofts peering-princip
titleSuffix: Azure
description: Microsofts peering-princip
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775230"
---
# <a name="peering-policy"></a>Peering-princip
Microsofts allmänna krav från nätverket beskrivs i avsnitten nedan. Dessa gäller både direkt peering-och Exchange peering-begäranden.

## <a name="technical-requirements"></a>Tekniska krav

* Ett fullständigt redundant nätverk med tillräcklig kapacitet för att utväxla trafik utan överbelastning.
* Peer kommer att ha ett offentligt dirigerbart autonomt system nummer (ASN).
* Både IPv4 och IPv6 stöds och Microsoft förväntar sig att upprätta sessioner av båda typerna på varje peering-plats.
* MD5 stöds inte.
* **ASN-information:**
    * Microsoft hanterar AS8075 tillsammans med följande ASN: er: AS8068, AS8069, AS12076. En fullständig lista över ASN: er med AS8075-peering, referens som-SET.
    * Alla parter som är peering med Microsoft accepterar att inte acceptera vägar från AS12076 (Express Route) under några omständigheter och bör filtrera ut AS12076 på alla peer-datorer.
* **Princip för Routning:**
    * Motparten kommer att ha minst ett offentligt dirigerbart/24.
    * Microsoft kommer att skriva över mottagna Diskriminatorer för flera avslutningar (med).
    * Microsoft föredrar att ta emot BGP community-taggar från peer-datorer för att indikera väg ursprung.
    * Peer förväntas registrera sina vägar i en IR-databas (Public Internet routing Registry), i syfte att filtrera, och kommer att göra en bättre tro-ansträngningar för att hålla den här informationen aktuell.
    * Vi föreslår att motparter ställer in ett max-prefix på 1000 (IPv4) och 100 (IPv6) vägar på peering-sessioner med Microsoft.
    * Om det inte uttryckligen överenskommits på förhand förväntas motparter att tillkännage konsekventa vägar på alla platser där de är peer-kopplade med Microsoft.
    * I allmänhet kommer peering-sessioner med AS8075 att annonsera alla som-MICROSOFT-vägar. AS8075-kopplingar i Afrika och Asien kan begränsas till vägar som är relevanta för respektive region.
    * Ingen av parterna kommer att upprätta en statisk väg, en väg till den sista utväg eller på annat sätt skicka trafik till den andra parten för en väg som inte annonseras via BGP.
    * Peer-datorer förväntas följa [MANRS](https://www.manrs.org/) bransch standarder för väg säkerhet.

## <a name="operational-requirements"></a>Drift krav
* En fullständigt bemannad nätverks åtgärds Center (NOC), som kan hjälpa till med lösning på alla tekniska och prestanda problem, säkerhets överträdelser, Denial of Service-attacker eller andra missbruk med ursprung i motparten eller deras kunder.
* Peer-datorer förväntas ha en fullständig och uppdaterad profil på [PeeringDB](https://www.peeringdb.com) , till exempel ett e-postmeddelande dygnet runt från företags domänen och telefonnumret. Vi använder den här informationen för att verifiera motpartens information, till exempel NOC information, teknisk kontakt information och deras närvaro vid peering-anläggningarna osv. Personliga Yahoo-, Gmail-och Hotmail-konton godkänns inte.

## <a name="physical-connection-requirements"></a>Krav för fysisk anslutning
* De platser där du kan ansluta till Microsoft för direkt peering eller Exchange-peering finns i [PeeringDB](https://www.peeringdb.com/net/694)
* **Exchange-peering:**
    * Samtrafik måste vara över en fiber fiber med lämpliga 10Gbps-optik.
    * Peer-datorer förväntas uppgradera sina portar när högsta användning överskrider 50%.
* **Direkt peering:**
    * Samtrafik måste vara över en fiber fiber med lämpliga 10Gbps eller 100Gbps optik.
    * Microsoft upprättar endast direkt peering med ISP-eller nätverks tjänst leverantörer.
    * Peer-datorer förväntas uppgradera sina portar när den högsta belastningen överskrider 50% och har olika kapacitet i varje tunnelbane linje, antingen inom en enda plats eller på flera platser i en tunnelbane linje.
    * Varje direkt peering består av två anslutningar till två Microsoft Edge-routrar från peer-routrar som finns i peer-gränsen. Microsoft kräver dubbla BGP-sessioner över dessa anslutningar. Motparten kan välja att inte distribuera redundanta enheter i slutet.

## <a name="traffic-requirements"></a>Trafik krav
* Peer-datorer via Exchange-peering måste ha minst 200 MB trafik och mindre än 2 GB.  För trafik som överstiger 2 GB Direct-peering bör granskas.
* Trafik från ditt nätverk till Microsoft måste uppfylla minimi kraven för direkt peering.

    | Geografi                      | Lägsta trafik till Microsoft   |
    | :----------------------- |:-------------------------------|
    | Afrika                   | 500 Mbps                       |
    | APAC (utom Indien)      |   2 Gbit/s                       |
    | APAC (endast Indien)        | 500 Mbps                       |
    | Europa                   |   2 Gbit/s                       |
    | LATAM                    |   2 Gbit/s                       |
    | Mellanöstern              | 500 Mbps                       |
    | Ej tillämpligt                       |   2 Gbit/s                       |

* **Spridning**
    * I NA, Europa, APAC och LATAM, sammankopplar i minst tre geografiskt skilda platser om möjligt och upprätthåller varierande kapacitet för att tillåta redundans i varje tunnelbane linje.
    * I Afrika, Mellanöstern och Indien, sammankopplar du så många olika platser som möjligt. Måste ha tillräckligt med kapacitet för att säkerställa att trafiken förblir i regionen.

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om hur du konfigurerar direkt peering med Microsoft följer du [genom gången av direkt peering](walkthrough-direct-all.md).
* Om du vill veta mer om hur du konfigurerar Exchange-peering med Microsoft följer du [genom gången av Exchange-peering](walkthrough-exchange-all.md).