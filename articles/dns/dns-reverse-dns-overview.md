---
title: "Översikt över omvänd DNS i Azure | Microsoft Docs"
description: "Lär dig hur omvänd DNS fungerar och hur den kan användas i Azure"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 08f4f4aca20efad8f51ebc9ca8c6df8de8d0d4c7
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Översikt över omvänd DNS- och support i Azure

Den här artikeln ger en översikt över hur omvänd DNS fungerar och omvänd DNS-scenarier som stöds i Azure.

## <a name="what-is-reverse-dns"></a>Vad är omvänd DNS?

Vanliga DNS-poster aktiverar en mappning från en DNS-namn (t.ex 'www.contoso.com) till en IP-adress (till exempel 64.4.6.100).  Omvänd DNS gör översättningen av IP-adress (64.4.6.100) till ett namn ('www.contoso.com).

Omvänd DNS-poster används i en mängd olika situationer. Omvänd DNS-poster används ofta för att bekämpa skräppost e-post genom att verifiera avsändaren av ett e-postmeddelande.  Ta emot e-postservern hämtar omvänd DNS-posten för skicka-serverns IP-adress och kontrollerar om värden har behörighet att skicka e-post från den ursprungliga domänen. 

## <a name="how-reverse-dns-works"></a>Hur omvänd DNS fungerar

Omvänd DNS-poster finns i särskilda DNS-zoner, kallas ”ARPA-zoner.  Dessa zoner bildar en separat DNS parallellt med vanliga hierarkin värd domäner, till exempel ”contoso.com”.

Till exempel implementeras DNS-poster ”www.contoso.com” med hjälp av DNS-A-post med namnet www i zonen contoso.com.  Den här A-posten pekar på den motsvarande IP-adressen i det här fallet 64.4.6.100.  Omvänd sökning implementeras separat, med hjälp av en 'PTR-post med namnet '100' i zonen '6.4.64.in-addr.arpa' (Observera att IP-adresser återförs i ARPA-zoner.)  Den här PTR-post pekar om den har konfigurerats på rätt sätt, på namnet www.contoso.com.

När en organisation tilldelas ett IP-Adressblock måste hämta de också behörighet för att hantera motsvarande ARPA-zon. ARPA-zoner som motsvarar IP-Adressblock som används av Azure på och hanteras av Microsoft. Leverantören kan vara värd för zonen ARPA för din egen IP-adresser för dig, eller kanske kan vara värd för zonen ARPA i en DNS-tjänsten du väljer, till exempel Azure DNS.

> [!NOTE]
> Vanliga DNS-sökningar och omvänd DNS-sökning implementeras i separata, parallella DNS-hierarkier. Omvänd sökning för ”www.contoso.com” är **inte** finns i zonen ”contoso.com”, i stället den finns i zonen ARPA för motsvarande IP-adressintervall. Separata zoner används för IPv4 och IPv6-Adressblock.

### <a name="ipv4"></a>IPv4

Namnet på en IPv4 zon för omvänd sökning bör vara i följande format: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Till exempel när du skapar en zon för omvänd till värdposter för värdar med IP-adresser som finns i prefixet 192.0.2.0/24 skapas zonnamnet genom att isolera nätverksprefixet adress (192.0.2) och återföra ordning (2.0.192) och lägga till suffix `.in-addr.arpa`.

|Undernät-klass|Nätverksprefixet  |Inverterad nätverksprefixet  |Standard-suffix  |Zonnamn på för omvänd |
|-------|----------------|------------|-----------------|---------------------------|
|Klass A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Klass B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Klass C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Classless IPv4-delegering

I vissa fall kan det IP-adressintervall som allokerats till en organisation som är mindre än en klass C (/ 24) intervall. I detta fall faller inte IP-intervallet på en zon gräns inom den `.in-addr.arpa` zonen hierarki och därför kan inte delegeras som en underordnad zon.

I stället används en annan mekanism för att överföra kontroll av enskilda omvänd sökning (PTR) poster till en dedikerad DNS-zon. Den här mekanismen delegerar en underordnad zon för varje IP-adressintervall och mappar varje IP-adress i intervallet individuellt till den underordnade zonen med CNAME-poster.

Anta exempelvis att en organisation beviljas IP-intervallet 192.0.2.128/26 av dess Internetleverantör. Detta representerar 64 IP-adresser från 192.0.2.128 till 192.0.2.191. Omvänd DNS för det här intervallet implementeras på följande sätt:
- Organisationen skapar en zon för omvänd sökning som kallas 128-26.2.0.192.in-addr.arpa. Prefixet ' 128-26' representerar det nätverkssegment som tilldelats organisationen inom klass C (/ 24) intervall.
- Internetleverantören skapar NS-poster att ställa in DNS-delegering för zonen senare från den överordnade zonen klass C. Det skapar också CNAME-poster i zonen för omvänd sökning av överordnad (klass C) mappning av varje IP-adress i IP-adressintervall till den nya zonen som skapats av organisationen:

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
En omvänd sökning för IP-adressen '192.0.2.129' frågor för en PTR-post med namnet '129.2.0.192.in-addr.arpa'. Den här frågan matchas via CNAME i den överordnade zonen till PTR-post i den underordnade zonen.

### <a name="ipv6"></a>IPv6

Namnet på en zon för omvänd sökning IPv6 bör vara i följande format:`<IPv6 network prefix in reverse order>.ip6.arpa`

Till exempel. Skapa en zon för omvänd till värdposter för värdar med IP-adresser som finns i 2001:db8:1000:abdc när:: / 64 prefix zonnamnet skapas genom att isolera nätverksprefixet av adressen (2001:db8:abdc::). Expandera bredvid IPv6-prefix för nätverket att ta bort [noll komprimering](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), om den används för att korta ned IPv6-adressprefix (2001:0db8:abdc:0000::). I omvänd ordning med en period som avgränsare mellan varje hexadecimalt värde prefix, skapa inverterad nätverksprefixet (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) och lägger till suffixet `.ip6.arpa`.


|Nätverksprefixet  |Utökade och inverterad nätverksprefixet |Standard-suffix |Zonnamn på för omvänd  |
|---------|---------|---------|---------|
|2001:db8:abdc:: / 64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | . ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102:: / 64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | . ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Azure-stöd för omvänd DNS

Azure stöder två separata scenarier som gäller för omvänd DNS:

**Värd för zon för omvänd sökning som motsvarar IP-adressintervall.**
Azure DNS kan användas för att [värd zoner för omvänd sökning och hantera PTR-poster för varje omvänd DNS-sökning](dns-reverse-dns-hosting.md), för både IPv4 och IPv6.  Processen för att skapa en zon för omvänd sökning (ARPA), konfigurera delegeringen och konfigurera PTR-poster är desamma som för vanliga DNS-zoner.  Endast skillnaderna är att delegeringen måste konfigureras via din Internetleverantör i stället för DNS-registratorns och endast posttyp PTR ska användas.

**Konfigurera omvänd DNS-posten för IP-adress som tilldelats till din Azure-tjänsten.** Azure kan du [konfigurera omvänd sökning för IP-adresser som allokerats till Azure-tjänstens](dns-reverse-dns-for-azure-services.md).  Den här omvänd sökning har konfigurerats i Azure som en PTR-post i zonen motsvarande ARPA.  Dessa ARPA-zoner som motsvarar alla IP-adressintervall som används av Azure, hanteras av Microsoft

## <a name="next-steps"></a>Nästa steg

Läs mer om omvänd DNS [omvänd DNS-sökning på Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig hur du [värd zon för omvänd sökning för din ISP-tilldelad IP-adressintervall i Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Lär dig hur du [hantera omvänd DNS-posterna för din Azure-tjänster](dns-reverse-dns-for-azure-services.md).

