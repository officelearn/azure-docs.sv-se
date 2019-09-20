---
title: Azure DNS fel söknings guide
description: Så här felsöker du vanliga problem med Azure DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: genli
ms.openlocfilehash: 885d41c9cea11805b08b19ec9c3cc4e533813673
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162068"
---
# <a name="azure-dns-troubleshooting-guide"></a>Azure DNS fel söknings guide

Den här artikeln innehåller felsöknings information för vanliga Azure DNS frågor.

Om de här stegen inte löser problemet kan du också söka efter eller publicera ditt problem i vårt [forum för Community-support på MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). Eller så kan du öppna en support förfrågan för Azure.


## <a name="i-cant-create-a-dns-zone"></a>Det går inte att skapa en DNS-zon

Prova ett eller flera av följande steg för att lösa vanliga problem:

1.  Granska Azure DNS gransknings loggar för att fastställa orsaken till problemet.
2.  Varje DNS-zonnamn måste vara unikt inom sin resursgrupp. Det vill säga att två DNS-zoner med samma namn inte kan dela en resurs grupp. Försök med ett annat zonnamn eller en annan resursgrupp.
3.  Ett felmeddelande om att ”Du har nått eller överskridit maxantalet zoner i prenumerationen {subscription id}” kanske visas. Då kan du antingen använda en annan Azure-prenumeration, ta bort vissa zoner eller kontakta Azure-supporten för att öka prenumerationsgränsen.
4.  Ett felmeddelande om att ”Zonen {zone name} inte är tillgänglig” kanske visas. Detta fel innebär att Azure DNS inte kunde allokera namnservrar för den här DNS-zonen. Försök med ett annat zonnamn. Eller, om du är domän namns ägare kan du kontakta Azure-supporten för att allokera namnservrar åt dig.


### <a name="recommended-articles"></a>Rekommenderade artiklar

* [DNS-zoner och -poster](dns-zones-records.md)
* [Skapa en DNS-zon](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Jag kan inte skapa någon DNS-post

Prova ett eller flera av följande steg för att lösa vanliga problem:

1.  Granska Azure DNS gransknings loggar för att fastställa orsaken till problemet.
2.  Finns postuppsättningen redan?  Azure DNS hanterar poster med post*uppsättningar*, vilket är en samling av poster med samma namn och av samma typ. Om det redan finns en post med samma namn och typ, redigerar du den befintliga postuppsättningen om du vill lägga till ännu en post.
3.  Försöker du skapa en post i DNS-basdomänen (zonens ”rot”)? Om så är fallet använder DNS-konventionen tecknet ”@” som postens namn. Observera också att DNS-standarder inte tillåter CNAME-poster i zonens Apex.
4.  Har du en CNAME-konflikt?  DNS-standarderna tillåter inte en CNAME-post med samma namn som en post av någon annan typ. Om du har ett befintligt CNAME kommer du att misslyckas om du försöker skapa en post med samma namn och av en annan typ.  På samma sätt går det inte att skapa ett CNAME om namnet matchar en befintlig post av en annan typ. Lös konflikten genom att ta bort den andra posten eller välja ett annat postnamn.
5.  Har du nått gränsen för antal postuppsättningar som tillåts i en DNS-zon? Det aktuella antalet postuppsättningar och det maximala antalet postuppsättningar visas i Azure Portal under ”Egenskaper” för zonen. Om du har nått den här gränsen kan du antingen ta bort vissa post uppsättningar eller kontakta Azure-supporten för att öka din post uppsättnings gräns för den här zonen. försök sedan igen. 


### <a name="recommended-articles"></a>Rekommenderade artiklar

* [DNS-zoner och -poster](dns-zones-records.md)
* [Skapa en DNS-zon](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Jag kan inte matcha min DNS-post

DNS-namnmatchningen är en process i flera steg som kan misslyckas av flera orsaker. Följande steg hjälper dig att undersöka varför DNS-matchningen inte fungerar för en DNS-post i en zon som finns i Azure DNS.

1.  Kontrollera att DNS-posterna har konfigurerats korrekt i Azure DNS. Granska DNS-posterna i Azure Portal och kontrollera att zonnamn, postnamn samt posttyp är korrekta.
2.  Kontrollera att DNS-posterna matchas korrekt på Azure DNS-namnservrarna.
    - Om du skapar DNS-frågor från din lokala dator, kan cachelagrade resultat visas som inte motsvarar det aktuella tillståndet för namnservrarna.  Företagsnätverk använder dessutom ofta DNS-proxyservrar, vilket förhindrar att DNS-frågor dirigeras till specifika namnservrar.  Undvik dessa problem genom att använda en webbaserad namnmatchningstjänst som t.ex. [digwebinterface](https://digwebinterface.com).
    - Se till att ange rätt namnservrar för DNS-zonen, enligt det som visas i Azure Portal.
    - Kontrollera att DNS-namnet är korrekt (du måste ange det fullständiga namnet, inklusive zonnamnet) och att posttypen stämmer
3.  Kontrollera att DNS-domännamnet har [delegerats till Azure DNS-namnservrarna](dns-domain-delegation.md) på rätt sätt. Det finns [många tredjeparts webbplatser som erbjuder DNS-delegeringsverifiering](https://www.bing.com/search?q=dns+check+tool). Det här testet är ett *zon*delegeringstest, så du bör bara ange DNS-zonens namn och inte det fullständiga postnamnet.
4.  När du har utfört ovanstående bör DNS-posten matchas korrekt. Om du vill kontrollera detta använder du [digwebinterface](https://digwebinterface.com) igen, men nu med standardinställningarna för namnservern.


### <a name="recommended-articles"></a>Rekommenderade artiklar

* [Delegera en domän till Azure DNS](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Hur anger jag ”tjänst” och ”protokoll” för en SRV-post?

Azure DNS hanterar DNS-poster som postuppsättningar, vilket är en samling av poster med samma namn och av samma typ. För en SRV-postuppsättning måste ”tjänst” och ”protokoll” anges som en del av namnet på postuppsättningen. Andra SRV-parametrar (”prioritet”, ”vikt”, ”port” och ”mål”) anges separat för varje post i postuppsättningen.

Exempel på SRV-postnamn (tjänstnamn ”sip”, protokoll ”tcp”):

- \_sip.\_tcp (skapar en postuppsättning i basdomänen)
- \_sip.\_tcp.sipservice (skapar en postuppsättning med namnet ”sipservice”)

### <a name="recommended-articles"></a>Rekommenderade artiklar

* [DNS-zoner och -poster](dns-zones-records.md)
* [Skapa DNS-postuppsättningar och poster med hjälp av Azure-portalen](dns-getstarted-create-recordset-portal.md)
* [SRV-posttyp (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azure DNS zoner och poster](dns-zones-records.md)
* Om du vill börja använda Azure DNS kan du läsa om hur du [skapar en DNS-zon](dns-getstarted-create-dnszone-portal.md) och hur du [skapar DNS-poster](dns-getstarted-create-recordset-portal.md).
* Om du vill migrera en befintlig DNS-zon, lär du dig att [Importera och exportera en DNS-zonfil](dns-import-export.md).

