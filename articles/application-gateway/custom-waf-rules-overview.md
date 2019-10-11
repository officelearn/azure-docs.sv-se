---
title: Anpassade regler för Azure Web Application Firewall (WAF) v2
description: Den här artikeln innehåller en översikt över anpassade regler för brand vägg för webbaserade program (WAF) v2 i Azure Application Gateway.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 8ae5c9b6b52ea13e3d0981664e8c920cc5b47a01
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263555"
---
# <a name="overview-custom-rules-for-web-application-firewall-v2"></a>Översikt: anpassade regler för webb programs brand vägg v2

Azure Application Gateway Web Application Firewall (WAF) v2 innehåller en förkonfigurerad, plattforms hanterad regel uppsättning som ger skydd från många olika typer av attacker. Dessa attacker omfattar skript körning över flera webbplatser, SQL-inmatning och andra. Om du är WAF-administratör kanske du vill skriva egna regler för att utöka regel uppsättnings reglerna för kärn regel. Reglerna kan antingen blockera eller tillåta begärd trafik baserat på matchnings villkor.

Med anpassade regler kan du skapa egna regler som utvärderas för varje begäran som passerar genom WAF. Dessa regler innehåller högre prioritet än resten av reglerna i de hanterade regel uppsättningarna. De anpassade reglerna innehåller ett regel namn, en regel prioritet och en matris med matchnings villkor. Om dessa villkor är uppfyllda vidtas en åtgärd för att tillåta eller blockera.

Du kan till exempel skapa en regel för att blockera alla begär Anden från en IP-adress i intervallet 192.168.5.4/24. I den här regeln är operatören *IPMatch*, *matchValues* är IP-adressintervallet (192.168.5.4/24) och *åtgärden* är att blockera trafiken. Du anger också regelns namn och prioritet.

Anpassade regler stöder användning av sammansatt logik för att skapa mer avancerade regler som åtgärdar dina säkerhets behov. Exempel: "(villkor 1 *och* villkor 2) *eller* villkor 3)" innebär att om villkor 1 *och* villkor 2 är uppfyllda, *eller* om villkor 3 är uppfyllt, bör WAF vidta den åtgärd som anges i den anpassade regeln.

Olika matchnings villkor i samma regel är alltid sammansatta med *och*. Till exempel en regel som använder *och* kan ange att blockera trafik från en viss IP-adress, och endast om en viss webbläsare används.

Om du vill använda *eller* för två olika villkor måste de två villkoren vara i olika regler. Till exempel kan regeln som använder *eller* kan ange att blockera trafik från en viss IP-adress eller blockera trafik om en viss webbläsare används.

> [!NOTE]
> Det maximala antalet anpassade WAF-regler är 100. Mer information om Application Gateway gränser finns i [Azure-prenumerationer och tjänst begränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md#application-gateway-limits).

Reguljära uttryck stöds också i anpassade regler, precis som de finns i kärn regel uppsättningarna. Exempel på dessa regler finns i "exempel 3" och "exempel 5" i [skapa och använda anpassade brand Väggs regler för webb program](create-custom-waf-rules.md).

## <a name="allowing-or-blocking-traffic"></a>Tillåta eller blockera trafik

Det är enkelt att tillåta eller blockera trafik med anpassade regler. Du kan till exempel blockera all trafik som kommer från ett intervall med IP-adresser. Du kan skapa en annan regel för att tillåta trafik om begäran kommer från en viss webbläsare.

Om du vill tillåta något måste du se till att parametern `-Action` är inställd på **Tillåt**. Om du vill blockera något kontrollerar du att parametern `-Action` är inställd på **blockera**, som du ser i följande kod:

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

Föregående `$BlockRule` mappar till följande anpassade regel i Azure Resource Manager:

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

Den här anpassade regeln innehåller ett namn, en prioritet, en åtgärd och en matris med matchnings villkor som måste uppfyllas för att åtgärden ska äga rum. Beskrivningar av fälten för anpassad regel finns i följande avsnitt. Exempel på anpassade regler finns i [skapa och använda anpassade brand Väggs regler för webb program](create-custom-waf-rules.md).

## <a name="custom-rule-fields"></a>Fält för anpassad regel

### <a name="name-optional"></a>Namn (valfritt)

Detta är namnet på regeln. Namnet visas i loggarna.

### <a name="priority-required"></a>Prioritet (krävs)

- Prioriteten avgör i vilken ordning reglerna utvärderas. Ju lägre värde, den tidigare utvärderings versionen av regeln. Det tillåtna intervallet är mellan 1 och 100.
- Prioriteten måste vara unik bland alla anpassade regler. En regel med prioritet 40 utvärderas före en regel med prioriteten 80.

### <a name="rule-type-required"></a>Regeltyp (krävs)

För närvarande måste regel typen vara **MatchRule**.

### <a name="match-variable-required"></a>Matcha variabel (krävs)

Match-variabeln måste vara något av följande:

- RemoteAddr: IP-adressen eller värd namnet för fjärran sluten dator anslutning
- RequestMethod: HTTP-metod för begäran (GET, POST, Box, DELETE och så vidare).
- QueryString: variabeln i URI: n.
- PostArgs: argumenten som skickas i POST-texten. Anpassade regler som använder den här matchnings variabeln används endast om Content-Type-huvudet är inställt på "Application/x-www-form-urlencoded" och "multipart/form-data".
- RequestUri: URI för begäran.
- RequestHeaders: huvudena i begäran.
- RequestBody: variabeln som innehåller hela begär ande texten som helhet. Anpassade regler som använder den här matchnings variabeln används endast om Content-Type-huvudet är inställt på "Application/x-www-form-urlencoded". 
- RequestCookies: cookies för begäran.

### <a name="selector-optional"></a>Väljare (valfritt)

Väljaren beskriver fältet i matchVariable-samlingen. Om matchVariable till exempel är "RequestHeaders" kan väljaren finnas i User-Agent-huvudet.

### <a name="operator-required"></a>Operator (krävs)

Operatorn måste vara något av följande:

- IPMatch: den här operatorn används endast när match-variabeln är *RemoteAddr*.
- Är lika med: indatamängden är samma som MatchValue.
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- verifiering

### <a name="negate-condition-optional"></a>Negera villkor (valfritt)

Negerar det aktuella villkoret.

### <a name="transform-optional"></a>Transformering (valfritt)

En lista med strängar med namnen på omvandlingar som ska slutföras innan matchningen görs. Transformeringarna inkluderar:

- Gemener
- Reducera
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Matchnings värden (obligatoriskt)

Fältet matchValues är en lista med värden som ska matchas mot, som kan betraktas som *eller*"Ed". Värdena kan till exempel vara IP-adresser eller andra strängar. Värde formatet beror på föregående operator.

### <a name="action-required"></a>Åtgärd (krävs)

I åtgärds fältet finns följande alternativ: 

- Tillåt: auktoriserar transaktionen och hoppar över alla efterföljande regler. Det innebär att den angivna begäran läggs till i listan över tillåtna och att efter att den har matchats, stoppar begäran ytterligare utvärdering och skickas till backend-poolen. Regler som finns på listan över tillåtna utvärderas inte för ytterligare anpassade regler eller hanterade regler.

- Blockera: blockerar transaktionen baserat på *SecDefaultAction* (identifiering/skydds läge). Precis som Tillåt-åtgärden, när begäran har utvärderats och lagts till i blockeringslistan, stoppas utvärderingen och begäran blockeras. Efterföljande begär Anden som uppfyller samma villkor utvärderas inte. De är bara blockerade. 

- Log: tillåter att regeln skriver till loggen och låter resten av reglerna köras för utvärdering. Efterföljande anpassade regler utvärderas i prioritetsordning, följt av de hanterade reglerna.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om anpassade regler kan du [skapa dina egna anpassade regler](create-custom-waf-rules.md).
