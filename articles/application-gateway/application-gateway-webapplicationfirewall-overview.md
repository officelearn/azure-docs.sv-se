---
title: "Introduktion till Brandvägg för webbaserade program (WAF) för Application Gateway | Microsoft Docs"
description: "Den här sidan innehåller en översikt över Brandvägg för webbaserade program för Application Gateway "
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
ms.date: 12/16/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: ce40a93372205a4b7c6b0c753ebf30c2b3d51d7a
ms.openlocfilehash: 2d8e851810b4ec5b7ae3f3b17c4aee11d7e51de4


---

# <a name="application-gateway-web-application-firewall-preview"></a>Brandvägg för webbaserade Application Gateway-program (förhandsgranskning)

Brandvägg för webbaserade program (WAF) är en funktion för Azure Application Gateway som ger skydd till webbprogram som utnyttjar programgateway för standard ADC-funktioner (Application Delivery Control). Brandvägg för webbaserade program gör detta genom att skydda dem mot de flesta av de 10 vanligaste OWASP-säkerhetsproblemen. Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin. En centraliserad brandvägg mot webbattacker underlättar säkerhetshanteringen och håller programmet bättre skyddat mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en programgateway med brandvägg för webbprogram.

![imageURLroute](./media/application-gateway-webapplicationfirewall-overview/WAF1.png)

Application Gateway fungerar som en domänkontrollant och erbjuder SSL-avslutning, cookie-baserad sessionstillhörighet, fördelning av resursallokeringsbelastning, innehållsbaserad routning, möjlighet att vara värd för flera webbplatser och säkerhetsförbättringar. Säkerhetsförbättringarna som erbjuds av Application Gateway inkluderar SSL-principhantering och SSL-support för slutpunkt till slutpunkt. Vi förstärker programsäkerhetsfunktionerna i vår tjänst genom att introducera brandväggen för webbprogram (WAF) och direkt integrera den i ADC-erbjudandet. Den utgör ett enkelt sätt att konfigurera en central plats för att hantera dina webbprogram och skydda dem mot vanliga säkerhetsrisker.

Konfigurering av WAF vid programgatewayen ger följande fördelar:

* Skyddar ditt webbprogram från säkerhetsrisker och attacker utan att du behöver ändra koden i serverdelen.
* Skyddar flera webbprogram samtidigt bakom en programgateway. Application Gateway har stöd för att vara värd för upp till 20 webbsidor bakom en enda gateway som skyddar dem alla från webbattacker.
* Övervaka ditt webbprogram för att förhindra attacker, med hjälp av realtidsrapporter som genereras av gatewayens WAF-loggar.
* Vissa kompatibilitetskontroller kräver att alla slutpunkter som är internetanslutna ska skyddas av en WAF-lösning. Genom att använda programgatewayen med WAF aktiverat kan du uppfylla dessa kompatibilitetskrav.

## <a name="overview"></a>Översikt

Application Gateway WAF erbjuds i en ny SKU (WAF SKU) och levereras förinställd med ModSecurity och OWASP Core Rule Set för att ge skydd vid baslinjen mot de flesta av OWASP:s 10 vanligaste webbsäkerhetsproblem.

* Skydd mot SQL-inmatning
* Skydd mot skriptkörning över flera webbplatser
* Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil
* Skydd mot åtgärder som inte följer HTTP-protokollet
* Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas
* Skydd mot robotar, crawlers och skannrar
* Identifiering av vanliga felkonfigureringar i program (t.ex. Apache, IIS osv.)

## <a name="waf-modes"></a>WAF-lägen

Application Gateway WAF kan konfigureras för att köras i följande två lägen:

* **Identifieringsläge** – När Application Gateway WAF körs i identifieringsläge övervakas och loggas alla hotvarningar i en loggfil. Du måste säkerställa att loggningsdiagnostik för Application Gateway har aktiverats i diagnostikavsnittet. Du måste också säkerställa att WAF-loggen har valts och är aktiverad.
* **Förhindringsläge** – När Application Gateway har konfigurerats för att köras i förhindringsläge blockerar den aktivt intrång och attacker som identifieras enligt reglerna för den. Attackeraren erhåller undantag 403 för obehörig åtkomst och anslutningen avbryts. Förhindringsläget fortsätter logga sådana attacker i WAF-loggarna.

## <a name="application-gateway-waf-reports"></a>Application Gateway WAF-rapporter

Application Gateway WAF erbjuder detaljerad rapportering för varje hot som upptäcks. Loggning integreras med Azure-diagnostikloggar och varningar registreras i JSON-format.

![imageURLroute](./media/application-gateway-webapplicationfirewall-overview/waf2.png)

```json
{
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
    "operationName": "ApplicationGatewayFirewall",
    "time": "2016-09-20T00:40:04.9138513Z",
    "category": "ApplicationGatewayFirewallLog",
    "properties":     {
        "instanceId":"ApplicationGatewayRole_IN_0",
        "clientIp":"108.41.16.164",
        "clientPort":1815,
        "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
        "ruleId":"OWASP_973336",
        "message":"XSS Filter - Category 1: Script Tag Vector",
        "action":"Logged",
        "site":"Global",
        "message":"XSS Filter - Category 1: Script Tag Vector",
        "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
}
```

## <a name="application-gateway-waf-sku-pricing"></a>Priser för WAF SKU för programgatewayen

Under förhandsgranskningen tillkommer inga extra kostnader för att använda Application Gateway WAF. Du debiteras fortsättningsvis enligt gällande avgifter för Basic SKU. WAF SKU-kostnaderna kommuniceras när den är tillgänglig för allmänheten. För kunder som har valt att distribuera Application Gateway i WAF SKU börjar kostnader genereras för WAF SKU först efter att den gjorts tillgänglig för allmänheten.

## <a name="next-steps"></a>Nästa steg

När du har lärt dig mer om funktionerna i WAF går du vidare till [Konfigurera brandväggen för webbaserade program på Application Gateway](application-gateway-web-application-firewall-portal.md).




<!--HONumber=Jan17_HO1-->


