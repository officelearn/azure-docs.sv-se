---
title: Översikt över omvänd DNS i Azure - Azure DNS
description: I den här utbildningsvägen kan du komma igång med att lära dig hur omvänd DNS fungerar och hur den kan användas i Azure
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: bf3da62e989f0e029efdc8e9c70f5f45e0ddd765
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932300"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Översikt över omvänd DNS och support i Azure

Den här artikeln innehåller en översikt över hur omvänd DNS fungerar och de omvända DNS-scenarier som stöds i Azure.

## <a name="what-is-reverse-dns"></a>Vad är omvänd DNS?

Konventionella DNS-poster möjliggör en mappning från ett DNS-namn (till exempel "www.contoso.com") till en IP-adress (till exempel 64.4.6.100).  Omvänd DNS gör det möjligt att återföra en IP-adress (64.4.6.100) till ett namn ("www.contoso.com").

Omvända DNS-poster används i en mängd olika situationer. Omvända DNS-poster används till exempel i stor utsträckning för att bekämpa skräppost genom att verifiera avsändaren av ett e-postmeddelande.  Den mottagande e-postservern hämtar den omvända DNS-posten för den sändande serverns IP-adress och verifierar om värden har behörighet att skicka e-post från den ursprungliga domänen. 

## <a name="how-reverse-dns-works"></a>Så här fungerar omvänd DNS

Omvända DNS-poster finns i särskilda DNS-zoner, så kallade ARPA-zoner.  Dessa zoner utgör en separat DNS-hierarki parallellt med de normala hierarkivärden som är värddomäner, till exempel "contoso.com".

DNS-posten "www.contoso.com" implementeras till exempel med en DNS-A-post med namnet "www" i zonen "contoso.com".  Detta A-post pekar på motsvarande IP-adress, i det här fallet 64.4.6.100.  Den omvända sökningen implementeras separat med hjälp av en "PTR"-post med namnet "100" i zonen "6.4.64.in-addr.arpa" (observera att IP-adresser återförs i ARPA-zoner.)  Denna PTR-post, om den har konfigurerats korrekt, pekar på namnet "www.contoso.com".

När en organisation tilldelas ett IP-adressblock får de också rätt att hantera motsvarande ARPA-zon. ARPA-zonerna som motsvarar IP-adressblocken som används av Azure är värd och hanteras av Microsoft. Internet-leverantören kan vara värd för ARPA-zonen för dina egna IP-adresser åt dig, eller så kan du vara värd för ARPA-zonen i en DNS-tjänst som du väljer, till exempel Azure DNS.

> [!NOTE]
> Framåt DNS-sökning och omvända DNS-sökning implementeras i separata parallella DNS-hierarkier. Den omvända sökningen för "www.contoso.com" finns **inte** i zonen "contoso.com", utan finns i ARPA-zonen för motsvarande IP-adressblock. Separata zoner används för IPv4- och IPv6-adressblock.

### <a name="ipv4"></a>IPv4

Namnet på en omvänd uppslagszon i IPv4 `<IPv4 network prefix in reverse order>.in-addr.arpa`bör vara i följande format: .

När du till exempel skapar en omvänd zon som värd för poster för värdar med IPs som finns i prefixet 192.0.2.0/24 skapas zonnamnet genom att isolera nätverksprefixet för adressen (192.0.2) `.in-addr.arpa`och sedan återställa ordern (2.0.192) och lägga till suffixet .

|Undernätsklass|Nätverksprefix  |Omvänt nätverksprefix  |Standardsuffix  |Namn på omvänd zon |
|-------|----------------|------------|-----------------|---------------------------|
|Klass A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Klass B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Klass C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Klasslös IPv4-delegering

I vissa fall är IP-intervallet som allokerats till en organisation mindre än ett klass C-intervall (/24). I det här fallet hamnar IP-intervallet inte på `.in-addr.arpa` en zongräns inom zonhierarkin och kan därför inte delegeras som en underordnad zon.

I stället används en annan mekanism för att överföra kontroll över enskilda PTR-poster (Reverse Lookup) till en dedikerad DNS-zon. Den här mekanismen delegerar en underordnad zon för varje IP-intervall och mappar sedan varje IP-adress i intervallet individuellt till den underordnade zonen med hjälp av CNAME-poster.

Anta till exempel att en organisation beviljas IP-intervallet 192.0.2.128/26 av dess Isp. Detta motsvarar 64 IP-adresser, från 192.0.2.128 till 192.0.2.191. Omvänd DNS för det här intervallet implementeras på följande sätt:
- Organisationen skapar en omvänd uppslagszon som kallas 128-26.2.0.192.in-addr.arpa. Prefixet "128-26" representerar det nätverkssegment som tilldelats organisationen inom intervallet Klass C (/24).
- Isp skapar NS-poster för att ställa in DNS-delegering för zonen ovan från den överordnade zonen Klass C. Det skapar också CNAME-poster i den överordnade (klass C) omvänd sökning zon, mappning varje IP-adress i IP-området till den nya zonen som skapats av organisationen:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- Organisationen hanterar sedan de enskilda PTR-posterna inom sin underordnade zon.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
En omvänd sökning för IP-adressen "192.0.2.129" frågor för en PTR-post med namnet "129.2.0.192.in-addr.arpa". Den här frågan matchas via CNAME i den överordnade zonen till PTR-posten i den underordnade zonen.

### <a name="ipv6"></a>IPv6

Namnet på en omvänd uppslagszon i IPv6 ska vara i följande form:`<IPv6 network prefix in reverse order>.ip6.arpa`

Till exempel,. När du skapar en omvänd zon som värdposter för värdar med IPs som finns i prefixet 2001:db8:1000:abdc::/64 skapas zonnamnet genom att nätverksprefixet för adressen isoleras (2001:db8:abdc::). Nästa expandera IPv6-nätverksprefixet för att ta bort [nollkomprimering](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), om det användes för att förkorta IPv6-adressprefixet (2001:0db8:abdc:0000::). Återför ordningen med hjälp av en punkt som avgränsare mellan varje hexadecimalt tal i`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`prefixet, för `.ip6.arpa`att skapa det omvända nätverksprefixet ( ) och lägg till suffixet .


|Nätverksprefix  |Utökat och vänt nätverksprefix |Standardsuffix |Namn på omvänd zon  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0 c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.1.8.b.d.0.1.0.0,2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Azure-stöd för omvänd DNS

Azure stöder två separata scenarier som rör omvänd DNS:

**Värd för den omvända uppslagszonen som motsvarar ditt IP-adressblock.**
Azure DNS kan användas för att vara värd för dina zoner för [omvänd sökning och hantera PTR-posterna för varje omvänd DNS-sökning](dns-reverse-dns-hosting.md), för både IPv4 och IPv6.  Processen att skapa zonen omvänd sökning (ARPA), ställa in delegeringen och konfigurera PTR-poster är densamma som för vanliga DNS-zoner.  De enda skillnaderna är att delegeringen måste konfigureras via din Isp i stället för din DNS-registrator, och endast PTR-posttypen ska användas.

**Konfigurera den omvända DNS-posten för IP-adressen som tilldelats din Azure-tjänst.** Med Azure kan du [konfigurera omvänd sökning för IP-adresser som tilldelats din Azure-tjänst](dns-reverse-dns-for-azure-services.md).  Den här omvända sökningen konfigureras av Azure som en PTR-post i motsvarande ARPA-zon.  Dessa ARPA-zoner, som motsvarar alla IP-intervall som används av Azure, är värd för Microsoft

## <a name="next-steps"></a>Nästa steg

Mer information om omvänd DNS finns i [omvänd DNS-sökning på Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig hur du [är värd för zonen för omvänd sökning för ditt IP-intervall som tilldelats isp i Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Lär dig hur du [hanterar omvända DNS-poster för dina Azure-tjänster](dns-reverse-dns-for-azure-services.md).

