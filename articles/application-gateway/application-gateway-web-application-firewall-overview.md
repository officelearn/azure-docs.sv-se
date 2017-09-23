---
title: "Introduktion till brandväggen för webbaserade program (WAF) för Azure Application Gateway | Microsoft Docs"
description: "Den här sidan innehåller en översikt över brandväggen för webbaserade program (WAF) för Application Gateway"
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 04b362bc-6653-4765-86f6-55ee8ec2a0ff
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: amsriva
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 50863b60f2843f033bdb07f4564d937cd6f0b7be
ms.contentlocale: sv-se
ms.lasthandoff: 06/07/2017

---

# <a name="web-application-firewall-waf"></a>Brandvägg för webbaserade program (WAF)

Brandvägg för webbaserade program (WAF) är en funktion i Application Gateway som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem. 

Brandväggen använder regler från [OWASP Core Rule Sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9. Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

![imageURLroute](./media/application-gateway-web-application-firewall-overview/WAF1.png)

Application Gateway fungerar som en domänkontrollant och erbjuder SSL-avslutning, cookie-baserad sessionstillhörighet, fördelning av resursallokeringsbelastning, innehållsbaserad routning, möjlighet att vara värd för flera webbplatser och säkerhetsförbättringar. Säkerhetsförbättringarna som erbjuds av Application Gateway inkluderar SSL-principhantering och SSL-support för slutpunkt till slutpunkt. Programsäkerheten har förstärkts genom att WAF är direkt integrerad i ADC. Den utgör ett enkelt sätt att konfigurera en central plats för att hantera dina webbprogram och skydda dem mot vanliga säkerhetsrisker.

## <a name="benefits"></a>Fördelar

Viktiga fördelar med Application Gateway och brandväggen för webbaserade program:

### <a name="protection"></a>Skydd

* Skyddar ditt webbprogram från säkerhetsrisker och attacker utan att du behöver ändra koden i serverdelen.

* Skyddar flera webbprogram samtidigt bakom en programgateway. Application Gateway har stöd för upp till 20 webbsidor bakom en enda gateway som skyddar dem alla från webbattacker med hjälp av brandväggen för webbaserade program.

### <a name="monitoring"></a>Övervakning

* Övervaka om ditt webbprogram utsätts för angrepp med hjälp av en realtidslogg för brandväggen för webbaserade program. Den här loggen är integrerad med [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) så att du kan spåra varningar och loggar för brandväggen för webbaserade program och enkelt övervaka trender.

* Brandväggen för webbaserade program kommer snart att integreras med Azure Security Center. I Azure Security Center finns en central vy som visar säkerhetsläget för alla dina Azure-resurser.

### <a name="customization"></a>Anpassning

* Du kan anpassa regler och regelgrupper för brandväggen så att de passar behoven för din app och eliminerar falska positiva identifieringar.

## <a name="features"></a>Funktioner

Brandväggen för webbaserade program är förkonfigurerad med CRS 3.0 som standard. Du kan också välja att använda 2.2.9. I CRS 3.0 finns bättre möjligheter att eliminera falska positiva identifieringar än i 2.2.9. Du kan [anpassa regler så att de passar dina behov](application-gateway-customize-waf-rules-portal.md). Några vanliga säkerhetshot som brandväggen för webbaserade program skyddar mot:

* Skydd mot SQL-inmatning
* Skydd mot skriptkörning över flera webbplatser
* Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil
* Skydd mot åtgärder som inte följer HTTP-protokollet
* Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas
* Skydd mot robotar, crawlers och skannrar
* Identifiering av vanliga felkonfigureringar i program (t.ex. Apache, IIS osv.)

En detaljerad lista över regler och deras skydd finns följande [Core Rule Sets](#core-rule-sets).

### <a name="core-rule-sets"></a>Core Rule Sets

Application Gateway stöder två regeluppsättningar: CRS 3.0 och CRS 2.2.9. Dessa Core Rule Sets är regelsamlingar som skyddar dina webbprogram mot skadlig aktivitet.

#### <a name="owasp30"></a>OWASP_3.0

Core Rule Set 3.0 har 13 regelgrupper (se följande tabell). Var och en av dessa regelgrupper innehåller flera regler. Dessa kan inaktiveras.

|RuleGroup|Beskrivning|
|---|---|
|**[REQUEST-910-IP-REPUTATION](application-gateway-crs-rulegroups-rules.md#crs910)**|Innehåller regler för att skydda mot kända skräppostavsändare eller skadlig aktivitet.|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Innehåller regler för att låsa metoder (PUT, PATCH< ..)|
|**[REQUEST-912-DOS-PROTECTION](application-gateway-crs-rulegroups-rules.md#crs912)**| Innehåller regler för att skydda mot DoS-attacker.|
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

* **Identifieringsläge** – När Application Gateway WAF körs i identifieringsläge övervakas och loggas alla hotvarningar i en loggfil. Loggningsdiagnostik för Application Gateway bör aktiveras i avsnittet **Diagnostik**. Du måste också säkerställa att WAF-loggen har valts och är aktiverad. Vid körning i identifieringsläge blockerar brandväggen för webbaserade program inte inkommande begäranden.
* **Förhindringsläge** – När Application Gateway har konfigurerats för att köras i förhindringsläge blockerar den aktivt intrång och attacker som identifieras enligt reglerna för den. Attackeraren erhåller undantag 403 för obehörig åtkomst och anslutningen avbryts. Förhindringsläget fortsätter logga sådana attacker i WAF-loggarna.

### <a name="application-gateway-waf-reports"></a>WAF-övervakning

Det är viktigt att övervaka hälsotillståndet för Application Gateway. Övervakningen av hälsotillståndet för brandväggen för webbaserade program och de program som den skyddar sker via loggning och integrering med Azure Monitor, Azure Security Center (kommer snart) och Log Analytics.

![diagnostik](./media/application-gateway-web-application-firewall-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Alla Application Gateway-loggar är integrerade med [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).  Det gör att du kan spåra diagnostisk information, t.ex. varningar och loggar för brandväggen för webbaserade program.  Den här funktionen hittar du på fliken **Diagnostik** i Application Gateway-resursen på portalen eller direkt i Azure Monitor-tjänsten. Mer information om hur du aktiverar diagnostikloggar för Application Gateway finns på sidan om [Application Gateway-diagnostik](application-gateway-diagnostics.md) (på engelska)

#### <a name="azure-security-center"></a>Azure Security Center

Med hjälp av [Azure Security Center](../security-center/security-center-intro.md) kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Funktionen Programgateway [ingår nu i Azure Security Center](application-gateway-integration-security-center.md). Azure Security Center söker igenom din miljö för att identifiera oskyddade webbappar. Vi rekommenderar programgatewayen WAF för att skydda de här känsliga resurserna. Du kan skapa programgatewayen WAF direkt från Azure Security Center.  De här WAF-instanserna är integrerade med Azure Security Center och skickar aviseringar och hälsoinformation tillbaka till Azure Security Center.

![bild 1](./media/application-gateway-web-application-firewall-overview/figure1.png)

#### <a name="logging"></a>Loggning

Application Gateway WAF erbjuder detaljerad rapportering för varje hot som upptäcks. Loggningen är integrerad med loggar och varningar för Azure Diagnostics och registreras i JSON-format. Dessa loggar kan integreras med [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).

![imageURLroute](./media/application-gateway-web-application-firewall-overview/waf2.png)

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

Brandväggen för webbaserade program är tillgänglig i en ny WAF SKU. Denna SKU är endast tillgänglig i Azure Resource Manager-etableringsmodellen, inte i den klassiska distributionsmodellen. WAF SKU finns dessutom endast i Application Gateway-instansstorlekarna medel och stor. Alla begränsningar som gäller för Application Gateway gäller även för WAF SKU:n. Priserna är baserade på avgifter per gatewayinstans per timme och databearbetningsavgifter. Gatewaypriset per timme för WAF SKU skiljer sig från avgifterna för Standard-SKU (se [Priser för Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/)). Databearbetningsavgifterna är desamma. Avgifter per regel eller per regelgrupp finns inte. Du kan skydda flera webbprogram bakom samma brandvägg för webbaserade program utan extra avgifter. 

Faktureringen för WAF börjar gälla från 5 maj 2017. Till dess debiteras WAF SKU-gateways enligt standardpriserna.

## <a name="next-steps"></a>Nästa steg

När du har lärt dig mer om funktionerna i WAF går du vidare till [Konfigurera brandväggen för webbaserade program på Application Gateway](application-gateway-web-application-firewall-portal.md).


