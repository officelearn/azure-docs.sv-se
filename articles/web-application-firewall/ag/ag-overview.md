---
title: Introduktion till Brandvägg för Azure Web Application
titleSuffix: Azure Web Application Firewall
description: Den här artikeln innehåller en översikt över brandväggen för webbprogram (WAF) på Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 1a210ff7da57147762dcf13e1dda7fee26dbe5f0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74384118"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway"></a>Azure Web Application-brandväggen på Azure Application Gateway

Azure Web Application Firewall (WAF) på Azure Application Gateway ger centraliserat skydd av dina webbprogram från vanliga kryphål och sårbarheter. Webbprogram blir alltmer inriktade på skadliga attacker som utnyttjar allmänt kända sårbarheter. SQL-injektion och skript över flera webbplatser är några av de vanligaste attackerna.

WAF on Application Gateway baseras på [CRS (Core Rule Set)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1, 3.0 eller 2.2.9 från OWASP (Open Web Application Security Project). WAF uppdateras automatiskt för att inkludera skydd mot nya säkerhetsproblem, utan ytterligare konfiguration som behövs. 

Alla WAF funktioner som anges nedan finns inuti en WAF Policy. Du kan skapa flera principer och de kan associeras med en programgateway, till enskilda lyssnare eller till sökvägsbaserade routningsregler för en programgateway. På så sätt kan du ha separata principer för varje webbplats bakom programgatewayen om det behövs. Mer information om WAF-principer finns i [Skapa en WAF-princip](create-waf-policy-ag.md).

   > [!NOTE]
   > Waf-principer per plats och per URI finns i offentlig förhandsversion. Det innebär att den här funktionen omfattas av Microsofts kompletterande användarvillkor. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![WAF-diagram för programgateway](../media/ag-overview/waf1.png)

Application Gateway fungerar som en application delivery controller (ADC). Det erbjuder SSL-avslutning (Secure Sockets Layer), cookie-baserad sessionstillhörighet, lastdistribution av round-robin, innehållsbaserad routning, möjlighet att vara värd för flera webbplatser och säkerhetsförbättringar.

Säkerhetsförbättringar för Application Gateway inkluderar SSL-principhantering och heltäckande SSL-stöd. Programsäkerheten stärks genom WAF-integrering i Application Gateway. Kombinationen skyddar dina webbprogram mot vanliga sårbarheter. Och det ger en lätt att konfigurera central plats att hantera.

## <a name="benefits"></a>Fördelar

I det här avsnittet beskrivs de grundläggande fördelar som WAF på Application Gateway tillhandahåller.

### <a name="protection"></a>Skydd

* Skydda dina webbprogram från webbsår och attacker utan ändringar av backend-kod.

* Skydda flera webbprogram samtidigt. En instans av Application Gateway kan vara värd för upp till 40 webbplatser som skyddas av en brandvägg för webbprogram.

* Skapa anpassade WAF-principer för olika platser bakom samma WAF 

* Skydda dina webbprogram från skadliga robotar med IP Reputation-regeluppsättningen (förhandsgranskning)

### <a name="monitoring"></a>Övervakning

* Övervaka attacker mot dina webbprogram med hjälp av en WAF-logg i realtid. Loggen är integrerad med [Azure Monitor](../../azure-monitor/overview.md) för att spåra WAF-aviseringar och enkelt övervaka trender.

* Application Gateway WAF är integrerat med Azure Security Center. Security Center ger en central vy över säkerhetstillståndet för alla dina Azure-resurser.

### <a name="customization"></a>Anpassning

* Anpassa WAF-regler och regelgrupper så att de passar dina programkrav och eliminera falska positiva identifieringar.

* Associera en WAF-princip för varje plats bakom din WAF för att möjliggöra platsspecifik konfiguration

* Skapa anpassade regler som passar behoven i ditt program

## <a name="features"></a>Funktioner

- SQL-injektionsskydd.
- Skriptskydd över flera webbplatser.
- Skydd mot andra vanliga webbattacker, till exempel kommandoinjektion, HTTP-begäransmuggling, HTTP-svarsdelning och fjärrfilinkludering.
- Skydd mot HTTP-protokollöverträdelser.
- Skydd mot HTTP-protokollavvikelser, till exempel saknad värdanvändaragent och acceptera rubriker.
- Skydd mot sökrobotar och skannrar.
- Identifiering av vanliga programmisskonfigurationer (till exempel Apache och IIS).
- Konfigurerbara storleksgränser för begäran med nedre och övre gränser.
- Med undantagslistor kan du utelämna vissa begärandeattribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory-infogade token som används för autentiserings- eller lösenordsfält.
- Skapa anpassade regler som passar de specifika behoven i dina program.
- Geofiltertrafik för att tillåta eller blockera vissa länder från att få åtkomst till dina program. (förhandsversion)
- Skydda dina program från robotar med bot mitigation ruleset. (förhandsversion)

## <a name="waf-policy"></a>WAF-policy

Om du vill aktivera en brandvägg för webbprogram i en programgateway måste du skapa en WAF-princip. Den här principen är där alla hanterade regler, anpassade regler, undantag och andra anpassningar, till exempel filöverföringsgränsen, finns. 

### <a name="core-rule-sets"></a>Core Rule Sets

Application Gateway stöder tre regeluppsättningar: CRS 3.1, CRS 3.0 och CRS 2.2.9. Dessa regler skyddar dina webbprogram från skadlig aktivitet.

Mer information finns i [CRS-regelgrupper och regler för crs-regelgrupper och regler för webbprogram brandvägg.](application-gateway-crs-rulegroups-rules.md)

### <a name="custom-rules"></a>Anpassade regler

Application Gateway stöder också anpassade regler. Med anpassade regler kan du skapa egna regler som utvärderas för varje begäran som passerar genom WAF. Dessa regler har högre prioritet än resten av reglerna i de hanterade regeluppsättningarna. Om en uppsättning villkor är uppfyllda vidtas en åtgärd för att tillåta eller blockera. 

Geomatch-operatorn är nu tillgänglig i offentlig förhandsversion för anpassade regler. Se anpassade regler för [geomatch](custom-waf-rules-overview.md#geomatch-custom-rules-preview) för mer information.

> [!NOTE]
> Geomatch-operatorn för anpassade regler är för närvarande i offentlig förhandsversion och har ett förhandsversionsavtal på tjänstnivå. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mer information om anpassade regler finns i [Anpassade regler för application gateway.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Bot Mitigation (förhandsgranskning)

En hanterad bot-regeluppsättning kan aktiveras för att WAF ska kunna blockera eller logga begäranden från kända skadliga IP-adresser, tillsammans med den hanterade regeluppsättningen. IP-adresserna kommer från Microsoft Threat Intelligence-feeden. Intelligent Security Graph driver Microsofts hotinformation och används av flera tjänster, inklusive Azure Security Center.

> [!NOTE]
> Bot protection regeluppsättning är för närvarande i offentlig förhandsversion och är försedd med en förhandsvisning servicenivå avtal. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om Bot Protection är aktiverat, inkommande begäranden som matchar Malicious Bot klient-IPs loggas i brandväggsloggen, se mer information nedan. Du kan komma åt WAF-loggar från lagringskonto, händelsehubben eller logganalys. 

### <a name="waf-modes"></a>WAF-lägen

Application Gateway WAF kan konfigureras för att köras i följande två lägen:

* **Identifieringsläge**: Övervakar och loggar alla hotvarningar. Du aktiverar loggningsdiagnostik för Application Gateway i avsnittet **Diagnostik.** Du måste också se till att WAF-loggen är markerad och aktiverad. Brandväggen för webbprogram blockerar inte inkommande begäranden när den körs i identifieringsläge.
* **Förebyggande läge**: Blockerar intrång och attacker som reglerna upptäcker. Angriparen får ett undantag för "403 obehörig åtkomst" och anslutningen stängs. Förebyggande läge registrerar sådana attacker i WAF-loggarna.

> [!NOTE]
> Vi rekommenderar att du kör en nyligen distribuerad WAF i identifieringsläge under en kort tidsperiod i en produktionsmiljö. Detta ger möjlighet att hämta [brandväggsloggar](../../application-gateway/application-gateway-diagnostics.md#firewall-log) och uppdatera eventuella undantag eller [anpassade regler](./custom-waf-rules-overview.md) före övergången till förebyggande läge. Detta kan bidra till att minska förekomsten av oväntade blockerad trafik.

### <a name="anomaly-scoring-mode"></a>Läget Avvikelsebedömning

OWASP har två lägen för att avgöra om trafik ska blockeras: Traditionellt läge och läget Avvikelsebedömning.

I traditionellt läge betraktas trafik som matchar alla regler oberoende av andra regelmatchningar. Det här läget är lätt att förstå. Men bristen på information om hur många regler som matchar en viss begäran är en begränsning. Så, Avvikelse scoring läge infördes. Det är standard för OWASP 3. *x*.

I läget Avvikelsebedömning blockeras inte trafiken som matchar alla regler omedelbart när brandväggen är i förebyggande läge. Reglerna har en viss allvarlighetsgrad: *Kritisk,* *Fel*, *Varning*eller *Meddelande*. Allvarlighetsgraden påverkar ett numeriskt värde för begäran, som kallas avvikelsepoäng. Till exempel bidrar en *varningsregelmatchning* 3 till poängen. En *Kritisk* härskar match bidrar 5.

|Severity  |Värde  |
|---------|---------|
|Kritisk     |5|
|Fel        |4|
|Varning      |3|
|anslagstavlan,       |2|

Det finns en tröskel på 5 för avvikelsepoängen att blockera trafiken. Så, en enda *kritisk* regel matchning är tillräckligt för ansökan Gateway WAF att blockera en begäran, även i förebyggande läge. Men en *varningsregelmatchning* ökar bara avvikelsepoängen med 3, vilket inte räcker i sig för att blockera trafiken.

> [!NOTE]
> Meddelandet som loggas när en WAF-regel matchar trafiken innehåller åtgärdsvärdet "Blockerad". Men trafiken är faktiskt bara blockeras för en Anomali Score på 5 eller högre.  

### <a name="waf-monitoring"></a>WAF övervakning

Det är viktigt att övervaka hälsotillståndet för Application Gateway. Övervakning av hälsotillståndet för din WAF och de program som skyddas stöds av integrering med Azure Security Center-, Azure Monitor- och Azure Monitor-loggar.

![Diagram över WAF-diagnostik för application gateway](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Application Gateway-loggar är integrerade med [Azure Monitor](../../azure-monitor/overview.md). På så sätt kan du spåra diagnostikinformation, inklusive WAF-aviseringar och loggar. Du kan komma åt den här funktionen på fliken **Diagnostik** i Application Gateway-resursen i portalen eller direkt via Azure Monitor. Mer information om hur du aktiverar loggar finns i [Diagnostik för Programgateway](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-intro.md) hjälper dig att förebygga, upptäcka och svara på hot. Det ger ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Application Gateway är [integrerat med Security Center](../../application-gateway/application-gateway-integration-security-center.md). Security Center söker igenom din miljö för att identifiera oskyddade webbprogram. Det kan rekommendera Application Gateway WAF för att skydda dessa sårbara resurser. Du skapar brandväggarna direkt från Security Center. Dessa WAF-instanser är integrerade med Security Center. De skickar aviseringar och hälsoinformation till Security Center för rapportering.

![Översiktsfönster för Säkerhetscenter](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel är en skalbar, molnbaserad, säkerhetsinformation händelsehantering (SIEM) och säkerhet orkestrering automatiserad svar (SOAR) lösning. Azure Sentinel levererar intelligent säkerhetsanalys och hotinformation i hela företaget, vilket ger en enda lösning för varningsidentifiering, hotsynlighet, proaktiv jakt och hothantering.

Med den inbyggda Azure WAF-brandväggshändelserarbetsboken kan du få en översikt över säkerhetshändelserna på din WAF. Detta inkluderar händelser, matchade och blockerade regler och allt annat som loggas i brandväggsloggarna. Se mer om loggning nedan. 


![Sentinel](../media/ag-overview/sentinel.png)

#### <a name="logging"></a>Loggning

Application Gateway WAF ger detaljerad rapportering om varje hot som identifieras. Loggning är integrerat med Azure Diagnostics-loggar. Aviseringar registreras i .json-format. Dessa loggar kan integreras med [Azure Monitor-loggar](../../azure-monitor/insights/azure-networking-analytics.md).

![Diagnostikloggar i diagnostikloggar för Application Gateway](../media/ag-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.145",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343"
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/globalWafPolicy",
      "policyScope": "Global",
      "policyScopeName": " Global "
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Priser för WAF SKU för programgatewayen

Prismodellerna är olika för WAF_v1 och WAF_v2 SKU:er. Läs [prissidan för Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) om du vill veta mer. 

## <a name="next-steps"></a>Nästa steg

- Komma igång [genom att skapa en WAF-princip](create-waf-policy-ag.md)
- Läs mer om [WAF-hanterade regler](application-gateway-crs-rulegroups-rules.md)
- Läs mer om [anpassade regler](custom-waf-rules-overview.md)
- Lär dig mer om [brandvägg för webbprogram på Azure Front Door](../afds/afds-overview.md)

