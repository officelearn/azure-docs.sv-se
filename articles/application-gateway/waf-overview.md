---
title: Introduktion till Brandvägg för webbaserade program för Azure Application Gateway
description: Den här artikeln innehåller en översikt över Brandvägg för webbaserade program (WAF) för Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 830513a03bd65ca14cb0938ae599a676f1bb3bca
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518192"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Brandvägg för webbaserade program för Azure Application Gateway

Azure Application Gateway erbjuder en brandvägg för webbaserade program (WAF) som ger ett centraliserat skydd av dina webbappar mot vanliga kryphål och säkerhetsproblem. Webbprogram som alltmer omfattas av skadliga attacker som utnyttjar ofta kända sårbarheter. SQL-inmatning och cross site scripting är bland de vanligaste attackerna.

Det är svårt att förhindra sådana attacker i programkoden. Det kan kräver ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin. En centraliserad brandvägg gör säkerhetshantering mycket enklare. En WAF även får administratörer bättre skydd mot hot och intrång.

En brandväggslösning kan reagera på ett säkerhetshot snabbare genom att centralt åtgärda en känd svaghet, i stället för att skydda varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till fire wall-aktiverade programgatewayer.

Application Gateway WAF är baserad på [Core Rule ange (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9 från den öppna OWASP Web Application Security Project (). WAF uppdateras automatiskt med skydd mot nya säkerhetsrisker med ingen ytterligare konfiguration krävs.

![Application Gateway WAF-diagram](./media/waf-overview/WAF1.png)

Application Gateway fungerar som en application Deliver controller (ADC). Den erbjuder Secure Sockets Layer (SSL)-avslutning, cookie-baserad sessionstillhörighet, belastningsutjämning med resursallokering, innehållsbaserad routning, möjlighet att vara värd för flera webbplatser och säkerhetsförbättringar.

Säkerhetsförbättringarna som Application Gateway inkluderar SSL-principhantering och stöd för slutpunkt till slutpunkt SSL. Programsäkerhet ökas genom att WAF-integrering i Application Gateway. Kombinationen skyddar dina webbprogram mot vanliga säkerhetsrisker. Och det ger ett enkelt att konfigurera central plats för att hantera.

## <a name="benefits"></a>Fördelar

Det här avsnittet beskrivs viktiga fördelar med Application Gateway och dess WAF tillhandahåller.

### <a name="protection"></a>Skydd

* Skydda dina webbprogram från säkerhetsrisker och attacker utan ändringar till backend-koden.

* Skydda flera webbprogram på samma gång. En instans av Application Gateway kan vara värd för upp till 20 webbplatser som skyddas av en brandvägg för webbaserade program.

### <a name="monitoring"></a>Övervakning

* Övervaka attacker mot dina webbprogram med hjälp av en realtidslogg för WAF. Loggen är integrerad med [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) spåra WAF aviseringar och enkelt övervaka trender.

* Application Gateway WAF är integrerad med Azure Security Center. Security Center ger en central vy över säkerhetsläget hos alla dina Azure-resurser.

### <a name="customization"></a>Anpassning

* Du kan anpassa WAF-regler och regelgrupper efter dina programkrav och eliminera falska positiva resultat.

## <a name="features"></a>Funktioner

- SQL-injection protection.
- Cross site scripting skydd.
- Skydd mot andra vanliga webbattacker, till exempel kommandot inmatning, HTTP-begäran som kommandoinmatning, HTTP-svar och remote filinkludering.
- Skydd mot protokollbrott för HTTP.
- Användaragent för värden och accept-skydd mot HTTP-protokollet avvikelser, till exempel som saknas.
- Skydd mot robotar, crawlers och skannrar.
- Identifiering av vanliga felkonfigureringar för programmet (till exempel Apache och IIS).
- Storleksförfrågningar med konfigurerbara gränser med lägre och övre gränser.
- Undantagslistor kan du utesluta vissa begärandeattribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory-infogas token som används för autentisering eller lösenorden.

### <a name="core-rule-sets"></a>Core Rule Sets

Application Gateway stöder två regeluppsättningar: CRS 3.0 och CRS 2.2.9. Dessa regler skyddar dina webbprogram från skadlig aktivitet.

Application Gateway WAF är förkonfigurerad med CRS 3.0 som standard. Men du kan välja att använda CRS 2.2.9 i stället. I CRS 3.0 finns minska falska positiva identifieringar jämfört med CRS 2.2.9. Du kan också [anpassa regler för att passa dina behov](application-gateway-customize-waf-rules-portal.md).

WAF skyddar mot följande säkerhetsrisker:

- SQL-injection attacks
- Cross-site skriptattacker
- Andra vanliga attacker, till exempel kommandot inmatning, HTTP-begäran huvudinmatning, HTTP-svar delning och införande av fjärrfil
- HTTP-protokollet överträdelser
- Användaragent för värden och accept-HTTP-protokollet avvikelser, till exempel som saknas
- Robotar, crawlers och skannrar
- Vanliga felkonfigureringar för programmet (till exempel Apache och IIS)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

I CRS 3.0 innehåller 13 regelgrupper som visas i följande tabell. Varje grupp innehåller flera regler som kan inaktiveras.

|Regelgrupp|Beskrivning|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Låsning metoder (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|Skydda mot port- och miljöskannrar|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Skydda mot protokoll- och kodningsproblem|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Skydda mot huvud-inmatning, begäran som kommandoinmatning och delning av svar.|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Skydda mot fil- och sökvägsattacker|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Skydda mot fjärrfil inkludering (RFI) attacker|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Skydda igen fjärrkörning av kod körning attacker|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Skydda mot PHP-inmatningsattacker.|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Skydda mot cross-site skriptattacker|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Skydda mot SQL-inmatningsattacker.|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Skydda mot sessionsfixering attacker|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CRS 2.2.9 innehåller 10 regelgrupper som visas i följande tabell. Varje grupp innehåller flera regler som kan inaktiveras.

|Regelgrupp|Beskrivning|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Skydda mot protokollbrott (till exempel ogiltiga tecken eller GET med en begärandetext)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Skydda mot felaktig huvudinformation|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Skydda mot argument eller filer som överstiger begränsningarna|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Skydda mot begränsade metoder, huvuden och filtyper|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Skydda mot webbcrawlers och skannrar|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Skydda mot allmänna attacker (till exempel sessionsfixering, remote filinkludering och PHP-inmatning)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Skydda mot SQL-inmatningsattacker.|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Skydda mot cross-site skriptattacker|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Skydda mot attacker sökväg-genomgång|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Skydda mot bakdörrstrojaner|

### <a name="waf-modes"></a>WAF-lägen

Application Gateway WAF kan konfigureras för att köras i följande två lägen:

* **Identifieringsläge**: Övervakar och loggar alla hotaviseringar. Du aktiverar loggningsdiagnostik för Application Gateway i den **diagnostik** avsnittet. Du måste också kontrollera att WAF-loggen har valts och aktiveras. Brandvägg för webbaserade program blockerar inte inkommande begäranden när det körs i identifieringsläge.
* **Förhindringsläge**: Block intrång och attacker som identifieras av reglerna. Angriparen tar emot ett ”403 för obehörig åtkomst”-undantag och avslutas anslutningen. Förhindringsläge registrerar sådana attacker i WAF-loggar.

### <a name="anomaly-scoring-mode"></a>Avvikelseidentifiering beräkning läge
 
OWASP har två lägen för att bestämma om du vill blockera trafik: Traditionell och Avvikelseidentifiering bedömning läge.

Trafik som matchar Standardregeln anses vara oberoende av andra regel-matchningar i traditionella läge. Det här läget är lätt att förstå. Men avsaknaden av information om hur många regler matchar en specifik begäran är en begränsning. Därför introducerades Avvikelseidentifiering bedömning läget. Det är standard för OWASP-3. *x*.

I läget för Avvikelseidentifiering bedömning trafik som matchar Standardregeln är inte omedelbart att blockeras när den är i förhindringsläge. Regler har en viss allvarlighetsgrad: *Kritiska*, *fel*, *varning*, eller *meddelande*. Den allvarlighetsgraden påverkar ett numeriskt värde för begäran, vilket kallas den Avvikelsepoäng. Exempelvis en *varning* regeln matchar bidrar 3 till poängen. En *kritisk* regeln matchar bidrar 5.

Det finns ett tröskelvärde på 5 för Avvikelsepoäng till blockerar trafiken. Det öppnar en enda *kritisk* regeln matchar räcker för Application Gateway WAF att blockera en begäran, även i förhindringsläge. Men en *varning* regeln matchar ökar bara Avvikelseidentifiering poäng med 3, vilket inte är tillräckligt ensamt blockera trafik.

> [!NOTE]
> Det meddelande som loggas när en WAF-regeln matchar trafik innehåller Åtgärdsvärdet ”blockerad”. Men trafiken är egentligen bara blockerade för en Avvikelsepoäng på 5 eller senare.  

### <a name="waf-monitoring"></a>WAF-övervakning

Det är viktigt att övervaka hälsotillståndet för Application Gateway. Övervaka hälsotillståndet för din WAF och de program som den skyddar stöds genom integrering med Azure Security Center, Azure Monitor och Azure Monitor-loggar.

![Diagram över Application Gateway WAF-diagnostik](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Application Gateway-loggar är integrerade med [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md). På så sätt kan du spåra diagnostisk information, inklusive WAF varningar och loggar. Du kan komma åt den här funktionen på den **diagnostik** flik i Application Gateway-resursen i portalen eller direkt via Azure Monitor. Mer information om hur du aktiverar loggar finns [Application Gateway-diagnostik](application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../security-center/security-center-intro.md) hjälper till att förhindra, upptäcka och svara på hot. Det ger ökad insyn i och kontroll över säkerheten hos dina Azure-resurser. Application Gateway är [integrerade med Security Center](application-gateway-integration-security-center.md). Security Center söker igenom din miljö för att identifiera oskyddade webbappar. Det kan rekommenderar Programgatewayen WAF för att skydda de här känsliga resurserna. Du kan skapa andra direkt från Security Center. De här WAF-instanserna är integrerade med Security Center. De Skicka aviseringar och hälsoinformation till Security Center för rapportering.

![Fönstret för översikt av Security Center](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Loggning

Application Gateway WAF erbjuder detaljerad rapportering för varje hot som upptäcks. Loggningen är integrerad med Azure-diagnostikloggar. Varningar registreras i JSON-format. Dessa loggar kan integreras med [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md).

![Application Gateway-diagnostiken loggar windows](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Priser för WAF SKU för programgatewayen

Application Gateway WAF är tillgänglig i en ny en SKU. Denna SKU finns bara i Azure Resource Manager-etableringsmodellen, inte i den klassiska distributionsmodellen. WAF SKU finns dessutom endast i medelstora och stora instansstorlekar för Application Gateway. Alla begränsningar som gäller för Application Gateway gäller även för WAF SKU.

Priserna baseras på gateway-instans timme och en avgift för databearbetning. [Priser för Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) för WAF SKU skiljer sig från avgifterna för standard-SKU. Databearbetning-är desamma. Det finns inga avgifter per regel eller grupp. Du kan skydda flera webbprogram bakom samma brandväggen för webbaserade program. Du debiteras inte för att stödja flera program.

## <a name="next-steps"></a>Nästa steg

Se [hur du konfigurerar brandväggen för webbaserade program på Application Gateway](tutorial-restrict-web-traffic-powershell.md).
