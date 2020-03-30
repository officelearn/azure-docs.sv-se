---
title: Översikt över DNS-zoner och poster - Azure DNS
description: Översikt över stöd för att vara värd för DNS-zoner och poster i Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 19189af6424960b8e20be686af745b10f2d8578b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265160"
---
# <a name="overview-of-dns-zones-and-records"></a>Översikt över DNS-zoner och poster

På den här sidan beskrivs de viktigaste begreppen för domäner, DNS-zoner och DNS-poster och postuppsättningar och hur de stöds i Azure DNS.

## <a name="domain-names"></a>Domännamn

Domain Name System är en hierarki av domäner. Hierarkin startar från rotdomänen, vars namn är ”**.**”.  Under detta kommer toppdomänerna, till exempel ”com”, ”net”, ”org”, ”se” eller ”uk”.  Under dessa finns domänerna på den andra nivån, till exempel ”org.se” eller ”co.uk”. Domänerna i DNS-hierarkin distribueras globalt och hanteras av DNS-namnservrar runt om i världen.

En domännamnsregistrare är en organisation som låter dig `contoso.com`köpa ett domännamn, till exempel .  När du köper ett domännamn kan du styra DNS-hierarkin under det `www.contoso.com` namnet, till exempel så att du kan dirigera namnet till företagets webbplats. Registratorn kan vara värd för domänen i sina egna namnservrar för din räkning, eller låta dig ange alternativa namnservrar.

Azure DNS tillhandahåller en globalt distribuerad serverinfrastruktur för namnserver med hög tillgänglighet som du kan använda för att vara värd för din domän. Genom att vara värd för dina domäner i Azure DNS kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg, fakturering och support som dina andra Azure-tjänster.

Azure DNS stöder för närvarande inte inköp av domännamn. Om du vill köpa ett domännamn måste du använda en domännamnsregistrer från tredje part. Registratorn tar vanligtvis ut en liten årsavgift. Domänerna kan sedan finnas i Azure DNS för hantering av DNS-poster. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).

## <a name="dns-zones"></a>DNS-zoner

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS-poster

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Tid att leva

Tiden att leva, eller TTL, anger hur länge varje post cachelagras av klienter innan den omklädes. I exemplet ovan är TTL 3600 sekunder eller 1 timme.

I Azure DNS anges TTL för postuppsättningen, inte för varje post, så samma värde används för alla poster i den postuppsättningen.  Du kan ange ett TTL-värde mellan 1 och 2 147 483 647 sekunder.

### <a name="wildcard-records"></a>Jokertecken poster

Azure DNS stöder [poster med jokertecken](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Jokerteckenposter returneras som svar på en fråga med ett matchande namn (såvida det inte finns en närmare matchning från en postuppsättning som inte jokertecken). Azure DNS stöder jokerteckenpostuppsättningar för alla posttyper utom NS och SOA.

Om du vill skapa en jokerteckenpostuppsättning\*använder du postuppsättningen ' '. Du kan också använda ett namn\*med ' ' som dess etikett\*som vänster, till exempel '.foo'.

### <a name="caa-records"></a>CAA-poster

CAA-poster gör det möjligt för domänägare att ange vilka certifikatutfärdare som har behörighet att utfärda certifikat för sin domän. Detta gör det möjligt för certifikatutfärdare att undvika att certifikat utfärdas på ett felaktigt sätt under vissa omständigheter. CAA-poster har tre egenskaper:
* **Flaggor**: Detta är ett heltal mellan 0 och 255, som används för att representera den kritiska flaggan som har särskild betydelse enligt [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag**: en ASCII-sträng som kan vara något av följande:
    * **problem:** använd detta om du vill ange certifikatutfärdare som får utfärda certifikat (alla typer)
    * **problemwild**: använd detta om du vill ange certifikatutfärdare som tillåts utfärda certifikat (endast jokerteckencertifikat)
    * **iodef**: ange en e-postadress eller värdnamn som certifikatutfärdare kan anmäla för obehöriga cert-utfärdandebegäranden
* **Värde**: värdet för den valda taggen

### <a name="cname-records"></a>CNAME-poster

CNAME-postuppsättningar kan inte samexistera med andra postuppsättningar med samma namn. Du kan till exempel inte skapa en CNAME-postuppsättning med det relativa namnet "www" och en A-post med det relativa namnet "www" samtidigt.

Eftersom zonapex (namn\@= ' ') alltid innehåller NS- och SOA-postuppsättningarna som skapades när zonen skapades, kan du inte skapa en CNAME-post som angetts vid zonsponsan.

Dessa begränsningar uppstår från DNS-standarderna och är inte begränsningar för Azure DNS.

### <a name="ns-records"></a>NS-poster

NS-posten som anges vid zonapexen (namnet '\@') skapas automatiskt med varje DNS-zon och tas bort automatiskt när zonen tas bort (den kan inte tas bort separat).

Den här postuppsättningen innehåller namnen på Azure DNS-namnservrar som tilldelats zonen. Du kan lägga till ytterligare namnservrar i den här NS-postuppsättningen för att stödja samhostingdomäner med mer än en DNS-provider. Du kan också ändra TTL och metadata för den här postuppsättningen. Du kan dock inte ta bort eller ändra de förifyllda Azure DNS-namnservrarna. 

Detta gäller endast NS-posten som ställts in vid zonsponsen. Andra NS-postuppsättningar i zonen (som används för att delegera underordnade zoner) kan skapas, ändras och tas bort utan begränsning.

### <a name="soa-records"></a>SOA-poster

En SOA-postuppsättning skapas automatiskt i toppen för varje\@zon (namn = ' '), och tas bort automatiskt när zonen tas bort.  SOA-poster kan inte skapas eller tas bort separat.

Du kan ändra alla egenskaper för SOA-posten förutom egenskapen host, som är förkonfigurerad för att referera till det primära namnservernamnet som tillhandahålls av Azure DNS.

Zonserienumret i SOA-posten uppdateras inte automatiskt när ändringar görs i posterna i zonen. Den kan uppdateras manuellt genom att redigera SOA-posten om det behövs.

### <a name="spf-records"></a>SPF-poster

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-poster

[SRV-poster](https://en.wikipedia.org/wiki/SRV_record) används av olika tjänster för att ange serverplatser. När du anger en SRV-post i Azure DNS:

* *Tjänsten* och *protokollet* måste anges som en del av postuppsättningens namn, som föregås av understreck.  Till exempel\_' sip. \_tcp.name".  För en post i zonapexen behöver du\@inte ange ' ' i postnamnet, använd\_bara tjänsten och protokollet, till exempel ' sip. \_tcp".
* *Prioritet,* *vikt,* *port*och *mål* anges som parametrar för varje post i postuppsättningen.

### <a name="txt-records"></a>TXT-poster

TXT-poster används för att mappa domännamn till godtyckliga textsträngar. De används i flera program, särskilt relaterade till e-postkonfiguration, till exempel [Sender Policy Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) och [DomainKeys Identified Mail (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

DNS-standarderna tillåter att en enda TXT-post innehåller flera strängar, som var och en kan vara upp till 254 tecken långa. Om flera strängar används sammanfogas de av klienter och behandlas som en enda sträng.

När du anropar Azure DNS REST API måste du ange varje TXT-sträng separat.  När du använder Azure-portalen, PowerShell- eller CLI-gränssnitten bör du ange en enda sträng per post, som automatiskt delas in i 254-teckenssegment om det behövs.

Flera strängar i en DNS-post bör inte förväxlas med flera TXT-poster i en TXT-postuppsättning.  En TXT-postuppsättning kan innehålla flera *poster, som var och en* kan innehålla flera strängar.  Azure DNS stöder en total stränglängd på upp till 1024 tecken i varje TXT-postuppsättning (för alla poster tillsammans).

## <a name="tags-and-metadata"></a>Taggar och metadata

### <a name="tags"></a>Taggar

Taggar är en lista över namn-värde-par och används av Azure Resource Manager för att märka resurser.  Azure Resource Manager använder taggar för att aktivera filtrerade vyer av din Azure-faktura och gör det också möjligt att ange en princip som taggar krävs på. Mer information om taggar finns i [Ordna dina Azure-resurser med hjälp av taggar](../azure-resource-manager/management/tag-resources.md).

Azure DNS stöder användning av Azure Resource Manager-taggar på DNS-zonresurser.  Det stöder inte taggar på DNS-postuppsättningar, men som en alternativ "metadata" stöds på DNS-postuppsättningar som förklaras nedan.

### <a name="metadata"></a>Metadata

Som ett alternativ till postuppsättningstaggar stöder Azure DNS kommenterande postuppsättningar med hjälp av metadata.  På samma sätt som taggar kan du associera namnvärdespar med varje postuppsättning.  Detta kan vara användbart, till exempel för att registrera syftet med varje postuppsättning.  Till skillnad från taggar kan metadata inte användas för att tillhandahålla en filtrerad vy av din Azure-faktura och kan inte anges i en Azure Resource Manager-princip.

## <a name="etags"></a>Etags

Anta att två personer eller två processer försöker ändra en DNS-post samtidigt. Vilken vinner? Och vet vinnaren att de har skrivit över ändringar som skapats av någon annan?

Azure DNS använder Etags för att hantera samtidiga ändringar av samma resurs på ett säkert sätt. Etags är separata från [Azure Resource Manager 'Tags'](#tags). Varje DNS-resurs (zon eller postuppsättning) har en Etag associerad med den. När en resurs hämtas hämtas även dess Etag. När du uppdaterar en resurs kan du välja att skicka tillbaka Etag så att Azure DNS kan verifiera att Etag på servern matchar. Eftersom varje uppdatering av en resurs resulterar i att Etag återskapas, anger en Etag-felmatchning en samtidig ändring har inträffat. Etags kan också användas när du skapar en ny resurs för att säkerställa att resursen inte redan finns.

Som standard använder Azure DNS PowerShell Etags för att blockera samtidiga ändringar i zoner och postuppsättningar. Den valfria *växeln -Skriv över* kan användas för att undertrycka Etag-kontroller, i vilket fall eventuella samtidiga ändringar som har inträffat skrivs över.

På nivån för Azure DNS REST API anges Etags med HTTP-huvuden.  Deras beteende anges i följande tabell:

| Huvud | Beteende |
| --- | --- |
| Inget |PUT lyckas alltid (inga Etag kontroller) |
| Om-match \<etag> |PUT lyckas bara om det finns resurser och Etag matchar |
| Om-matchning * |PUT lyckas bara om det finns resurser |
| Om-ingen-match * |PUT lyckas bara om det inte finns någon resurs |


## <a name="limits"></a>Begränsningar

Följande standardgränser gäller när du använder Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Nästa steg

* Börja använda Azure DNS och lära dig hur du [skapar en DNS-zon](dns-getstarted-create-dnszone-portal.md) och [skapar DNS-poster](dns-getstarted-create-recordset-portal.md).
* Om du vill migrera en befintlig DNS-zon lär du dig hur du [importerar och exporterar en DNS-zonfil](dns-import-export.md).
