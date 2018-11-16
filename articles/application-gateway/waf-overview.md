---
title: Introduktion till Brandvägg för webbaserade program (WAF) för Azure Application Gateway
description: Den här artikeln innehåller en översikt över Brandvägg för webbaserade program (WAF) för Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 11/16/2018
ms.author: amsriva
ms.openlocfilehash: da2603efd6b55ed3b3371156a98568226b18a65a
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706899"
---
# <a name="web-application-firewall-waf"></a>Brandvägg för webbaserade program (WAF)

Brandvägg för webbaserade program (WAF) är en funktion i Application Gateway som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem.

Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Vanliga bland annat är SQL-inmatningsattacker och mellan webbplatser attacker för att nämna några. 

Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning kan också reagera på ett säkerhetshot snabbare genom att åtgärda en känd svaghet på en central plats i stället för att utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

WAF är baserat på regler från den [OWASP core rule sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9. Den uppdateras automatiskt med skydd mot nya säkerhetsrisker med ingen ytterligare konfiguration krävs.

![imageURLroute](./media/waf-overview/WAF1.png)

Application Gateway fungerar som en application Deliver controller (ADC) och erbjuder SSL-avslutning, cookie-baserad sessionstillhörighet, belastningsutjämning med resursallokering, innehållsbaserad routning, möjlighet att vara värd för flera webbplatser och säkerhetsförbättringar.

Säkerhetsförbättringarna som erbjuds av Application Gateway inkluderar SSL-principhantering och stöd för slutpunkt till slutpunkt SSL. Programsäkerheten har förstärkts genom att WAF är direkt integrerad i ADC. Den utgör ett enkelt sätt att konfigurera en central plats för att hantera dina webbprogram och skydda dem mot vanliga säkerhetsrisker.

## <a name="benefits"></a>Fördelar

Viktiga fördelar med Application Gateway och brandväggen för webbaserade program:

### <a name="protection"></a>Skydd

* Skyddar ditt webbprogram från säkerhetsrisker och attacker utan att du behöver ändra koden i serverdelen.

* Skyddar flera webbprogram samtidigt bakom en programgateway. Application Gateway har stöd för upp till 20 webbsidor bakom en enda gateway som skyddar dem alla från webbattacker med hjälp av brandväggen för webbaserade program.

### <a name="monitoring"></a>Övervakning

* Övervaka om ditt webbprogram utsätts för angrepp med hjälp av en realtidslogg för brandväggen för webbaserade program. Den här loggen är integrerad med [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) så att du kan spåra varningar och loggar för brandväggen för webbaserade program och enkelt övervaka trender.

* WAF är integrerad med Azure Security Center. I Azure Security Center finns en central vy som visar säkerhetsläget för alla dina Azure-resurser.

### <a name="customization"></a>Anpassning

* Du kan anpassa regler och regelgrupper för brandväggen så att de passar behoven för din app och eliminerar falska positiva identifieringar.

## <a name="features"></a>Funktioner

- Skydd av SQL-inmatning
- Cross site scripting skydd
- Skydd mot vanliga webbattacker, till exempel kommandot inmatning, HTTP-begäran som kommandoinmatning, HTTP-svar och attack för inkludering av fjärrfil
- Skydd mot protokollbrott för HTTP
- Användaragent för värden och accept-skydd mot avvikelser för HTTP-protokollet som saknas
- Skydd mot robotar, crawlers och skannrar
- Identifiering av vanliga felkonfigureringar för programmet (till exempel Apache, IIS och så vidare)

### <a name="public-preview-features"></a>Funktioner i offentlig förhandsversion

Den aktuella WAF offentliga förhandsversionen SKU: N innehåller följande funktioner:

- **Begär storleksgränser** -Brandvägg för webbaserade program kan du konfigurera storleksbegränsningar för begäran i lägre och övre gränser.
- **Undantagslistor** -WAF undantagslistor Tillåt användare att utelämna vissa begärandeattribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory infogas token som används för autentisering eller lösenorden.

Läs mer om den offentliga förhandsversionen av WAF [storleksgränser för programmet brandväggen begäran och undantagslistor (offentlig förhandsversion)](application-gateway-waf-configuration.md).





### <a name="core-rule-sets"></a>Core Rule Sets

Application Gateway stöder två regeluppsättningar: CRS 3.0 och CRS 2.2.9. Dessa Core Rule Sets är regelsamlingar som skyddar dina webbprogram mot skadlig aktivitet.

Brandväggen för webbaserade program är förkonfigurerad med CRS 3.0 som standard. Du kan också välja att använda 2.2.9. I CRS 3.0 finns bättre möjligheter att eliminera falska positiva identifieringar än i 2.2.9. Du kan [anpassa regler så att de passar dina behov](application-gateway-customize-waf-rules-portal.md). Några vanliga säkerhetshot som brandväggen för webbaserade program skyddar mot:

- Skydd av SQL-inmatning
- Cross site scripting skydd
- Skydd mot vanliga webbattacker, till exempel kommandot inmatning, HTTP-begäran som kommandoinmatning, HTTP-svar och attack för inkludering av fjärrfil
- Skydd mot protokollbrott för HTTP
- Användaragent för värden och accept-skydd mot avvikelser för HTTP-protokollet som saknas
- Skydd mot robotar, crawlers och skannrar
- Identifiering av vanliga felkonfigureringar för programmet (till exempel Apache, IIS osv.)

En detaljerad lista över regler och deras skydd finns i [Core regeluppsättningar](#core-rule-sets).


#### <a name="owasp30"></a>OWASP_3.0

Core Rule Set 3.0 har 13 regelgrupper (se följande tabell). Var och en av dessa regelgrupper innehåller flera regler. Dessa kan inaktiveras.

|RuleGroup|Beskrivning|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Innehåller regler för att låsa metoder (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**| Innehåller regler för att skydda mot port- och miljöskannrar.|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Innehåller regler för att skydda mot protokoll- och kodningsproblem.|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Innehåller regler för att skydda mot huvudinmatning, dold begäran och delning av svar.|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Innehåller regler för att skydda mot fil- och sökvägsattacker.|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Innehåller regler för att skydda mot RFI (Remote File Inclusion)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Innehåller regler för att skydda mot RCE (Remote Code Execution)|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Innehåller regler för att skydda mot PHP-inmatningsattacker.|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Innehåller regler för att skydda mot skriptkörning över flera webbplatser.|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Innehåller regler för att skydda mot SQL-inmatningsattacker.|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Innehåller regler för att skydda mot sessionsfixeringsattacker.|

#### <a name="owasp229"></a>OWASP_2.2.9

Core Rule Set 2.2.9 har 10 regelgrupper (se följande tabell). Var och en av dessa regelgrupper innehåller flera regler. Dessa kan inaktiveras.

|RuleGroup|Beskrivning|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Innehåller regler för att skydda mot protokollbrott (ogiltiga tecken, GET med en begärandetext osv.)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Innehåller regler för att skydda mot felaktig huvudinformation.|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Innehåller regler för att skydda mot argument eller filer som överstiger begränsningarna.|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Innehåller regler för att skydda mot begränsade metoder, huvuden och filtyper. |
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Innehåller regler för att skydda mot webbcrawlers och skannrar.|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Innehåller regler för att skydda mot allmänna attacker (sessionsfixering, Remote File Inclusion, PHP-inmatning osv.)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Innehåller regler för att skydda mot SQL-inmatningsattacker.|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Innehåller regler för att skydda mot skriptkörning över flera webbplatser.|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Innehåller en regel för att skydda mot ”path traversal”-attacker|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Innehåller regler för att skydda mot bakdörrstrojaner.|

### <a name="waf-modes"></a>WAF-lägen

Application Gateway WAF kan konfigureras för att köras i följande två lägen:

* **Identifieringsläge** – när du konfigurerade för körning i identifieringsläge, Application Gateway WAF övervakas och loggas alla hotvarningar i en loggfil. Loggningsdiagnostik för Application Gateway bör aktiveras i avsnittet **Diagnostik**. Du måste också säkerställa att WAF-loggen har valts och är aktiverad. Vid körning i identifieringsläge blockerar brandväggen för webbaserade program inte inkommande begäranden.
* **Förhindringsläge** – När Application Gateway har konfigurerats för att köras i förhindringsläge blockerar den aktivt intrång och attacker som identifieras enligt reglerna för den. Attackeraren erhåller undantag 403 för obehörig åtkomst och anslutningen avbryts. Förhindringsläget fortsätter logga sådana attacker i WAF-loggarna.

### <a name="application-gateway-waf-reports"></a>WAF-övervakning

Det är viktigt att övervaka hälsotillståndet för Application Gateway. Övervakningen av hälsotillståndet för brandväggen för webbaserade program och de program som den skyddar sker via loggning och integrering med Azure Monitor, Azure Security Center och Log Analytics.

![diagnostik](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Alla Application Gateway-loggar är integrerade med [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).  Det gör att du kan spåra diagnostisk information, t.ex. varningar och loggar för brandväggen för webbaserade program.  Den här funktionen hittar du på fliken **Diagnostik** i Application Gateway-resursen på portalen eller direkt i Azure Monitor-tjänsten. Mer information om hur du aktiverar diagnostikloggar för application gateway finns [Application Gateway-diagnostik](application-gateway-diagnostics.md)

#### <a name="azure-security-center"></a>Azure Security Center

Med hjälp av [Azure Security Center](../security-center/security-center-intro.md) kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Funktionen Programgateway [ingår nu i Azure Security Center](application-gateway-integration-security-center.md). Azure Security Center söker igenom din miljö för att identifiera oskyddade webbappar. Vi rekommenderar programgatewayen WAF för att skydda de här känsliga resurserna. Du kan skapa programgatewayen WAF direkt från Azure Security Center.  De här WAF-instanserna är integrerade med Azure Security Center och skickar aviseringar och hälsoinformation tillbaka till Azure Security Center.

![bild 1](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Loggning

Application Gateway WAF erbjuder detaljerad rapportering för varje hot som upptäcks. Loggningen är integrerad med loggar och varningar för Azure Diagnostics och registreras i JSON-format. Dessa loggar kan integreras med [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).

![imageURLroute](./media/waf-overview/waf2.png)

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

Brandväggen för webbaserade program är tillgänglig i en ny WAF SKU. Denna SKU är endast tillgänglig i Azure Resource Manager-etableringsmodellen, inte i den klassiska distributionsmodellen. WAF SKU finns dessutom endast i medelstora och stora application gateway-instansstorlekarna. Alla begränsningar som gäller för Application Gateway gäller även för WAF SKU:n.

Priserna är baserade på avgifter per gatewayinstans per timme och databearbetningsavgifter. Gatewaypriset per timme för WAF SKU skiljer sig från avgifterna för Standard-SKU (se [Priser för Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/)). Databearbetningsavgifterna är desamma. Det finns inte per regel eller regel grupp avgifter. Du kan skydda flera webbprogram bakom samma brandväggen för webbaserade program och du debiteras inte för att stödja flera program.

## <a name="next-steps"></a>Nästa steg

När du har läst om WAF [hur du konfigurerar brandväggen för webbaserade program på Application Gateway](tutorial-restrict-web-traffic-powershell.md).

