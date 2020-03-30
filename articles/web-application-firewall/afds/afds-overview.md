---
title: Vad är Brandvägg för Azure-webbprogram på Azure Front Door?
description: Lär dig hur Azure-brandväggen för webbprogram i Azure Front Door-tjänsten skyddar dina webbprogram från skadliga attacker.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: c8ff1849668d5effe15b6c25d00f3965a17b8e3e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77915647"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Azure Web Application-brandväggen på Azure-ytterdörren

Azure Web Application Firewall (WAF) på Azure Front Door ger centraliserat skydd för dina webbprogram. WAF försvarar dina webbtjänster mot vanliga bedrifter och sårbarheter. Det håller din tjänst mycket tillgänglig för dina användare och hjälper dig att uppfylla efterlevnadskraven.

WAF on Front Door är en global och centraliserad lösning. Den distribueras på Azure-nätverkskantplatser runt om i världen. WAF-aktiverade webbapplikationer inspekterar varje inkommande begäran som levereras av Ytterdörren vid nätverkskanten. 

WAF förhindrar skadliga attacker i närheten av attackkällorna innan de kommer in i ditt virtuella nätverk. Du får globalt skydd i stor skala utan att offra prestanda. En WAF-policy länkar enkelt till valfri Front Door-profil i din prenumeration. Nya regler kan distribueras inom några minuter, så att du snabbt kan svara på ändrade hotmönster.

![Brandvägg för Azure-webbprogram](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>WAF:s policy och regler

Du kan konfigurera en WAF-princip och associera principen med en eller flera frontslut för fronten för front. En WAF-princip består av två typer av säkerhetsregler:

- anpassade regler som skapas av kunden.

- hanterade regeluppsättningar som är en samling Azure-hanterade förkonfigurerade regeluppsättningar.

När båda finns bearbetas anpassade regler innan reglerna bearbetas i en hanterad regeluppsättning. En regel består av ett matchningsvillkor, en prioritet och en åtgärd. Åtgärdstyper som stöds är: TILLÅT, BLOCKERA, LOGGA och OMDIRIGERA. Du kan skapa en helt anpassad princip som uppfyller dina specifika programskyddskrav genom att kombinera hanterade och anpassade regler.

Regler inom en princip bearbetas i prioritetsordning. Prioritet är ett unikt heltal som definierar ordningen på de regler som ska bearbetas. Mindre heltalsvärde anger en högre prioritet och dessa regler utvärderas före regler med ett högre heltalsvärde. När en regel har matchats tillämpas motsvarande åtgärd som definierades i regeln på begäran. När en sådan matchning har bearbetats bearbetas inte regler med lägre prioritet ytterligare.

En webbapplikation som levereras av Ytterdörren kan bara ha en WAF-princip kopplad till den åt gången. Du kan dock ha en frontdörrkonfiguration utan waf-principer som är associerade med den. Om det finns en WAF-princip replikeras den till alla våra kantplatser för att säkerställa konsekventa säkerhetsprinciper över hela världen.

## <a name="waf-modes"></a>WAF-lägen

WAF-principen kan konfigureras för att köras i följande två lägen:

- **Identifieringsläge:** När waf körs i identifieringsläge vidtar du inga andra åtgärder än övervakare och loggar begäran och dess matchade WAF-regel till WAF-loggar. Du kan aktivera loggningsdiagnostik för ytterdörren. När du använder portalen går du till avsnittet **Diagnostik.**

- **Förebyggande läge:** I förebyggande läge vidtar WAF den angivna åtgärden om en begäran matchar en regel. Om en matchning hittas utvärderas inga ytterligare regler med lägre prioritet. Alla matchade begäranden loggas också i WAF-loggarna.

## <a name="waf-actions"></a>WAF-åtgärder

WAF-kunder kan välja att köra från en av åtgärderna när en begäran matchar en regels villkor:

- **Tillåt:**  Begäran passerar genom WAF och vidarebefordras till backend. Inga ytterligare regler med lägre prioritet kan blockera den här begäran.
- **Blockera:** Begäran blockeras och WAF skickar ett svar till klienten utan att vidarebefordra begäran till backend.
- **Logga:**  Begäran loggas i WAF-loggarna och WAF fortsätter att utvärdera regler med lägre prioritet.
- **Omdirigera:** WAF omdirigerar begäran till den angivna URI:n. Den angivna URI-principen är en principnivåinställning. När alla begäranden som matchar åtgärden **Omdirigera** har konfigurerats skickas de till den URI:n.

## <a name="waf-rules"></a>WAF-regler

En WAF-princip kan bestå av två typer av säkerhetsregler – anpassade regler, som skapas av kunden och hanterade regeluppsättningar, Azure-hanterade förkonfigurerade regeluppsättningar.

### <a name="custom-authored-rules"></a>Anpassade författade regler

Du kan konfigurera anpassade regler WAF på följande sätt:

- **LISTA och blocklista för IP-tillåta:** Du kan styra åtkomsten till dina webbprogram baserat på en lista över klient-IP-adresser eller IP-adressintervall. Både IPv4- och IPv6-adresstyper stöds. Den här listan kan konfigureras för att antingen blockera eller tillåta de begäranden där käll-IP matchar en IP-adress i listan.

- **Geografiskt baserad åtkomstkontroll:** Du kan styra åtkomsten till dina webbprogram baserat på landskoden som är kopplad till en klients IP-adress.

- **HTTP-parameterbaserad åtkomstkontroll:** Du kan basera regler på strängmatchningar i HTTP/HTTPS-begäranden.  Till exempel frågesträngar, POST args, Request URI, Request Header och Request Body.

- **Begär metodbaserad åtkomstkontroll:** Du baserar regler på HTTP-begäransmetoden för begäran. Till exempel GET, PUT eller HEAD.

- **Storleksbegränsning:** Du kan basera regler på längden på specifika delar av en begäran, till exempel frågesträng, Uri eller begärandetext.

- **Regler för hastighetsbegränsning:** En hastighetskontrollregel är att begränsa onormal hög trafik från en klient-IP. Du kan konfigurera ett tröskelvärde för antalet webbbegäranden som tillåts från en klient-IP under en minuts varaktighet. Den här regeln skiljer sig från en IP-lista-baserad tillåt/blockera anpassad regel som antingen tillåter alla eller blockerar alla begäranden från en klient-IP. Hastighetsgränser kan kombineras med ytterligare matchningsvillkor, till exempel HTTP(S) parametermatchningar för detaljerad hastighetskontroll.

### <a name="azure-managed-rule-sets"></a>Azure-hanterade regeluppsättningar

Azure-hanterade regeluppsättningar är ett enkelt sätt att distribuera skydd mot en gemensam uppsättning säkerhetshot. Eftersom sådana regeluppsättningar hanteras av Azure uppdateras reglerna efter behov för att skydda mot nya angreppssignaturer. Azure-hanterad standardregeluppsättning innehåller regler mot följande hotkategorier:

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
Standardregeluppsättning är aktiverad som standard i identifieringsläge i WAF-principerna. Du kan inaktivera eller aktivera enskilda regler i standardregeluppsättningen för att uppfylla dina programkrav. Du kan också ange specifika åtgärder (ALLOW/BLOCK/REDIRECT/LOG) per regel.

Ibland kan du behöva utelämna vissa begäranattribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory-infogade token som används för autentisering. Du kan konfigurera en undantagslista för en hanterad regel, regelgrupp eller för hela regeluppsättningen.  

Standardåtgärden är att BLOCKERA. Dessutom kan anpassade regler konfigureras i samma WAF-princip om du vill kringgå någon av de förkonfigurerade reglerna i standardregeluppsättningen.

Anpassade regler tillämpas alltid innan regler i standardregeluppsättningen utvärderas. Om en begäran matchar en anpassad regel tillämpas motsvarande regelåtgärd. Begäran blockeras eller skickas till backend. Inga andra anpassade regler eller reglerna i standardregeluppsättningen bearbetas. Du kan också ta bort standardregeluppsättningen från WAF-principerna.

### <a name="bot-protection-rule-set-preview"></a>Regeluppsättning för botskydd (förhandsgranskning)

Du kan aktivera en hanterad bot-skyddsregeluppsättning för att vidta anpassade åtgärder på begäranden från kända botkategorier. 

Det finns tre bot kategorier som stöds: Bad, Good och Unknown. Bot signaturer hanteras och uppdateras dynamiskt av WAF-plattformen.

Dåliga robotar inkluderar robotar från skadliga IP-adresser och robotar som har förfalskat sina identiteter. Skadliga IP-adresser kommer från Microsoft Threat Intelligence-flödet och uppdateras varje timme. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) driver Microsoft Threat Intelligence och används av flera tjänster, inklusive Azure Security Center.

Bra Bots inkluderar validerade sökmotorer. Okända kategorier inkluderar ytterligare botgrupper som har identifierat sig som robotar. Till exempel marknadsanalysator, feed fetchers och datainsamlingsagenter. 

Okända robotar klassificeras via publicerade användaragenter utan ytterligare validering. Du kan ange att anpassade åtgärder ska blockera, tillåta, logga eller omdirigera för olika typer av robotar.

![Regeluppsättning för botskydd](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Regeluppsättningen för botskydd är för närvarande i offentlig förhandsversion och har ett förhandsversionsavtal på tjänstnivå. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet.  Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om botskydd är aktiverat loggas inkommande begäranden som matchar bot-regler i FrontdoorWebApplicationFirewallLog-loggen. Du kan komma åt WAF-loggar från ett lagringskonto, händelsenav eller logganalys.

## <a name="configuration"></a>Konfiguration

Du kan konfigurera och distribuera alla WAF-regeltyper med hjälp av Azure-portalen, REST-API:er, Azure Resource Manager-mallar och Azure PowerShell.

## <a name="monitoring"></a>Övervakning

Övervakning för WAF vid ytterdörren är integrerad med Azure Monitor för att spåra aviseringar och enkelt övervaka trafiktrender.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [brandväggen för webbprogram i Azure Application Gateway](../ag/ag-overview.md)