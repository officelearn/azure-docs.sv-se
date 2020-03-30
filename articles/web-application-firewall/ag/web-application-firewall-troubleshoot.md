---
title: Felsöka – Brandvägg för Azure-webbprogram
description: Den här artikeln innehåller felsökningsinformation för brandvägg för webbprogram (WAF) för Azure Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046199"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Felsöka brandvägg för webbprogram (WAF) för Azure Application Gateway

Det finns några saker du kan göra om förfrågningar som ska passera genom din brandvägg för webbprogram (WAF) är blockerade.

Kontrollera först att du har läst [WAF-översikten](ag-overview.md) och [WAF-konfigurationsdokumenten.](application-gateway-waf-configuration.md) Se också till att du har aktiverat [WAF-övervakning](../../application-gateway/application-gateway-diagnostics.md) Dessa artiklar förklarar hur WAF fungerar, hur WAF-regeln anger fungerar och hur du kommer åt WAF-loggar.

## <a name="understanding-waf-logs"></a>Förstå WAF-loggar

Syftet med WAF-loggar är att visa varje begäran som matchas eller blockeras av WAF. Det är en redovisning för alla utvärderade begäranden som matchas eller blockeras. Om du märker att WAF blockerar en begäran om att den inte ska (ett falskt positivt) kan du göra några saker. Först, begränsa och hitta den specifika begäran. Titta igenom loggarna för att hitta den specifika URI, tidsstämpel eller transaktions-ID för begäran. När du hittar de associerade loggposterna kan du börja agera på de falska positiva.

Anta till exempel att du har en legitim trafik som innehåller strängen *1=1* som du vill skicka genom din WAF. Om du försöker med begäran blockerar WAF trafik som innehåller *din 1=1-sträng* i alla parametrar eller fält. Detta är en sträng som ofta förknippas med en SQL-injektionsattack. Du kan titta igenom loggarna och se tidsstämpeln för begäran och de regler som blockerade/matchade.

I följande exempel kan du se att fyra regler utlöses under samma begäran (med fältet TransactionId). Den första säger att det matchade eftersom användaren använde en numerisk / IP-URL för begäran, vilket ökar avvikelsepoängen med tre eftersom det är en varning. Nästa regel som matchade är 942130, vilket är den du letar efter. Du kan se *fältet 1=1.* `details.data` Detta ytterligare ökar anomali poäng med tre igen, eftersom det är också en varning. I allmänhet ökar varje regel som har åtgärden **Matchad** avvikelsepoängen, och vid denna tidpunkt skulle avvikelsepoängen vara sex. Mer information finns i [Bedömningsläge för avvikelse](ag-overview.md#anomaly-scoring-mode).

De två sista loggposterna visar att begäran blockerades eftersom avvikelsepoängen var tillräckligt hög. Dessa poster har en annan åtgärd än de andra två. De visar att de faktiskt *blockerade* begäran. Dessa regler är obligatoriska och kan inte inaktiveras. De bör inte ses som regler, men mer som kärninfrastruktur i WAF interna.

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

## <a name="fixing-false-positives"></a>Fastställande av falska positiva

Med den här informationen, och vetskapen om att regel 942130 är den som matchade *1 = 1* strängen, kan du göra några saker för att stoppa detta från att blockera din trafik:

- Använda en undantagslista

   Mer information om undantagslistor finns i [WAF-konfigurationen.](application-gateway-waf-configuration.md#waf-exclusion-lists)
- Inaktivera regeln.

### <a name="using-an-exclusion-list"></a>Använda en undantagslista

För att fatta ett välgrundat beslut om att hantera ett falskt positivt är det viktigt att du bekantar dig med den teknik som ditt program använder. Anta till exempel att det inte finns en SQL-server i teknikstacken och att du får falska positiva identifieringar relaterade till dessa regler. Om du inaktiverar dessa regler försvagas inte nödvändigtvis din säkerhet.

En fördel med att använda en undantagslista är att endast en viss del av en begäran inaktiveras. Detta innebär dock att en specifik uteslutning är tillämplig på all trafik som passerar genom din WAF eftersom det är en global inställning. Detta kan till exempel leda till ett problem om *1=1* är en giltig begäran i brödtexten för en viss app, men inte för andra. En annan fördel är att du kan välja mellan brödtext, rubriker och cookies som ska uteslutas om ett visst villkor är uppfyllt, i motsats till att utesluta hela begäran.

Ibland finns det fall där specifika parametrar skickas till WAF på ett sätt som kanske inte är intuitivt. Det finns till exempel en token som skickas när du autentiserar med Azure Active Directory. Denna token, *__RequestVerificationToken*, skickas vanligtvis in som en begäran cookie. Men i vissa fall där cookies är inaktiverade skickas den här token också som ett begäranattribut eller "arg". Om detta inträffar måste du se till att *__RequestVerificationToken* läggs till i undantagslistan som **ett attributnamn för begäran.**

![Undantag](../media/web-application-firewall-troubleshoot/exclusion-list.png)

I det här exemplet vill du utesluta **attributet Request-namn** som är lika med *text1*. Detta är uppenbart eftersom du kan se attributnamnet i brandväggsloggarna: **data: Matchade data: 1=1 finns i ARGS:text1: 1=1**. Attributet är **text1**. Du kan också hitta det här attributnamnet på några andra sätt, se [Hitta attributnamn för begäran](#finding-request-attribute-names).

![UNDANTAGSLISTOR FÖR WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Inaktivera regler

Ett annat sätt att komma runt ett falskt positivt är att inaktivera regeln som matchade på indata WAF trodde var skadligt. Eftersom du har tolkat WAF-loggarna och har begränsat regeln till 942130 kan du inaktivera den i Azure-portalen. Se [Anpassa brandväggsregler för webbprogram via Azure-portalen](application-gateway-customize-waf-rules-portal.md).

En fördel med att inaktivera en regel är att om du vet att all trafik som innehåller ett visst villkor som normalt kommer att blockeras är giltig trafik, kan du inaktivera den regeln för hela WAF. Men om det bara är giltig trafik i ett specifikt användningsfall öppnar du ett säkerhetsproblem genom att inaktivera den regeln för hela WAF eftersom det är en global inställning.

Om du vill använda Azure PowerShell läser du [Anpassa brandväggsregler för webbprogram via PowerShell](application-gateway-customize-waf-rules-powershell.md). Om du vill använda Azure CLI läser du [Anpassa brandväggsregler för webbprogram via Azure CLI](application-gateway-customize-waf-rules-cli.md).

![WAF-regler](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Söka efter attributnamn för begäran

Med hjälp av [Fiddler inspekterar](https://www.telerik.com/fiddler)du enskilda förfrågningar och bestämmer vilka specifika fält på en webbsida som kallas. Detta kan bidra till att utesluta vissa fält från inspektion med undantagslistor.

I det här exemplet kan du se att fältet där *1=1-strängen* angavs kallas **text1**.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

Det här är ett fält som du kan utesluta. Mer information om undantagslistor finns i [Storleksbegränsningar för webbprogramsbrandväggen och undantagslistor](application-gateway-waf-configuration.md#waf-exclusion-lists). Du kan utesluta utvärderingen i det här fallet genom att konfigurera följande undantag:

![UNDANTAG FRÅN WAF](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Du kan också undersöka brandväggsloggarna för att få information för att se vad du behöver lägga till i undantagslistan. Om du vill aktivera loggning finns i [Backend-hälsa, diagnostikloggar och mått för Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

Undersök brandväggsloggen och visa filen PT1H.json under den timme som begäran som du vill granska inträffade.

I det här exemplet kan du se att du har fyra regler med samma TransactionID och att alla inträffade samtidigt:

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

Med din kunskap om hur CRS-regeln anger fungerar och att CRS-regeluppsättningen 3.0 fungerar med ett avvikelsebedömningssystem (se [Webbprogrambrandvägg för Azure Application Gateway)](ag-overview.md)vet du att de två nedersta reglerna med **åtgärden: Blockerad** egenskap blockerar baserat på den totala avvikelsepoängen. Reglerna att fokusera på är de två bästa.

Den första posten loggas eftersom användaren använde en numerisk IP-adress för att navigera till Application Gateway, som kan ignoreras i det här fallet.

Den andra (regel 942130) är den intressanta. Du kan se i informationen att det matchade ett mönster (1=1) och fältet heter **text1**. Följ samma tidigare steg för att utesluta det **begärandeattributnamn** som **är lika med** **1=1**.

## <a name="finding-request-header-names"></a>Söka efter sidnamn för begäran

Fiddler är ett användbart verktyg igen för att hitta namn på begäranhuvud. I följande skärmbild kan du se rubrikerna för den här GET-begäran, som inkluderar *Content-Type*, *User-Agent*och så vidare.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

Ett annat sätt att visa begäran och svar rubriker är att titta inuti utvecklarverktygen i Chrome. Du kan trycka på F12 eller högerklicka på -> **Inspektera** -> **utvecklarverktyg**och välja fliken **Nätverk.** Läs in en webbsida och klicka på den begäran som du vill granska.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Hitta cookienamn för begäran

Om begäran innehåller cookies kan fliken **Cookies** väljas för att visa dem i Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Begränsa globala parametrar för att eliminera falska positiva identifieringar

- Inaktivera kroppskontroll för begäran

   Genom att ställa **in Inspektera begäran kroppen** att av, begäran organ för all trafik kommer inte att utvärderas av din WAF. Detta kan vara användbart om du vet att begäran organ inte är skadliga för ditt program.

   Genom att inaktivera det här alternativet inspekteras inte endast begärandeorganet. Rubrikerna och cookies förblir inspekterade, såvida inte enskilda är undantagna med hjälp av funktionen för undantagslistan.

- Begränsningar för filstorlek

   Genom att begränsa filstorleken för din WAF begränsar du risken för att en attack ska inträffa på dina webbservrar. Genom att tillåta stora filer som ska laddas upp, risken för din backend att bli överväldigad ökar. Att begränsa filstorleken till ett normalt användningsfall för ditt program är bara ett annat sätt att förhindra attacker.

   > [!NOTE]
   > Om du vet att din app aldrig behöver någon filuppladdning över en viss storlek kan du begränsa den genom att ange en gräns.

## <a name="firewall-metrics-waf_v1-only"></a>Brandväggsmått (endast WAF_v1)

För brandväggar för v1-webbprogram är följande mått nu tillgängliga i portalen: 

1. Antal blockerade begäranden för webbprogram Brandvägg Antalet antal begäranden som har blockerats
2. Webbprogram brandvägg blockerade regel räkna alla regler som matchades **och** begäran blockerades
3. Total regeldistribution av brandväggen för webbprogram Alla regler som matchades under utvärderingen
     
Om du vill aktivera mått markerar du fliken **Mått** i portalen och väljer ett av de tre måtten.

## <a name="next-steps"></a>Nästa steg

Se [Så här konfigurerar du brandväggen för webbprogram i Application Gateway](tutorial-restrict-web-traffic-powershell.md).
