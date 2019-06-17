---
title: Felsökning av Brandvägg för webbaserade program för Azure Application Gateway
description: Den här artikeln innehåller felsökningsinformation för Web Application Firewall (WAF) för Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 6/11/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: b81ffc5769fa5521094734da5d00aab77aa7cd35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082448"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Felsöka Web Application Firewall (WAF) för Azure Application Gateway

Det finns några saker som du kan göra om begäranden som ska skickas via din Web Application Firewall (WAF) är blockerade.

Kontrollera först att du har läst den [WAF översikt](waf-overview.md) och [konfiguration av WAF](application-gateway-waf-configuration.md) dokument. Kontrollera också att du har aktiverat [WAF-övervakning](application-gateway-diagnostics.md) dessa artiklar förklarar hur WAF fungerar, hur WAF regeluppsättningar arbete, och hur du kommer åt WAF-loggar.

## <a name="understanding-waf-logs"></a>Förstå WAF-loggar

Syftet med WAF-loggar är att visa alla begäranden som matchade eller blockeras av WAF. Det är ett transaktionsregister för alla utvärderade begäranden som matchade eller blockeras. Om du märker att WAF blockerar en begäran som den bör inte (en falsk positiv identifiering) kan göra du några saker. Först, begränsa och hitta specifik begäran. Titta igenom loggarna för att hitta specifika URI, tidsstämpel eller transaktionen ID för förfrågan. När du hittar de associerade loggposterna kan börja du att agera på falska positiva identifieringar.

Anta exempelvis att du har en giltig trafik som innehåller strängen *1 = 1* som du vill skicka via WAF. Om du försöker begäran WAF blockerar trafik som innehåller din *1 = 1* sträng i en parameter eller ett fält. Det här är en sträng som ofta som är associerade med ett SQL-angrepp. Du kan titta igenom loggarna och se tidsstämpel för begäran och de regler som blockerats/matchas.

I följande exempel ser du att fyra regler har utlösts under samma begäran (med fältet TransactionId). Den första som säger att den matchade eftersom användaren använde en numerisk/IP-adress URL: en för begäran, vilket ökar avvikelsepoäng med tre eftersom det är en varning. Nästa regel som matchade är 942130, vilket är det du letar efter. Du kan se den *1 = 1* i den `details.data` fält. Detta ökar ytterligare avvikelsepoäng med tre igen, eftersom det är också en varning. I allmänhet varje regel med åtgärden **matchade** ökar avvikelsen poängsätta och nu avvikelsepoäng skulle vara sex. Mer information finns i [Avvikelseidentifiering bedömnings läge](waf-overview.md#anomaly-scoring-mode).

De sista två loggposterna visar begäran har blockerats eftersom avvikelsepoäng var tillräckligt högt. Detta har en annan åtgärd än de andra två. De visar de faktiskt *blockeras* begäran. Dessa regler är obligatoriskt och kan inte inaktiveras. De bör inte betraktas som regler, men mer som grundläggande infrastruktur för systemarkitekturen WAF.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Åtgärda falska positiva identifieringar

Med den här informationen och den kunskap som regeln 942130 är den som matchade den *1 = 1* sträng, kan du göra några saker att stoppa detta inte blockerar trafiken:

- Använda en undantagslista

   Se [konfiguration av WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) för mer information om undantag visas en lista.
- Inaktivera regeln.

### <a name="using-an-exclusion-list"></a>Med hjälp av en undantagslista

För att fatta ett välgrundat beslut om hur du hanterar ett falskt positivt, är det viktigt att bekanta dig med de tekniker som används i ditt program. Anta exempelvis att det inte finns en SQLServer i din stack-teknik och du får falska positiva identifieringar som rör dessa regler. Inaktivera dessa regler inte nödvändigtvis att begränsa din säkerhet.

En fördel med att använda en undantagslista är att endast en del av en begäran är inaktiverad. Detta innebär dock att en specifika undantag gäller för all trafik som passerar genom WAF eftersom det är en global inställning. Exempel: Detta kan leda till ett problem om *1 = 1* är en giltig förfrågan i brödtexten för en viss app, men inte för andra. En annan fördel är att du kan välja mellan brödtext, rubriker och cookies som ska undantas om ett visst villkor är uppfyllt, till skillnad från exklusive hela begäran.

Ibland kan finns det fall där specifika parametrar hämta skickades till WAF på ett sätt som inte kanske intuitivt. Det finns till exempel en token som skickas när du autentiserar med Azure Active Directory. Denna token *__RequestVerificationToken*, vanligtvis hämta skickas som en Cookie för begäran. Men i vissa fall där cookies är inaktiverat kan skickas denna token också som ett attribut för begäran eller ”arg”. Om det händer kan du behöva se till att *__RequestVerificationToken* läggs till i listan över undantag som en **begäran attributnamn** samt.

![Undantag](media/waf-tshoot/exclusion-list.png)

I det här exemplet som du vill undanta den **begäran attributnamn** som är lika med *text1*. Det är tydligt eftersom du kan se attributets namn i brandväggsloggar: **data: Matchade Data: 1 = 1 i ARGS:text1: 1=1**. Attributet är **text1**. Du kan också hitta den här attributnamnet några andra sätt, finns i [att söka efter begäran attributnamn](#finding-request-attribute-names).

![WAF-undantagslistor](media/waf-tshoot/waf-config.png)

### <a name="disabling-rules"></a>Inaktivera regler

Ett annat sätt att komma runt ett falskt positivt är att inaktivera regeln som matchade indata WAF eftersom du tror har skadlig. Eftersom du har parsats WAF-loggar och gjorts smalare regeln ned 942130 kan inaktivera du det i Azure-portalen. Se [anpassa brandväggsregler för webbaserade program via Azure portal](application-gateway-customize-waf-rules-portal.md).

En fördel med att inaktivera en regel är att om du vet att all trafik som innehåller ett visst villkor som blockeras normalt är giltig trafik kan du inaktivera regeln för hela WAF. Men om det är bara giltigt trafik i ett specifikt användningsfall kan öppna du upp en sårbarhet genom att inaktivera regeln för hela WAF eftersom det är en global inställning.

Om du vill använda Azure PowerShell, se [anpassa brandväggsregler för webbaserade program via PowerShell](application-gateway-customize-waf-rules-powershell.md). Om du vill använda Azure CLI, se [anpassa brandväggsregler för webbaserade program via Azure CLI](application-gateway-customize-waf-rules-cli.md).

![WAF-regler](media/waf-tshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Attributnamn för att hitta begäran

Med hjälp av [Fiddler](https://www.telerik.com/fiddler), du inspektera enskilda förfrågningar och avgör vilka specifika fält av en webbsida anropas. Detta kan hjälpa för att undanta vissa fält från inspektion med undantag visas.

I det här exemplet ser du att fältet där den *1 = 1* sträng har angetts kallas **text1**.

![Fiddler](media/waf-tshoot/fiddler-1.png)

Det här är ett fält som du kan utesluta. Läs mer om undantagslistor i [storleksgränser för Web application firewall begäran och undantagslistor](application-gateway-waf-configuration.md#waf-exclusion-lists). Du kan utesluta utvärderingen i det här fallet genom att konfigurera följande undantag:

![WAF-undantag](media/waf-tshoot/waf-exclusion-02.png)

Du kan även undersöka brandväggsloggar för att hämta informationen för att se vad du behöver att lägga till i undantagslistan. För att aktivera loggning, se [backend-hälsotillstånd, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).

Granska brandväggsloggen och visa filen pt1h.JSON för timme som du vill kontrollera begäran inträffade.

I det här exemplet ser du att du har fyra regler med samma TransactionID och att de inträffat exakt samtidigt:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Med dina kunskaper om hur CRS regeluppsättningar arbete och som ruleset för CRS 3.0 som fungerar med en avvikelse bedömning system (se [Brandvägg för webbaserade program för Azure Application Gateway](waf-overview.md)) du vet att ned två regler med den  **åtgärd: Blockerad** egenskapen blockerar utifrån den totala avvikelsepoäng. Regler för att fokusera på finns på överst två.

Den första posten loggas eftersom användaren använde en numerisk IP-adress för att navigera till Application Gateway, som kan ignoreras i det här fallet.

Andra en (regel 942130) är det intressanta. Du kan se i informationen om att det matchar ett mönster (1 = 1) och heter fältet **text1**. Följ samma föregående anvisningar för att undanta den **begär attributets namn** som **är lika med** **1 = 1**.

## <a name="finding-request-header-names"></a>Rubriknamn för att hitta begäran

Fiddler är användbart igen för att hitta begäran rubriknamn. I följande skärmbild ser du de huvuden för den här GET-begäran som innehåller *Content-Type*, *användaragent*och så vidare.

![Fiddler](media/waf-tshoot/fiddler-2.png)

Ett annat sätt att visa sidhuvuden för begäran och svar är att titta på utvecklarverktyg av Chrome. Du kan trycka på F12 eller högerklicka -> **granska** -> **utvecklarverktyg**, och välj den **nätverk** fliken. Läsa in en webbsida och på begäran som du vill granska.

![Chrome F12](media/waf-overview/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Cookie-namn för att hitta begäran

Om begäran innehåller cookies, den **Cookies** fliken kan du välja att visa dem i Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Begränsa globala parametrar för att eliminera falska positiva resultat

- Inaktivera begäran brödtext inspektion

   Genom att ange **granska begärandetexten** av, begärandetext av all trafik utvärderas inte av WAF. Detta kan vara användbart om du vet att begärandetext inte är skadlig för ditt program.

   Genom att inaktivera det här alternativet kontrolleras endast begärandetexten inte. Rubriker och cookies förblir inspekterade, om inte enskilda som är undantagna använda funktionen för listan över undantag.

- Filstorleksbegränsning

   Genom att begränsa filstorleken för din WAF du begränsa risken för en attack som händer till webbservrar. Genom att tillåta att stora filer som ska överföras, ökar risken för serverdelen överbelastas. Begränsa filstorleken till ett vanligt användningsfall för ditt program är bara ett annat sätt att förhindra attacker.

   > [!NOTE]
   > Om du vet att din app aldrig måste alla filuppladdning över en viss storlek, kan du begränsa som genom att ange en gräns.

## <a name="next-steps"></a>Nästa steg

Se [hur du konfigurerar brandväggen för webbaserade program på Application Gateway](tutorial-restrict-web-traffic-powershell.md).
