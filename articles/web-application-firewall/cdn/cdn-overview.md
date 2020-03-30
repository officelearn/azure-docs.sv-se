---
title: Vad är Brandvägg för Azure-webbprogram på Azure CDN?
description: Lär dig hur Azure-brandväggen för webbprogram i Azure CDN-tjänsten skyddar dina webbprogram från skadliga attacker.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 559ce4d1500dc5f1b67a3b3dbdbeb5d8dda7e487
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79485855"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network"></a>Azure Web Application-brandväggen i Azure Content Delivery Network

Azure Web Application Firewall (WAF) på Azure Content Delivery Network (CDN) ger centraliserat skydd för ditt webbinnehåll. WAF försvarar dina webbtjänster mot vanliga bedrifter och sårbarheter. Det håller din tjänst mycket tillgänglig för dina användare och hjälper dig att uppfylla efterlevnadskraven.

> [!IMPORTANT]
> WAF på Azure CDN är för närvarande i offentlig förhandsversion och är försedd med ett förhandsversionstjänstnivåavtal. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet.  Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

WAF på Azure CDN är en global och centraliserad lösning. Den distribueras på Azure-nätverkskantplatser runt om i världen. WAF stoppar skadliga attacker nära attackkällorna, innan de når ditt ursprung. Du får globalt skydd i stor skala utan att offra prestanda. 

En WAF-princip länkar enkelt till alla CDN-slutpunkter i din prenumeration. Nya regler kan distribueras inom några minuter, så att du snabbt kan svara på ändrade hotmönster.

![Brandvägg för Azure-webbprogram](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>WAF:s policy och regler

Du kan konfigurera en WAF-princip och associera principen till en eller flera CDN-slutpunkter för skydd. En WAF-princip består av två typer av säkerhetsregler:

- anpassade regler som du kan skapa.

- hanterade regeluppsättningar som är en samling azure-hanterade förkonfigurerade regler.

När båda finns bearbetas anpassade regler innan reglerna bearbetas i en hanterad regeluppsättning. En regel består av ett matchningsvillkor, en prioritet och en åtgärd. Åtgärdstyper som stöds är: *TILLÅT*, *BLOCKERA,* *LOGGA*och *OMDIRIGERA*. Du kan skapa en helt anpassad princip som uppfyller dina specifika programskyddskrav genom att kombinera hanterade och anpassade regler.

Regler inom en princip bearbetas i prioritetsordning. Prioritet är ett unikt tal som definierar ordningen på de regler som ska bearbetas. Mindre tal har högre prioritet och dessa regler utvärderas före regler med ett större värde. När en regel har matchats tillämpas motsvarande åtgärd som definierades i regeln på begäran. När en sådan matchning har bearbetats bearbetas inte regler med lägre prioritet ytterligare.

Ett webbprogram som finns på Azure CDN kan bara ha en WAF-princip associerad med den åt gången. Du kan dock ha en CDN-slutpunkt utan waf-principer som är associerade med den. Om det finns en WAF-princip replikeras den till alla våra kantplatser för att säkerställa konsekventa säkerhetsprinciper över hela världen.

## <a name="waf-modes"></a>WAF-lägen

WAF-principen kan konfigureras för att köras i följande två lägen:

- *Identifieringsläge*: När waf körs i identifieringsläge vidtar du inga andra åtgärder än bildskärmar och loggar begäran och dess matchade WAF-regel till WAF-loggar. Du kan aktivera loggningsdiagnostik för ytterdörren. När du använder portalen går du till avsnittet **Diagnostik.**

- *Förebyggande läge*: I förebyggande läge vidtar WAF den angivna åtgärden om en begäran matchar en regel. Om en matchning hittas utvärderas inga ytterligare regler med lägre prioritet. Alla matchade begäranden loggas också i WAF-loggarna.

## <a name="waf-actions"></a>WAF-åtgärder

Du kan välja en av följande åtgärder när en begäran matchar en regels villkor:

- *Tillåt*: Begäran passerar genom WAF och vidarebefordras till backend. Inga ytterligare regler med lägre prioritet kan blockera den här begäran.
- *Blockera*: Begäran är blockerad och WAF skickar ett svar till klienten utan att vidarebefordra begäran till backend.
- *Logg:* Begäran loggas i WAF-loggarna och WAF fortsätter att utvärdera regler med lägre prioritet.
- *Omdirigering*: WAF omdirigerar begäran till den angivna URI.Redirect : WAF redirects the request to the specified URI. Den angivna URI-principen är en principnivåinställning. När alla begäranden som matchar *åtgärden Omdirigera* har konfigurerats skickas de till den URI:n.

## <a name="waf-rules"></a>WAF-regler

En WAF-princip kan bestå av två typer av säkerhetsregler:

- *anpassade regler:* regler som du skapar själv 
- *hanterade regeluppsättningar:* Azure-hanterade förkonfigurerade uppsättning regler

### <a name="custom-rules"></a>Anpassade regler

Anpassade regler kan ha matchningsregler och regler för hastighetskontroll.

Du kan konfigurera följande anpassade matchningsregler:

- *IP-tillåta lista och blockera lista:* Du kan styra åtkomsten till dina webbprogram baserat på en lista över klient-IP-adresser eller IP-adressintervall. Både IPv4- och IPv6-adresstyper stöds. Den här listan kan konfigureras för att antingen blockera eller tillåta de begäranden där käll-IP matchar en IP-adress i listan.

- *Geografisk baserad åtkomstkontroll*: Du kan styra åtkomsten till dina webbprogram baserat på landskoden som är associerad med en klients IP-adress.

- *HTTP-parameterbaserad åtkomstkontroll*: Du kan basera regler på strängmatchningar i PARAMETRAR för HTTP/HTTPS-begäran.  Till exempel frågesträngar, POST args, Request URI, Request Header och Request Body.

- *Begäran metodbaserad åtkomstkontroll*: Du baserar regler på HTTP-begäran metoden för begäran. Till exempel GET, PUT eller HEAD.

- *Storleksbegränsning:* Du kan basera regler på längden på specifika delar av en begäran, till exempel frågesträng, Uri eller begärandetext.

En hastighetskontrollregel begränsar onormalt hög trafik från alla klient-IP-adresser.

- *Regler för hastighetsbegränsning:* Du kan konfigurera ett tröskelvärde för antalet webbbegäranden som tillåts från en klient-IP-adress under en minuts varaktighet. Den här regeln skiljer sig från en IP-lista-baserad tillåt/blockera anpassad regel som antingen tillåter alla eller blockerar alla begäranden från en klient-IP-adress. Hastighetsgränser kan kombineras med ytterligare matchningsvillkor, till exempel HTTP(S) parametermatchningar för detaljerad hastighetskontroll.

### <a name="azure-managed-rule-sets"></a>Azure-hanterade regeluppsättningar

Azure-hanterade regeluppsättningar är ett enkelt sätt att distribuera skydd mot en gemensam uppsättning säkerhetshot. Eftersom dessa regeluppsättningar hanteras av Azure uppdateras reglerna efter behov för att skydda mot nya attacksignaturer. Den hanterade standardregeluppsättningen för Azure innehåller regler mot följande hotkategorier:

- Skriptkörning över flera webbplatser
- Java-attacker
- Lokal filinkludering
- PHP injektion attacker
- Fjärrkommandokörning
- Införande av fjärrfil
- Session fixering
- Skydd mot SQL-inmatning
- Protokollangripare

Versionsnumret för standardregeluppsättningen ökar när nya attacksignaturer läggs till i regeluppsättningen.
Standardregeluppsättningen är aktiverad som standard i *identifieringsläge* i WAF-principerna. Du kan inaktivera eller aktivera enskilda regler i standardregeluppsättningen för att uppfylla dina programkrav. Du kan också ange specifika åtgärder (ALLOW/BLOCK/REDIRECT/LOG) per regel. Standardåtgärden för den hanterade standardregeluppsättningen är *Blockera*.

Anpassade regler tillämpas alltid innan regler i standardregeluppsättningen utvärderas. Om en begäran matchar en anpassad regel tillämpas motsvarande regelåtgärd. Begäran blockeras eller skickas till backend. Inga andra anpassade regler eller reglerna i standardregeluppsättningen bearbetas. Du kan också ta bort standardregeluppsättningen från WAF-principerna.

## <a name="configuration"></a>Konfiguration

Du kan konfigurera och distribuera alla WAF-regeltyper med hjälp av Azure-portalen, REST-API:er, Azure Resource Manager-mallar och Azure PowerShell.

## <a name="monitoring"></a>Övervakning

Övervakning för WAF med CDN är integrerad med Azure Monitor för att spåra aviseringar och enkelt övervaka trafiktrender.

## <a name="next-steps"></a>Nästa steg

- [Självstudiekurs: Skapa en WAF-princip med Azure CDN med Azure-portalen](waf-cdn-create-portal.md)