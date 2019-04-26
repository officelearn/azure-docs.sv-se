---
title: Översikt över omvänd DNS i Azure | Microsoft Docs
description: Lär dig hur omvänd DNS fungerar och hur den kan användas i Azure
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: 9d3a62ec1c9ede1f25f2b53f800642a792b3aa28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60192990"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Översikt över omvänd DNS- och stöd i Azure

Den här artikeln ger en översikt över hur omvänd DNS fungerar och omvänd DNS-scenarier som stöds i Azure.

## <a name="what-is-reverse-dns"></a>Vad är omvänd DNS?

Konventionell DNS-poster för att aktivera en mappning från ett DNS-namn (till exempel ”www.contoso.com”) till en IP-adress (till exempel 64.4.6.100).  Omvänd DNS kan översättningen av IP-adress (64.4.6.100) till ett namn (”www.contoso.com”).

Omvända DNS-poster används i olika situationer. Omvända DNS-poster används ofta för att bekämpa skräppost e-post genom att verifiera avsändaren av ett e-postmeddelande.  Den mottagande e-postservern hämtar omvänd DNS-posten för den avsändande serverns IP-adress och kontrollerar om värden har behörighet att skicka e-post från den ursprungliga domänen. 

## <a name="how-reverse-dns-works"></a>Hur omvänd DNS fungerar

Omvända DNS-poster finns i särskilda DNS-zoner, kallas även ”ARPA-zoner.  Zonerna utgör en separat DNS-hierarkin parallellt med den vanliga hierarkin som värd för domäner, till exempel ”contoso.com”.

Till exempel implementeras DNS-poster ”www.contoso.com” med hjälp av en DNS-”A”-post med namnet ”www” i zonen ”contoso.com”.  Den här A-post som pekar på den motsvarande IP-adressen i det här fallet 64.4.6.100.  Omvänd sökning implementeras separat, med hjälp av en ”PTR-post med namnet” 100 ”i zonen” 6.4.64.in-addr.arpa ”(Observera att IP-adresser återförs i ARPA-zoner.)  Den här PTR-post pekar om den har konfigurerats korrekt, på namn ”www.contoso.com”.

När en organisation tilldelas ett IP-Adressblock, hämta de också behörighet för att hantera motsvarande ARPA-zonen. ARPA-zoner som motsvarar IP-Adressblock som används av Azure är hyst och hanterad av Microsoft. Leverantören kan vara värd för ARPA-zonen för dina egna IP-adresser åt dig, eller kan du vara värd för ARPA-zonen i en DNS-tjänst om du föredrar, till exempel Azure DNS.

> [!NOTE]
> Vanliga DNS-sökningar och omvänd DNS-sökning är implementerade i separata, parallella DNS-hierarkier. Omvänd sökning efter ”www.contoso.com” är **inte** på zonen ”contoso.com” i stället den är värd för ARPA-zonen för motsvarande IP-Adressblock. Separata zoner används för IPv4 och IPv6-Adressblock.

### <a name="ipv4"></a>IPv4

Namnet på en IPv4 zon för omvänd sökning ska vara i formatet: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Till exempel när du skapar en omvänd zon till värdposter för värdar med IP-adresser som finns i prefixet 192.0.2.0/24 skapas zonnamnet genom att isolera nätverksprefixet av adressen (192.0.2) och sedan återställa ordning (2.0.192) och lägger till suffixet `.in-addr.arpa`.

|Undernät-klass|Nätverksprefix  |Återförda nätverksprefixet  |Standard-suffix  |Omvända zonnamnet |
|-------|----------------|------------|-----------------|---------------------------|
|Klass A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Klass B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Klass C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Classless IPv4-delegering

I vissa fall kan det IP-adressintervall som allokerats till en organisation är mindre än en klass C (/ 24) intervall. I det här fallet IP-adressintervall faller inte på en zon gräns inom den `.in-addr.arpa` zon hierarkin och därför kan inte delegeras som en underordnad zon.

I stället används en annan mekanism för att överföra kontroll över enskilda omvänd sökning (PTR) poster till en dedikerad DNS-zon. Den här mekanismen delegerar en underordnad zon för varje IP-adressintervall och mappar varje IP-adress i intervallet individuellt till den underordnade zonen med CNAME-poster.

Anta exempelvis att en organisation har beviljats IP-adressintervall 192.0.2.128/26 av dess ISP: N. Detta representerar 64 IP-adresser från 192.0.2.128 till 192.0.2.191. Omvänd DNS för det här intervallet har införts på följande sätt:
- Organisationen skapar en zon för omvänd sökning som heter 128-26.2.0.192.in-addr.arpa. Prefixet ' 128-26' representerar det nätverkssegment som tilldelats organisationen i klass C (/ 24) intervall.
- ISP: N skapar NS-poster för att ställa in DNS-delegering för zonen ovan från överordnade klass C-zonen. Det skapar också CNAME-poster i zonen för omvänd sökning på överordnade (klass C), mappa varje IP-adress i IP-adressintervall till den nya zonen som skapats av organisationen:

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
- Organisation sedan hanterar enskilda PTR-poster i sina underordnade zonen.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
En omvänd sökning för IP-adress ”192.0.2.129”-frågor för en PTR-post med namnet ”129.2.0.192.in-addr.arpa”. Den här frågan matchas via CNAME-post i den överordnade zonen till PTR-post i den underordnade zonen.

### <a name="ipv6"></a>IPv6

Namnet på en zon för omvänd sökning av IPv6 bör vara i följande format: `<IPv6 network prefix in reverse order>.ip6.arpa`

Till exempel. När skapar en omvänd zon till värdposter för värdar med IP-adresser som tillhör 2001:db8:1000:abdc:: / 64 prefix zonnamnet skapas genom att isolera nätverksprefixet av adressen (2001:db8:abdc::). Expandera därefter IPv6-prefix för nätverket att ta bort [noll komprimering](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), om den har använts för att förkorta IPv6-adressprefix (2001:0db8:abdc:0000::). Omvänd ordning, med en punkt som avgränsare mellan varje hexadecimalt värde prefix, skapa återförda nätverksprefixet (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) och lägger till suffixet `.ip6.arpa`.


|Nätverksprefix  |Utökad och återförda nätverksprefixet |Standard-suffix |Omvända zonnamnet  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Azure-stöd för omvänd DNS

Azure har stöd för två separata scenarier om omvänd DNS:

**Värdar för zonen för omvänd sökning som motsvarar IP-Adressblock.**
Azure DNS kan användas för att [värd zoner för omvänd sökning och hantera PTR-posterna för varje omvänd DNS-sökning](dns-reverse-dns-hosting.md), för både IPv4 och IPv6.  Hur du skapar en zon för omvänd sökning (ARPA), ställa in delegeringen och konfigurera PTR-poster är desamma som för vanliga DNS-zoner.  De enda skillnaderna är att delegeringen måste konfigureras via Leverantören i stället för DNS-registrator och endast posttyp PTR ska användas.

**Konfigurera omvänd DNS-posten för IP-adress som tilldelats till din Azure-tjänst.** Azure kan du [konfigurera omvänd sökning för IP-adresser som allokerats till din Azure-tjänst](dns-reverse-dns-for-azure-services.md).  Den här omvänd sökning har konfigurerats av Azure som en PTR-post i motsvarande ARPA-zonen.  Dessa ARPA-zoner som motsvarar alla IP-adressintervall som används av Azure, hos Microsoft

## <a name="next-steps"></a>Nästa steg

Läs mer om omvänd DNS [omvänd DNS-sökning på Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig hur du [värdar för zonen för omvänd sökning för din ISP-tilldelad IP-adressintervall i Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Lär dig hur du [hantera omvända DNS-poster för dina Azure-tjänster](dns-reverse-dns-for-azure-services.md).

