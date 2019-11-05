---
title: Vad är en brand vägg för Azure Web Application på Azures front dörr?
description: Lär dig hur Azure-webbprograms brand vägg på Azures frontend-tjänst skyddar dina webb program mot skadliga attacker.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 09/28/2019
ms.author: victorh
ms.openlocfilehash: ce70260c6033d22b20675d6f3872c2ffa6368252
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502354"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Azure Web Application-brandvägg i Azures front dörr

Azure brand vägg för webbaserade program (WAF) i Azures front dörr tillhandahåller centraliserat skydd för dina webb program som levereras globalt med Azures front dörr. Den är utformad och drivs för att skydda dina webbtjänster mot vanliga kryphål och säkerhetsproblem och se till att din tjänst har hög tillgänglighet för dina användare förutom att den hjälper dig att uppfylla efterlevnadskrav.

WAF på front dörren är en global och centraliserad lösning. Den distribueras på Azures nätverks gräns platser över hela världen och varje inkommande begäran för ett WAF-aktiverat webb program som levereras av front dörren inspekteras i nätverks kanten. Detta gör att WAF förhindrar skadliga attacker nära angrepps källorna innan de går in i ditt virtuella nätverk och erbjuder globalt skydd i skala utan att offra prestanda. En WAF-princip kan enkelt länkas till en profil för en front dörr i din prenumeration och nya regler kan distribueras på några minuter, så att du snabbt kan svara på att ändra hot mönster.

![Brand vägg för Azure-webbprogram](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>WAF princip och regler

Du kan konfigurera en WAF-princip och associera principen till en eller flera front dörrs frontend-sidor för skydd. En WAF-princip består av två typer av säkerhets regler:

- anpassade regler som har skapats av kunden.

- hanterade regel uppsättningar som är en samling med Azure-hanterad förkonfigurerad uppsättning regler.

När båda finns bearbetas anpassade regler innan reglerna bearbetas i en hanterad regel uppsättning. En regel består av ett matchnings villkor, en prioritet och en åtgärd. De åtgärds typer som stöds är: Tillåt, blockera, logga och OMDIRIGERA. Du kan skapa en helt anpassad princip som uppfyller dina specifika krav för program skydd genom att kombinera de hanterade och anpassade reglerna.

Regler i en princip bearbetas i en prioriterad ordning där prioritet är ett unikt heltal som definierar ordningen för regler som bearbetas. Mindre heltals värde anger högre prioritet och de utvärderas före regler med ett högre heltals värde. När en regel har matchats tillämpas motsvarande åtgärd som definierats i regeln för begäran. När en sådan matchning bearbetas bearbetas inte regler med lägre prioritet ytterligare.

Ett webb program som levereras av front dörren kan bara ha en WAF-princip kopplad till sig i taget. Du kan dock ha en konfiguration för en front dörr utan några WAF-principer. Om det finns en WAF-princip replikeras den till alla våra gräns platser för att säkerställa konsekventa säkerhets principer över hela världen.

## <a name="waf-modes"></a>WAF-lägen

WAF-principen kan konfigureras att köras i följande två lägen:

- **Identifierings läge:** Vid körning i identifierings läge vidtar WAF inga andra åtgärder än övervakare och loggar begäran och dess matchade WAF-regel till WAF-loggar. Du kan aktivera Logging Diagnostics för front dörr (när du använder portalen kan du göra det genom att gå till avsnittet **diagnostik** i Azure Portal).

- **Skydds läge:** När WAF har kon figurer ATS för att köras i förebyggande läge, tar den angivna åtgärden om en begäran matchar en regel och om en matchning hittas utvärderas inga ytterligare regler med lägre prioritet. Alla matchade begär Anden loggas också i WAF-loggarna.

## <a name="waf-actions"></a>WAF-åtgärder

WAF-kunder kan välja att köra från en av åtgärderna när en begäran matchar en regels villkor:

- **Tillåt:**  Begäran passerar genom WAF och vidarebefordras till Server delen. Inga ytterligare regler för lägre prioritet kan blockera den här begäran.
- **Blockera:** Begäran blockeras och WAF skickar ett svar till klienten utan att vidarebefordra begäran till Server delen.
- **Logg:**  Begäran loggas i WAF-loggarna och WAF fortsätter att utvärdera lägre prioritets regler.
- **Omdirigera:** WAF omdirigerar begäran till angiven URI. Den angivna URI: n är en princip nivå inställning. När den har kon figurer ATS skickas alla förfrågningar som matchar **omdirigeringen** till denna URI.

## <a name="waf-rules"></a>WAF-regler

En WAF-princip kan bestå av två typer av säkerhets regler – anpassade regler, som har skapats av kunden och hanterade rulesets, Azure-hanterad uppsättning regler.

### <a name="custom-authored-rules"></a>Anpassade skapade regler

Du kan konfigurera anpassade regler WAF på följande sätt:

- Lista **över tillåtna IP-listor och blockerade listor:** Du kan konfigurera anpassade regler för att styra åtkomsten till dina webb program baserat på en lista över klient-IP-adresser eller IP-adressintervall. Både IPv4-och IPv6-adress typer stöds. Den här listan kan konfigureras för att antingen blockera eller tillåta de förfrågningar där käll-IP: en motsvarar en IP-adress i listan.

- **Geografisk baserad åtkomst kontroll:** Du kan konfigurera anpassade regler för att styra åtkomsten till dina webb program baserat på den landskod som är kopplad till en klients IP-adress.

- **Http-parameter-baserad åtkomst kontroll:** Du kan konfigurera anpassade regler baserat på sträng matchnings parametrar för HTTP/HTTPS, till exempel frågesträngar, POST argument, begär ande-URI, begär ande huvud och brödtext.

- **Begär metod baserad åtkomst kontroll:** Du kan konfigurera anpassade regler baserat på HTTP-begärans metod för begäran, till exempel GET, tag eller HEAD.

- **Storleks begränsning:** Du kan konfigurera anpassade regler baserat på längden på vissa delar av en begäran, till exempel frågesträng, URI eller brödtext i begäran.

- **Hastighets begränsnings regler:** En hastighets kontroll regel är att begränsa onormal hög trafik från alla klient-IP-adresser. Du kan konfigurera ett tröskelvärde för antalet webb förfrågningar som tillåts från en klient-IP under en minuts varaktighet. Detta skiljer sig från en anpassad regel för att tillåta/blockera en IP-lista som antingen tillåter alla eller blockerar all begäran från en klient-IP. Hastighets begränsning kan kombineras med ytterligare matchnings villkor, till exempel HTTP (S) parametrar som matchar en detaljerad hastighets kontroll.

### <a name="azure-managed-rule-sets"></a>Azure-hanterade regel uppsättningar

Azure-hanterade regel uppsättningar ger ett enkelt sätt att distribuera skydd mot en gemensam uppsättning säkerhetshot. Eftersom sådana rulesets hanteras av Azure uppdateras reglerna vid behov för att skydda mot nya attack-signaturer. I offentlig för hands version inkluderar den Azure-hanterade standard regel uppsättningen regler mot följande hot kategorier:

- Skript för Cross-Site
- Java-attacker
- Lokal fil inkludering
- Angrepp för PHP-injektering
- Fjärrkörning av kommando
- Fjärrstyrd fil inkludering
- Sessions-bindning
- Skydd mot SQL-inmatning
- Protokoll angripare

Versions numret för standard regel uppsättningen kommer att öka när nya angrepps signaturer läggs till i regel uppsättningen.
Standard regel uppsättningen är aktive rad som standard i identifierings läge i dina WAF-principer. Du kan inaktivera eller aktivera enskilda regler inom standard regel uppsättningen för att uppfylla dina program krav. Du kan också ange specifika åtgärder (Tillåt/blockera/OMDIRIGERA/logga) per regel. Standard åtgärd är att blockera. Dessutom kan anpassade regler konfigureras i samma WAF-princip om du vill kringgå någon av de förkonfigurerade reglerna i standard regel uppsättningen.
Anpassade regler tillämpas alltid innan regler i standard regel uppsättningen utvärderas. Om en begäran matchar en anpassad regel tillämpas motsvarande regel åtgärd och begäran blockeras eller skickas vidare till Server del, utan att några ytterligare anpassade regler eller regler anges i standard regel uppsättningen. Dessutom kan du välja att ta bort standard regel uppsättningen från dina WAF-principer.


### <a name="bot-protection-rule-preview"></a>Skydds regel för bot (för hands version)

En regel uppsättning för hanterad bot-skydd kan aktive ras för WAF för att vidta anpassade åtgärder på begär Anden från kända skadliga IP-adresser. IP-adresserna har en källa från Microsoft Threat Intelligence-flödet. [Intelligent Security graphs](https://www.microsoft.com/security/operations/intelligence) befogenheter Microsoft Threat intelligence och används av flera tjänster, inklusive Azure Security Center.

![Regel uppsättning för bot-skydd](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> Regel uppsättningen för bot-skydd är för närvarande en offentlig för hands version och tillhandahålls med ett service nivå avtal för för hands versionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet.  Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om bot-skydd är aktiverat loggas inkommande begär Anden som matchar skadlig robotar-klient-IP-adresser i FrontdoorWebApplicationFirewallLog-loggen. Du får åtkomst till WAF-loggar från lagrings kontot, händelsehubben eller Log Analytics. 

## <a name="configuration"></a>Konfiguration

Att konfigurera och distribuera alla WAF regel typer stöds fullt ut med Azure Portal, REST API: er, Azure Resource Manager mallar och Azure PowerShell.

## <a name="monitoring"></a>Övervakning

Övervakning av WAF i front dörren är integrerat med Azure Monitor för att spåra aviseringar och enkelt övervaka trafik trender.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [brand vägg för webbaserade program på Azure Application Gateway](../ag/ag-overview.md)