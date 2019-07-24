---
title: Anpassad regel för brand vägg för webbaserade program för Azures front dörr
description: Lär dig att använda anpassade regler för brand vägg för webbaserade program (WAF) som skyddar dina webb program mot skadliga attacker.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 02b335de7f105d768168d5f798ec9109136d7430
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846265"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Anpassade regler för brand vägg för webb program med Azures front dörr
Med Azure WebApplication-brandväggen (WAF) med frontend-tjänsten kan du kontrol lera åtkomsten till dina webb program baserat på de villkor som du definierar. En anpassad WAF-regel består av ett prioritets nummer, en regeltyp, matchnings villkor och en åtgärd. Det finns två typer av anpassade regler: matchnings regler och hastighets begränsnings regler. En matchnings regel styr åtkomst baserat på matchnings villkor medan en hastighets begränsnings regel styr åtkomst baserat på matchnings villkor och frekvensen av inkommande begär Anden. Du kan inaktivera en anpassad regel för att förhindra att den utvärderas, men behåll konfigurationen fortfarande. I den här artikeln beskrivs matchnings regler som baseras på http-parametrar.

## <a name="priority-match-conditions-and-action-types"></a>Prioritet, matchnings villkor och åtgärds typer
Du kan kontrol lera åtkomst med en anpassad WAf-regel som definierar ett prioritets nummer, en regeltyp, matchnings villkor och en åtgärd. 

- **Prioritet:** är ett unikt heltal som beskriver ordningen för utvärdering av WAF-regler. Regler med lägre värden utvärderas före regler med högre värden

- **Åtgärd:** definierar hur en begäran ska dirigeras om en WAF-regel matchas. Du kan välja en av nedanstående åtgärder som ska tillämpas när en begäran matchar en anpassad regel.

    - *Allow* -WAF vidarebefordrar fråge änden till Server delen och loggar en post i WAF-loggar och avslutas.
    - *Blockering av* begäran blockeras, WAF skickar svar till klienten utan att vidarebefordra begäran till Server delen. WAF loggar en post i WAF-loggar.
    - *Log* -WAF loggar en post i WAF-loggar och fortsätter att utvärdera nästa regel.
    - *Redirect* -WAF omdirigerar begäran till en angiven URI, loggar en post i WAF-loggar och avslutas.

- **Matchnings villkor:** definierar en match variabel, en operator och ett matchnings värde. Varje regel kan innehålla flera matchnings villkor. Ett matchnings villkor kan baseras på nedanstående *matcha variabler*:
    - RemoteAddr (klient-IP)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Operator:** listan innehåller följande:
    - Any: används ofta för att definiera standard åtgärder om inga regler matchas. Alla är en match alla-operator.
    - IPMatch: definiera IP-begränsning för RemoteAddr-variabel
    - Geografisk matchning: definiera geo-filtrering för RemoteAddr-variabel
    - Skeppningskvantiteten
    - innehåller
    - LessThan: storleks begränsning
    - GreaterThan: storleks begränsning
    - LessThanOrEqual: storleks begränsning
    - GreaterThanOrEqual: storleks begränsning
    - BeginsWith
     - EndsWith

Du kan ställa  in negationt villkor så att det är sant om resultatet av ett villkor ska vara negationt.

*Matchnings värde* definierar listan över möjliga matchnings värden.
Värden för HTTP-begäran som stöds är:
- HÄMTA
- POST
- PLACERA
- FÖRETAGETS
- DELETE
- SKRIVLÅS
- OLÅST
- UPPHANDLARPROFIL
- ALTERNATIV
- PROPFIND
- PROPPATCH
- MKCOL
- EXEMPLAR
- FART

## <a name="examples"></a>Exempel

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>WAF anpassade regel exempel baserat på http-parametrar

Här är ett exempel som visar konfigurationen av en anpassad regel med två matchnings villkor. Begär Anden kommer från en angiven plats som definieras av referent, och frågesträngen innehåller inte "Password".

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
En exempel konfiguration för blockering av "placering"-metoden visas som nedan:

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Storleks begränsning

Du kan bygga en anpassad regel som anger storleks begränsningen för en del av en inkommande begäran. Till exempel blockerar regel en URL som är längre än 100 tecken.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [brand vägg för webbaserade program](waf-overview.md)
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).

