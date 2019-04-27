---
title: Felsökningsguide för Azure DNS | Microsoft Docs
description: Så här felsöker du vanliga problem med Azure DNS
services: dns
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
ms.assetid: 95b01dc3-ee69-4575-a259-4227131e4f9c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/20/2017
ms.author: genli
ms.openlocfilehash: 535e7604915555f32a7636b739c49f72cb0220c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60823903"
---
# <a name="azure-dns-troubleshooting-guide"></a>Felsökningsguide för Azure DNS

Den här sidan innehåller felsökningsinformation för vanliga Azure DNS-frågor.

Om de här stegen inte löser problemet kan du också kan söka efter eller publicera problemet på vår [Communitys Supportforum på MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork). Du kan också öppna en supportförfrågan för Azure.


## <a name="i-cant-create-a-dns-zone"></a>Jag kan inte skapa en DNS-zon

Prova ett eller flera av följande steg för att lösa vanliga problem:

1.  Gå igenom granskningsloggar för Azure DNS för att fastställa orsaken till felet.
2.  Varje DNS-zonnamn måste vara unikt inom sin resursgrupp. Det innebär att två DNS-zoner med samma namn inte kan dela en resursgrupp. Försök med ett annat zonnamn eller en annan resursgrupp.
3.  Ett felmeddelande om att ”Du har nått eller överskridit maxantalet zoner i prenumerationen {subscription id}” kanske visas. Då kan du antingen använda en annan Azure-prenumeration, ta bort vissa zoner eller kontakta Azure-supporten för att öka prenumerationsgränsen.
4.  Ett felmeddelande om att ”Zonen {zone name} inte är tillgänglig” kanske visas. Detta fel innebär att Azure DNS inte kunde allokera namnservrar för den här DNS-zonen. Försök med ett annat zonnamn. Om du är ägare till domännamnet kan du även kontakta Azure-supporten som kan allokera namnservrar åt dig.


### <a name="recommended-documents"></a>**Rekommenderade dokument**

[DNS-zoner och -poster](dns-zones-records.md)
<br>
[Skapa en DNS-zon](dns-getstarted-create-dnszone-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Jag kan inte skapa någon DNS-post

Prova ett eller flera av följande steg för att lösa vanliga problem:

1.  Gå igenom granskningsloggar för Azure DNS för att fastställa orsaken till felet.
2.  Finns postuppsättningen redan?  Azure DNS hanterar poster med post*uppsättningar*, vilket är en samling av poster med samma namn och av samma typ. Om det redan finns en post med samma namn och typ, redigerar du den befintliga postuppsättningen om du vill lägga till ännu en post.
3.  Försöker du skapa en post i DNS-basdomänen (zonens ”rot”)? Om så är fallet använder DNS-konventionen tecknet ”@” som postens namn. Observera också att DNS-standarden inte tillåter CNAME-poster i basdomänen.
4.  Har du en CNAME-konflikt?  DNS-standarden tillåter inte en CNAME-post med samma namn som en post av någon annan typ. Om du har ett befintligt CNAME kommer du att misslyckas om du försöker skapa en post med samma namn och av en annan typ.  På samma sätt går det inte att skapa ett CNAME om namnet matchar en befintlig post av en annan typ. Lös konflikten genom att ta bort den andra posten eller välja ett annat postnamn.
5.  Har du nått gränsen för antal postuppsättningar som tillåts i en DNS-zon? Det aktuella antalet postuppsättningar och det maximala antalet postuppsättningar visas i Azure Portal under ”Egenskaper” för zonen. Om du har uppnått gränsen kan du antingen ta bort vissa postuppsättningar eller kontakta Azure-supporten om du vill öka gränsen för den här zonen. Försök sedan igen. 


### <a name="recommended-documents"></a>**Rekommenderade dokument**

[DNS-zoner och -poster](dns-zones-records.md)
<br>
[Skapa en DNS-zon](dns-getstarted-create-dnszone-portal.md)



## <a name="i-cant-resolve-my-dns-record"></a>Jag kan inte matcha min DNS-post

DNS-namnmatchningen är en process i flera steg som kan misslyckas av flera orsaker. Följande steg hjälper dig att undersöka varför DNS-matchningen inte fungerar för en DNS-post i en zon som finns i Azure DNS.

1.  Kontrollera att DNS-posterna har konfigurerats korrekt i Azure DNS. Granska DNS-posterna i Azure Portal och kontrollera att zonnamn, postnamn samt posttyp är korrekta.
2.  Kontrollera att DNS-posterna matchas korrekt på Azure DNS-namnservrarna.
    - Om du skapar DNS-frågor från din lokala dator, kan cachelagrade resultat visas som inte motsvarar det aktuella tillståndet för namnservrarna.  Företagsnätverk använder dessutom ofta DNS-proxyservrar, vilket förhindrar att DNS-frågor dirigeras till specifika namnservrar.  Undvik dessa problem genom att använda en webbaserad namnmatchningstjänst som t.ex. [digwebinterface](https://digwebinterface.com).
    - Se till att ange rätt namnservrar för DNS-zonen, enligt det som visas i Azure Portal.
    - Kontrollera att DNS-namnet är korrekt (du måste ange det fullständiga namnet, inklusive zonnamnet) och att posttypen stämmer
3.  Kontrollera att DNS-domännamnet har [delegerats till Azure DNS-namnservrarna](dns-domain-delegation.md) på rätt sätt. Det finns [många tredjeparts webbplatser som erbjuder DNS-delegeringsverifiering](https://www.bing.com/search?q=dns+check+tool). Det här testet är ett *zon*delegeringstest, så du bör bara ange DNS-zonens namn och inte det fullständiga postnamnet.
4.  När du har utfört ovanstående bör DNS-posten matchas korrekt. Om du vill kontrollera detta använder du [digwebinterface](https://digwebinterface.com) igen, men nu med standardinställningarna för namnservern.


### <a name="recommended-documents"></a>**Rekommenderade dokument**

[Delegera en domän till Azure DNS](dns-domain-delegation.md)



## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Hur anger jag ”tjänst” och ”protokoll” för en SRV-post?

Azure DNS hanterar DNS-poster som postuppsättningar, vilket är en samling av poster med samma namn och av samma typ. För en SRV-postuppsättning måste ”tjänst” och ”protokoll” anges som en del av namnet på postuppsättningen. Andra SRV-parametrar (”prioritet”, ”vikt”, ”port” och ”mål”) anges separat för varje post i postuppsättningen.

Exempel på SRV-postnamn (tjänstnamn ”sip”, protokoll ”tcp”):

- \_sip.\_tcp (skapar en postuppsättning i basdomänen)
- \_sip.\_tcp.sipservice (skapar en postuppsättning med namnet ”sipservice”)

### <a name="recommended-documents"></a>**Rekommenderade dokument**

[DNS-zoner och -poster](dns-zones-records.md)
<br>
[Skapa DNS-postuppsättningar och poster med hjälp av Azure-portalen](dns-getstarted-create-recordset-portal.md)
<br>
[SRV-posttyp (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Azure DNS-zoner och poster](dns-zones-records.md)
* Om du vill börja använda Azure DNS, lär du dig hur du [skapa en DNS-zon](dns-getstarted-create-dnszone-portal.md) och [skapa DNS-poster](dns-getstarted-create-recordset-portal.md).
* Om du vill migrera en befintlig DNS-zon, lär du dig hur du [importera och exportera en DNS-zonfil](dns-import-export.md).

