---
title: Azure Web Application Firewall (WAF) v2 anpassade regler
description: Den här artikeln innehåller en översikt över Web Application Firewall (WAF) v2 anpassade regler i Azure Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: f6ea831771a8ffecfdd4c7c0d6374c16894e25ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164668"
---
# <a name="custom-rules-for-web-application-firewall-v2"></a>Anpassade regler för brandväggen för webbaserade program v2

Azure Application Gateway Web Application Firewall (WAF) v2 levereras med en förkonfigurerad, hanterad plattform regeluppsättning som ger skydd från många olika typer av attacker. Dessa attacker är mellan webbplatser, SQL-inmatning och andra. Om du är en WAF-administratör kan du skriva egen regeluppsättning att utöka den grundläggande regeln (CRS) regler. Reglerna kan blockera eller tillåta begärda trafik baserat på matchar villkoren.

Anpassade regler kan du skapa dina egna regler som utvärderas för varje begäran som skickas via WAF. Dessa regler har högre prioritet än resten av reglerna i hanterade regeluppsättning. Anpassade regler innehåller ett regelnamn, regelprioritet och en matris med matchande villkor. Om dessa villkor är uppfyllda, åtgärd en (för att tillåta eller blockera).

Exempelvis kan du blockera alla förfrågningar från en IP-adress i intervallet 192.168.5.4/24. I den här regeln operatorn är *IPMatch*matchValues är IP-adressintervall (192.168.5.4/24) och åtgärden är att blockera trafiken. Du kan ange regelns namn och prioritet.

Anpassade regler stöder med sammanlagda logik för att göra mer avancerade regler adressen dina säkerhetsbehov. Till exempel (villkor 1 **och** villkor 2) **eller** villkor 3).  Det här exemplet innebär att om villkor 1 **och** villkor 2 är uppfyllda, **eller** om villkor 3 är uppfyllt, WAF ska vidta åtgärden anges i den anpassade regeln.

Olika matchande villkor inom samma regel är alltid beredda med **och**. Till exempel blockerar trafik från en specifik IP-adress, och bara om de använder en viss webbläsare.

Om du vill **eller** två olika villkor, de två villkoren måste finnas i olika regler. Blockera trafik från en specifik IP-adress eller blockera trafik till exempel om de använder en viss webbläsare.

> [!NOTE]
> Det maximala antalet WAF anpassade regler är 100. Mer information om Application Gateway-begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md#application-gateway-limits).

Reguljära uttryck stöds även i anpassade regler, precis som i CRS regeluppsättningar. Exempel på dessa finns exempel 3 och 5 i [brandväggsregler skapa och använda anpassade web application](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Att tillåta och blockera

Tillåta och blockera trafik är enkelt med anpassade regler. Du kan till exempel blockerar all trafik som kommer från ett intervall med IP-adresser. Du kan göra en annan regel som tillåter trafik om förfrågan kommer från en viss webbläsare.

Om du vill tillåta något, kontrollera att den `-Action` parametern är inställd på **Tillåt**. För att blockera något, kontrollera att den `-Action` parametern är inställd på **blockera**.

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

Föregående `$BlockRule` mappar till följande anpassad regel i Azure Resource Manager:

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

Den här anpassade regeln innehåller ett namn, prioritet, en åtgärd och matrisen med matchande villkor som måste uppfyllas för att åtgärden ska äga rum. Se följande fältbeskrivningar ytterligare förklaring av de här fälten. Till exempel anpassade regler, se [brandväggsregler skapa och använda anpassade web application](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Fält för anpassade regler

### <a name="name-optional"></a>[Valfritt] namn

Det här är namnet på regeln. Det här namnet visas i loggarna.

### <a name="priority-required"></a>Prioritet [krävs]

- Anger ordningen som reglerna utvärderas i. Lägst värdet, tidigare utvärderingen av regeln.
-Måste vara unikt bland alla anpassade regler. En regel med prioritet 100 utvärderas före en regel med prioritet 200.

### <a name="rule-type-required"></a>Regeltypen [krävs]

För närvarande måste vara **MatchRule**.

### <a name="match-variable-required"></a>Matchar variabeln [krävs]

Måste vara något av variabler:

- RemoteAddr – IP-adress/värdnamnet för fjärrdatorn anslutningen
- RequestMethod – HTTP-begäran-metoden (GET, POST, PUT, DELETE och så vidare.)
- QueryString-variabel i URI: N
- PostArgs – argumenten skickas i meddelandetexten INLÄGG
- RequestUri – URI för begäran
- RequestHeaders – sidhuvuden för begäran
- RequestBody – begäran
- RequestCookies – Cookies för begäran

### <a name="selector-optional"></a>[Valfritt] väljare

Beskriver fältet i samlingen matchVariable. Till exempel om matchVariable RequestHeaders, Väljaren kan vara på den *användaragent* rubrik.

### <a name="operator-required"></a>Operatorn [krävs]

Måste vara något av följande operatorer:

- IPMatch - endast användas när matchar variabeln är *RemoteAddr*
- Är lika med – indata är samma som MatchValue
- innehåller
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex

### <a name="negate-condition-optional"></a>Negera villkoret [valfritt]

Eliminerar det aktuella förhållandet.

### <a name="transform-optional"></a>Transformering [valfritt]

En lista med strängar med namnen på transformationer innan matchningen görs. Det kan vara följande omvandlingar:

- Gemener
- trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Matcha värden [krävs]

Lista med värden att matcha mot, som kan betraktas som *eller*' ed. Det kan till exempel vara IP-adresser eller andra strängar. Formatet för värdet beror på tidigare operatorn.

### <a name="action-required"></a>Åtgärd [krävs]

- Tillåt – tillåter transaktion, hoppar över alla efterföljande regler. Det innebär att den angivna begäran har lagts till i listan över tillåtna och när matchade begäran slutar ytterligare utvärderingen och skickas till serverdelspoolen. Regler som finns på listan över tillåtna utvärderas inte för alla ytterligare anpassade regler och hanterade regler.
- Blockera – blockerar transaktionen utifrån *SecDefaultAction* (identifiering/förhindringsläge). Precis som med tillåtelseåtgärden när begäran utvärderas och läggs till i blockeringslistan, utvärderingen har stoppats och begäran har blockerats. Alla förfrågningar efter som uppfyller samma villkor utvärderas inte och kommer bara att blockeras. 
- Log – kan regeln skriver till loggen, utan låter resten av reglerna kör för utvärdering. Efterföljande anpassade regler utvärderas i prioritetsordning, följt av de hantera reglerna.

## <a name="next-steps"></a>Nästa steg

När du lär dig mer om anpassade regler, [skapa dina egna anpassade regler](create-custom-waf-rules.md).
