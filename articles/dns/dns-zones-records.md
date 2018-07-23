---
title: DNS-zoner och poster översikt – Azure DNS | Microsoft Docs
description: Översikt över stöd för värd för DNS-zoner och poster i Microsoft Azure DNS.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: victorh
ms.openlocfilehash: 7f69d77ac7a6c2a17ef2568f0c7edaef2e1ee3f5
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39174418"
---
# <a name="overview-of-dns-zones-and-records"></a>Översikt över DNS-zoner och poster

Den här sidan förklaras nyckelbegreppen för domäner, DNS-zoner och DNS-poster och postuppsättningar och hur de stöds i Azure DNS.

## <a name="domain-names"></a>Domännamn

Domain Name System är en hierarki av domäner. Hierarkin startar från rotdomänen, vars namn är ”**.**”.  Under detta kommer toppdomänerna, till exempel ”com”, ”net”, ”org”, ”se” eller ”uk”.  Under dessa finns domänerna på den andra nivån, till exempel ”org.se” eller ”co.uk”. Domänerna i DNS-hierarkin distribueras globalt, värd för DNS-namnservrar runtom i världen.

En domänregistrator är en organisation där du kan köpa ett domännamn, till exempel ”contoso.com”.  Köp ett domännamn ger dig rätt att kontrollera DNS-hierarkin med det, till exempel så att du kan dirigera namnet ”www.contoso.com” till företagets webbplats. Registratorn kan vara värd för domänen i sin egen namnservrarna för din räkning, eller kan du ange alternativa DNS-servrar.

Azure DNS tillhandahåller en globalt distribuerad, hög tillgänglighet namn serverinfrastruktur, som du kan använda som värd för din domän. Av som värd för domäner i Azure DNS kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API: er, verktyg, fakturering och support som dina andra Azure-tjänster.

Azure DNS stöder för närvarande inte köpa domännamn. Om du vill köpa ett domännamn kan behöva du använda en tredjeparts-domännamnsregistrator. Registratorn oftast debiterar en liten årsavgift. Domänerna som kan finnas i Azure DNS för hantering av DNS-poster. Se [delegera en domän till Azure DNS](dns-domain-delegation.md) mer information.

## <a name="dns-zones"></a>DNS-zoner

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS-poster

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time to live

TTL-värde, eller TTL, anger du hur länge varje post ska cachelagras av klienter innan som requeried. I exemplet ovan är TTL 3600 sekunder eller 1 timme.

I Azure DNS och har TTL-värdet angetts för postuppsättningen, inte för varje post, så samma värde används för alla poster inom den postuppsättningen.  Du kan ange ett TTL-värde mellan 1 och 2 147 483 647 sekunder.

### <a name="wildcard-records"></a>Poster med jokertecken

Azure DNS stöder [poster med jokertecken](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Poster med jokertecken returneras som svar på alla frågor med ett matchande namn (om det inte finns en bättre matchning från en postuppsättning utan jokertecken). Azure DNS stöder postuppsättningar med jokertecken för alla postuppsättningar utom NS och SOA.

Om du vill skapa en postuppsättning med jokertecken, använder du postuppsättningsnamnet ”\*”. Alternativt kan du också använda ett namn med ”\*” som dess vänstra etiketten, till exempel ”\*.foo”.

### <a name="caa-records"></a>CAA-poster

CAA-poster att domänägare kan ange vilken certifikatutfärdare (CA) har behörighet att utfärda certifikat för sin domän. På så sätt kan certifikatutfärdare för att förhindra att fel certifikat i vissa fall. CAA-poster har tre egenskaper:
* **Flaggor**: det här är ett heltal mellan 0 och 255 som används för att representera kritisk flagga som har en särskild innebörd per den [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Taggen**: en ASCII-sträng som kan vara något av följande:
    * **problemet**: Använd det här om du vill ange CA: er som tillåts att utfärda certifikat (alla typer)
    * **issuewild**: Använd det här om du vill ange CA: er som tillåts att utfärda certifikat (endast certifikat med jokertecken)
    * **iodef**: Ange en e-postadress eller ett värdnamn som CA: er kan meddela för obehörig cert problemet begäranden
* **Värdet**: värdet för den specifika taggen valt

### <a name="cname-records"></a>CNAME-poster

CNAME-postuppsättningar kan inte samexistera med andra postuppsättningar med samma namn. Du kan till exempel skapa en CNAME-postuppsättning med det relativa namnet ”www” och en A-post med det relativa namnet ”www” på samma gång.

Eftersom zonens apex (namn = '\@') alltid innehåller NS- och SOA-postuppsättningar som skapades när zonen skapades, du kan inte skapa en CNAME-postuppsättning på zonens apex.

Dessa begränsningar uppstår från DNS-standarden och är inte begränsningar i Azure DNS.

### <a name="ns-records"></a>NS-poster

NS-postuppsättning på zonens apex (namn '\@”) skapas automatiskt med varje DNS-zon och tas bort automatiskt när zonen tas bort (det kan inte tas bort separat).

Den här uppsättningen av poster innehåller namnen på Azure DNS-namnservrarna som tilldelats i zonen. Du kan lägga till ytterligare servrar till den här NS-post anger för att stödja delad hosting domäner med fler än en DNS-leverantör ett namn. Du kan också ändra TTL-värde och metadata för den här uppsättningen av poster. Du kan inte ta bort eller ändra förifyllda Azure DNS-namnservrarna. 

Detta gäller endast för NS-postuppsättning på zonens apex. Andra NS postuppsättningar i din zon (som används för att delegera underordnade zoner) kan skapas, ändras och tas bort utan begränsning.

### <a name="soa-records"></a>SOA-poster

En SOA-postuppsättning skapas automatiskt överst i varje zon (namn = '\@'), och tas bort automatiskt när zonen tas bort.  SOA-poster kan inte skapas eller tas bort separat.

Du kan ändra alla egenskaper för SOA-posten förutom 'host'-egenskapen, som är förkonfigurerad att referera till primärt namn-servernamnet som tillhandahålls av Azure DNS.

### <a name="spf-records"></a>SPF-poster

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-poster

[SRV-poster](https://en.wikipedia.org/wiki/SRV_record) används av olika tjänster för att ange serverplatser. När du anger en SRV-post i Azure DNS:

* Den *service* och *protokollet* måste anges som en del av namnet på postuppsättningen föregås av understreck.  Till exempel ”\_sip.\_ TCP.Name'.  För en post i basdomänen finns behöver inte ange '\@”i postnamnet, helt enkelt använda tjänst och protokoll, till exempel”\_sip.\_ TCP ”.
* Den *prioritet*, *vikt*, *port*, och *target* anges som parametrar för varje post i postuppsättningen.

### <a name="txt-records"></a>TXT-poster

TXT-poster används för att mappa domännamn till godtyckliga textsträngar. De används i flera program, särskilt relaterade till e-postkonfigurationen, exempelvis den [avsändaren princip Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) och [DomainKeys identifieras e-post (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

DNS-standarden tillåter en TXT-post som innehåller flera strängar, som kan bestå av högst 254 tecken. Om flera strängar används de sammanfogas av klienter och behandlas som en sträng.

När du anropar REST-API i Azure DNS måste du ange varje TXT-sträng separat.  När du använder Azure portal, PowerShell eller CLI gränssnitt bör du ange en sträng per post som automatiskt är uppdelad i 254 tecken segment om det behövs.

Flera strängar i en DNS-post ska inte förväxlas med flera TXT-poster i en TXT-postuppsättning.  En TXT-postuppsättning kan innehålla flera poster, *som* kan innehålla flera strängar.  Azure DNS stöder en totala stränglängd på högst 1024 tecken i varje TXT-postuppsättning (över alla poster).

## <a name="tags-and-metadata"></a>Taggar och metadata

### <a name="tags"></a>Taggar

Taggar är en lista över namn / värde-par och används av Azure Resource Manager för att märka resurser.  Azure Resource Manager använder taggar för att aktivera Azure-fakturan filtrerade datavyer och även möjligt att ställa in en princip som taggar krävs. Mer information om taggar finns i [Ordna dina Azure-resurser med hjälp av taggar](../azure-resource-manager/resource-group-using-tags.md).

Azure DNS stöder med hjälp av Azure Resource Manager-taggar på DNS-zon resurser.  Det stöder inte taggar på DNS-postuppsättningar, även om som ett alternativ ”metadata” stöds på DNS-postuppsättningar som beskrivs nedan.

### <a name="metadata"></a>Metadata

Som ett alternativ till uppsättning av poster taggar Azure DNS stöder kommentera postuppsättningar med ”metadata”.  Liknar taggar, metadata kan du associera namn / värde-par med varje uppsättning av poster.  Detta kan vara användbart, till exempel till post syftet med varje uppsättning av poster.  Till skillnad från taggar, metadata kan inte användas för att tillhandahålla en filtrerad vy av Azure-fakturan och kan inte anges i en Azure Resource Manager-princip.

## <a name="etags"></a>Etags

Anta att två personer eller två processer du försöker ändra en DNS-post på samma gång. Vilken wins? Och vinnaren känner att de har över ändringar som skapats av någon annan?

Azure DNS använder Etags för att hantera samtidiga ändringar till samma resurs på ett säkert sätt. Etags skiljer sig från [Azure Resource Manager ”-taggar'](#tags). Varje DNS-resurs (zon eller uppsättning av poster) har en Etag som är kopplade till den. Varje gång en resurs hämtas, hämtas även dess Etag. När du uppdaterar en resurs måste välja du att skicka tillbaka Etag så att Azure DNS kan kontrollera som Etag på server-matchningar. Eftersom varje uppdatering till en resurs resulterar i Etag återskapas, anger ett Etag-Typfel samtidiga har ändrats. Etags kan också användas när du skapar en ny resurs att se till att resursen inte finns redan.

Som standard använder Azure DNS PowerShell Etags för att blockera samtidiga ändringar till zoner och uppsättningar av poster. Den valfria *-Skriv över* växel kan användas för att undertrycka Etag kontroller, i så fall eventuella samtidiga ändringar som har skett skrivs över.

På Azure DNS REST API-nivå anges Etags med HTTP-huvuden.  Deras beteende ges i följande tabell:

| Sidhuvud | Beteende |
| --- | --- |
| Ingen |PUT lyckas alltid (inga Etag-kontroller) |
| IF-match <etag> |PUT lyckas bara om resursen finns och Etag matchar |
| IF-match * |PUT lyckas bara om resursen finns |
| IF-none-match * |PUT lyckas bara om resursen inte finns |


## <a name="limits"></a>Begränsningar

Följande standard begränsningar gäller när du använder Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Nästa steg

* Om du vill börja använda Azure DNS, lär du dig hur du [skapa en DNS-zon](dns-getstarted-create-dnszone-portal.md) och [skapa DNS-poster](dns-getstarted-create-recordset-portal.md).
* Om du vill migrera en befintlig DNS-zon, lär du dig hur du [importera och exportera en DNS-zonfil](dns-import-export.md).
