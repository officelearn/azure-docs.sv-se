---
title: "DNS-zoner och poster översikt – Azure DNS | Microsoft Docs"
description: "Översikt över stöd för värd för DNS-zoner och poster i Microsoft Azure DNS."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
editor: 
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: jonatul
ms.openlocfilehash: 5818986c939c464a364c52ab31225e15130ab30e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-dns-zones-and-records"></a>Översikt över DNS-zoner och poster

Den här sidan beskriver nyckelbegreppen för domäner, DNS-zoner och DNS-poster och postuppsättningar och hur de stöds i Azure DNS.

## <a name="domain-names"></a>Domännamn

Domain Name System är en hierarki av domäner. Hierarkin startar från rotdomänen, vars namn är ”**.**”.  Under detta kommer toppdomänerna, till exempel ”com”, ”net”, ”org”, ”se” eller ”uk”.  Under dessa finns domänerna på den andra nivån, till exempel ”org.se” eller ”co.uk”. Domäner i DNS-hierarkin distribueras globalt, finns på DNS-namn-servrar runtom i världen.

En domännamnsregistratorn är en organisation där du kan köpa ett domännamn, till exempel ”contoso.com”.  Köp ett domännamn kan du rätt att kontrollera DNS-hierarkin under namnet, till exempel så att du kan dirigera namnet ”www.contoso.com” till företagets webbplats. Registratorn kan vara värd för domänen i sin egen namnservrar för din räkning, eller kan du ange alternativt namn för servrar.

Azure DNS ger en serverinfrastruktur för namn på distribuerade globalt, hög tillgänglighet som du kan använda som värd för din domän. Som värd för dina domäner i Azure DNS, kan du hantera DNS-poster med samma autentiseringsuppgifter, API: er, verktyg, fakturering och stöd som andra Azure-tjänster.

Azure DNS stöder för närvarande inte köp av domännamn. Om du vill köpa ett domännamn måste du använda en tredje parts domännamnsregistratorn. Registratorn debiterar vanligtvis en liten årlig avgift. Domänerna kan finnas i Azure DNS för för hantering av DNS-poster. Se [delegera en domän till Azure DNS](dns-domain-delegation.md) mer information.

## <a name="dns-zones"></a>DNS-zoner

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS-poster

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time to live

Livslängd, eller TTL, anger hur länge varje post ska cachelagras av klienter innan den efterfrågas på nytt. I exemplet ovan är TTL 3600 sekunder eller 1 timme.

I Azure DNS har TTL-värdet angetts för postuppsättningen, inte för varje post, så samma värde används för alla poster inom den postuppsättningen.  Du kan ange ett TTL-värde mellan 1 och 2 147 483 647 sekunder.

### <a name="wildcard-records"></a>Poster med jokertecken

Azure DNS stöder [poster med jokertecken](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Jokertecken poster returneras som svar på alla frågor med ett matchande namn (om det finns en bättre matchning från en icke-jokertecken postuppsättning). Azure DNS stöder postuppsättningar med jokertecken för alla postuppsättningar utom NS och SOA.

Om du vill skapa en postuppsättning med jokertecken, använder du postuppsättningsnamnet ”\*'. Alternativt kan du också använda ett namn med ”\*'som dess vänstra etiketten, till exempel'\*.foo'.

### <a name="cname-records"></a>CNAME-poster

CNAME-postuppsättningar kan inte samexistera med andra postuppsättningar med samma namn. Du kan till exempel skapa en CNAME-postuppsättning med det relativa namnet ”www” och en A-post med det relativa namnet ”www” på samma gång.

Eftersom zonens apex (namn = ”@”) alltid innehåller NS- och SOA-postuppsättningar som skapades när zonen skapades, du kan inte skapa en CNAME-postuppsättning på zonens apex.

Dessa villkor uppkommer DNS-standarderna och är inte begränsningar i Azure DNS.

### <a name="ns-records"></a>NS-poster

NS postuppsättningen på zonens apex (namn ”@”) skapas automatiskt med varje DNS-zon och tas bort automatiskt när zonen raderas (det kan inte tas bort separat).

Den här postuppsättning innehåller namnen på Azure DNS-namnservrar tilldelas zonen. Du kan lägga till ytterligare servrar till den här NS-post inställda på en värd domäner med mer än en DNS-leverantör har stöd ett namn. Du kan också ändra TTL-värde och metadata för den här postuppsättningen. Du kan inte ta bort eller ändra de i förväg Azure DNS-namnservrarna. 

Observera att detta gäller endast NS-postuppsättning på zonens apex. Andra NS postuppsättningar i zonen (som används för att delegera underordnade zoner) kan skapas, ändras och tas bort utan begränsning.

### <a name="soa-records"></a>SOA-poster

En uppsättning för SOA-poster skapas automatiskt vid apex för varje zon (namn = ”@”), och tas bort automatiskt när zonen tas bort.  SOA-poster kan inte skapas eller tas bort separat.

Du kan ändra alla egenskaper för SOA-post förutom egenskapen 'host', som är förkonfigurerad att referera till primära namn servernamnet som tillhandahålls av Azure DNS.

### <a name="spf-records"></a>SPF-poster

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-poster

[SRV-poster](https://en.wikipedia.org/wiki/SRV_record) används av olika tjänster för att ange serverplatser. När du anger en SRV-post i Azure DNS:

* Den *service* och *protokollet* måste anges som en del av namnet på postuppsättningen prefixet understreck.  Till exempel '\_sip.\_ TCP.Name'.  För en post på zonens apex det finns ingen anledning att ange ”@” i postnamnet helt enkelt använda tjänsten och protokoll, till exempel '\_sip.\_ TCP'.
* Den *prioritet*, *vikt*, *port*, och *mål* anges som parametrar i varje post i uppsättningen av poster.

### <a name="txt-records"></a>TXT-poster

TXT-poster används för att mappa domännamn till valfri textsträngar. De används i flera program, särskilt relaterade till e-konfiguration som den [avsändaren princip Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) och [DomainKeys identifieras e-post (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

DNS-standarden tillåter en enskild TXT-post som innehåller flera strängar, som kan vara upp till 254 tecken. När flera strängar används kan de användas i sammanslagningen av klienter och behandlas som en sträng.

Du måste ange varje TXT-sträng separat när du anropar Azure DNS-REST API.  När du använder Azure-portalen, PowerShell eller CLI gränssnitt bör du ange en enskild textsträng per post som automatiskt är uppdelad i 254 tecken segment om det behövs.

Flera strängar i en DNS-post ska inte förväxlas med flera TXT-poster i en TXT-postuppsättning.  En TXT-postuppsättning kan innehålla flera poster, *som* kan innehålla flera strängar.  Azure DNS stöder en totala stränglängd på högst 1024 tecken i varje TXT-postuppsättning (för alla poster som kombineras).

## <a name="tags-and-metadata"></a>Taggar och metadata

### <a name="tags"></a>Taggar

Taggar är en lista över namn / värde-par och används av Azure Resource Manager etikett resurser.  Azure Resource Manager använder taggar för att aktivera filtrerade datavyer fakturan Azure och kan du skapa en princip som taggar krävs också. Mer information om taggar finns i [Ordna dina Azure-resurser med hjälp av taggar](../azure-resource-manager/resource-group-using-tags.md).

Azure DNS stöder med hjälp av Azure Resource Manager taggar på DNS-zonen resurser.  Det stöder inte taggar på DNS-postuppsättningar, även om som ett alternativ 'metadata' stöds på DNS-post anger som beskrivs nedan.

### <a name="metadata"></a>Metadata

Azure DNS stöder kommentera postuppsättningar med 'metadata' som ett alternativ till postuppsättning taggar.  Liknar taggar, metadata kan du associera namn / värde-par med varje uppsättning av poster.  Detta kan vara användbart, till exempel till post syftet med varje uppsättning av poster.  Till skillnad från taggar, metadata kan inte användas för att tillhandahålla en filtrerad vy av fakturan Azure och kan inte anges i en Azure Resource Manager-princip.

## <a name="etags"></a>Etags

Anta att två personer eller två processer försök att ändra en DNS-post på samma gång. Vilken wins? Och vinnaren känner att de har över ändringar som skapats av någon annan?

Azure DNS använder Etags för att hantera samtidiga ändringar till samma resurs på ett säkert sätt. Etags skiljer sig från [Azure Resource Manager ”-taggar'](#tags). Varje DNS-resursposter (zonen eller postuppsättning) har en Etag som är kopplade till den. När en resurs hämtas, hämtas även dess Etag. När du uppdaterar en resurs, kan du välja att skicka tillbaka Etag så att Azure DNS kan kontrollera som Etag på servern matchar. Eftersom varje uppdatering till en resurs resulterar i Etag återskapas, anger ett Etag-Typfel samtidiga har ändrats. Etags kan också användas när du skapar en ny resurs att se till att resursen inte finns redan.

Som standard använder Azure DNS-PowerShell Etags blockera samtidiga ändringar till zoner och postuppsättningar. Det valfria *-skriva över* växel kan användas för att undertrycka Etag-kontroller, i vilket fall alla samtidiga ändringarna som skett skrivs över.

På nivån av Azure DNS-REST API anges Etags med HTTP-huvuden.  Deras beteende ges i följande tabell:

| Huvudet | Beteende |
| --- | --- |
| Ingen |PLACERA lyckas alltid (inga Etag-kontroller) |
| IF-match<etag> |PLACERA lyckas bara om resursen finns och Etag matchar |
| IF-match * |PLACERA lyckas bara om resursen finns |
| IF-none-match * |PLACERA lyckas bara om resursen inte finns |


## <a name="limits"></a>Begränsningar

Följande standard begränsningar gäller när du använder Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Nästa steg

* Om du vill börja använda Azure DNS, lär du dig hur du [skapa en DNS-zon](dns-getstarted-create-dnszone-portal.md) och [skapa DNS-poster](dns-getstarted-create-recordset-portal.md).
* Om du vill migrera en befintlig DNS-zon, lär du dig hur du [importera och exportera en DNS-zonfilen](dns-import-export.md).
