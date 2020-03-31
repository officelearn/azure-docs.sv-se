---
title: Anpassade regler för Azure Web Application Firewall (WAF) v2-anpassade regler för Application Gateway
description: Den här artikeln innehåller en översikt över WAF-anpassade regler för webbprogram brandvägg (WAF) v2 på Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 072c7bd5b5b292ca4f0e53c59fcb7e9771331a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031739"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Anpassade regler för brandvägg för webbprogram v2 på Azure Application Gateway

Azure Application Gateway Web Application Firewall (WAF) v2 levereras med en förkonfigurerad, plattformshanterad regeluppsättning som erbjuder skydd mot många olika typer av attacker. Dessa attacker inkluderar korslokalskript, SQL-injektion och andra. Om du är WAF-administratör kanske du vill skriva dina egna regler för att utöka grundläggande regeluppsättningsreglerna (CRS). Dina regler kan antingen blockera eller tillåta begärd trafik baserat på matchande kriterier.

Med anpassade regler kan du skapa egna regler som utvärderas för varje begäran som passerar genom WAF. Dessa regler har högre prioritet än resten av reglerna i de hanterade regeluppsättningarna. De anpassade reglerna innehåller ett regelnamn, regelprioritet och en matris med matchande villkor. Om dessa villkor är uppfyllda vidtas en åtgärd (för att tillåta eller blockera).

Du kan till exempel blockera alla begäranden från en IP-adress i intervallet 192.168.5.4/24. I den här regeln är operatören *IPMatch*, matchValues är IP-adressintervallet (192.168.5.4/24) och åtgärden är att blockera trafiken. Du anger också regelns namn och prioritet.

Anpassade regler stöder hur du använder kompounderingslogik för att skapa mer avancerade regler som tillgodoser dina säkerhetsbehov. Till exempel (villkor 1 **och** villkor 2) **eller** villkor 3). Det innebär att om villkor 1 **och** villkor 2 är uppfyllda, **eller** om villkor 3 är uppfyllt, bör WAF vidta den åtgärd som anges i den anpassade regeln.

Olika matchningsvillkor inom samma regel förvärras alltid med **och**. Blockera till exempel trafik från en viss IP-adress och endast om de använder en viss webbläsare.

Om du vill **eller** två olika villkor måste de två villkoren vara i olika regler. Blockera till exempel trafik från en viss IP-adress eller blockera trafik om de använder en viss webbläsare.

> [!NOTE]
> Det maximala antalet ANPASSADE WAF-regler är 100. Mer information om begränsningar för Application Gateway finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

Reguljära uttryck stöds också i anpassade regler, precis som i CRS-regeluppsättningarna. Exempel på exempel finns i exempel 3 och 5 i [Skapa och använd anpassade brandväggsregler för webbprogram](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Tillåta kontra blockering

Tillåta och blockera trafik är enkelt med anpassade regler. Du kan till exempel blockera all trafik som kommer från ett intervall av IP-adresser. Du kan göra en annan regel för att tillåta trafik om begäran kommer från en viss webbläsare.

Om du vill tillåta `-Action` något kontrollerar du att parametern är inställd **på Tillåt**. Om du vill blockera `-Action` något kontrollerar du att parametern är inställd **på Blockera**.

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

Den här anpassade regeln innehåller ett namn, prioritet, en åtgärd och den matris med matchande villkor som måste uppfyllas för att åtgärden ska kunna utföras. Mer information om dessa fält finns i följande fältbeskrivningar. Till exempel anpassade regler, se [Skapa och använda anpassade webbprogram brandväggsregler](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Fält för anpassade regler

### <a name="name-optional"></a>Namn [valfritt]

Namnet på regeln.  Den visas i loggarna.

### <a name="priority-required"></a>Prioritet [krävs]

- Bestämmer regelvärderingsordern. Ju lägre värde, desto tidigare utvärdering av regeln. Det tillåtna intervallet är från 1-100. 
- Måste vara unikt i alla anpassade regler. En regel med prioritet 40 utvärderas före en regel med prioritet 80.

### <a name="rule-type-required"></a>Regeltyp [obligatoriskt]

För närvarande måste **vara MatchRule**.

### <a name="match-variable-required"></a>Matchvariabel [obligatoriskt]

Måste vara en av variablerna:

- RemoteAddr – IP-adress/värdnamn för fjärrdatoranslutningen
- RequestMethod – HTTP-begäran (GET, POST, PUT, DELETE och så vidare.)
- QueryString – variabel i URI
- PostArgs - Argument skickas i POST kroppen. Anpassade regler som använder den här matchningsvariabeln tillämpas endast om rubriken "Content-Type" är inställt på "application/x-www-form-urlencoded" och "multipart/form-data".
- RequestUri – URI för begäran
- RequestHeaders – Rubriker för begäran
- RequestBody – Detta innehåller hela begäran kroppen som helhet. Anpassade regler med den här matchningsvariabeln tillämpas endast om rubriken "Content-Type" är inställt på "application/x-www-form-urlencoded". 
- RequestCookies – Cookies för begäran

### <a name="selector-optional"></a>Väljare [valfritt]

Beskriver fältet för den matchvarierbara samlingen. Om matchVariable till exempel är RequestHeaders kan väljaren finnas i *användaragenthuvudet.*

### <a name="operator-required"></a>Operatör [krävs]

Måste vara en av följande aktörer:

- IPMatch - används endast när Match variabel är *RemoteAddr*
- Lika med – indata är samma som MatchValue
- Innehåller
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- Geomatch (förhandsgranskning)

### <a name="negate-condition-optional"></a>Förneka villkor [valfritt]

Upphäver det aktuella tillståndet.

### <a name="transform-optional"></a>Transformera [valfritt]

En lista med strängar med namn på omvandlingar att göra innan matchningen görs. Dessa kan vara följande omvandlingar:

- Gemener
- Trim
- UrlDecode (UrlDecode)
- UrlEncode (UrlEncode) 
- Ta bortUller
- HtmlEntityDecode

### <a name="match-values-required"></a>Matcha värden [obligatoriskt]

Lista över värden att matcha mot, som kan ses som *ELLER ed.* Det kan till exempel vara IP-adresser eller andra strängar. Värdeformatet beror på den tidigare operatorn.

### <a name="action-required"></a>Åtgärd [krävs]

- Tillåt – Godkänner transaktionen och hoppar över alla andra regler. Den angivna begäran läggs till i tillåt-listan och när den har matchats stoppas ytterligare utvärdering och skickas till serverdapoolen. Regler som finns i listan över tillåtna utvärderas inte för ytterligare anpassade regler eller hanterade regler.
- Block – Blockerar transaktionen baserat på *SecDefaultAction* (identifierings-/förebyggande läge). Precis som åtgärden Tillåt stoppas utvärderingen när begäran har utvärderats och lagts till i blockeringslistan och begäran blockeras. Alla förfrågningar efter det uppfyller samma villkor kommer inte att utvärderas och kommer bara att blockeras. 
- Log – Låter regeln skriva till loggen, men låter resten av reglerna köras för utvärdering. De andra anpassade reglerna utvärderas i prioritetsordning, följt av de hanterade reglerna.

## <a name="geomatch-custom-rules-preview"></a>Anpassade regler för Geomatch (förhandsgranskning)

Med anpassade regler kan du skapa skräddarsydda regler som passar de exakta behoven hos dina program och säkerhetsprinciper. Du kan begränsa åtkomsten till dina webbprogram efter land/region. Mer information finns i [Geomatch anpassade regler (förhandsgranskning)](geomatch-custom-rules.md).

## <a name="next-steps"></a>Nästa steg

När du har lärt dig mer om anpassade regler [skapar du egna anpassade regler](create-custom-waf-rules.md).
