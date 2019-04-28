---
title: Web anpassade brandväggsregeln för application för Azure ytterdörren
description: Lär dig hur du använder web application firewall (WAF) anpassade regler skyddar dina webbprogram mot skadliga attacker.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud;tyao
ms.openlocfilehash: 744c6fb9235c9daa2d5239ef9fd13679db943650
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459716"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Anpassade regler för brandväggen för webbaserade program med Azure ytterdörren
Azure waf (WAF) med ytterdörren-tjänsten kan du styra åtkomsten till dina webbprogram baserat på de villkor som du definierar. En anpassad regel för WAF består av flera prioritet, en regeltyp, matchningsvillkor och en åtgärd. Det finns två typer av anpassade regler: matchar regler och Betygsätt gränsen regler. En match-regel styr åtkomsten baserat på matchning villkor när en gräns rate-regel styr åtkomsten baserat på matchning villkor och räntorna för inkommande begäranden. Du kan inaktivera en anpassad regel om du vill förhindra att utvärderas, utan att konfigurationen. Den här artikeln beskriver matchar regler som baseras på http-parametrar.

## <a name="priority-match-conditions-and-action-types"></a>Prioritet och matchningsvillkor åtgärdstyper
Du kan styra åtkomst med en anpassad WAf-regel som definierar en prioritet siffra, en regeltyp, matchningsvillkor och en åtgärd. 

- **Prioritet:** är ett unikt heltal som anger ordningen för utvärdering av WAF-regler. Regler med lägre värden utvärderas före regler med högre värden

- **Åtgärd:** definierar hur du dirigerar en begäran om en WAF-regel matchas. Du kan välja något av de nedan åtgärder för att använda när en begäran matchar en anpassad regel.

    - *Tillåt* -WAF vidarebefordrar begäran om att serverdelen, loggar en post i WAF-loggar och avslutas.
    - *Blockera* -begäran är blockerad, WAF skickar svar till klienten utan att vidarebefordra begäran till backend-server. WAF loggar en post i WAF-loggar.
    - *Log* -WAF-loggar en post i WAF loggar och fortsätter utvärdera nästa regel.
    - *Omdirigera* -WAF omdirigerar begäran till en angiven URI, loggar en post i WAF-loggar och avslutas.

- **Matchningsvillkor:** definierar en matchning variabel, en operator och matcha värdet. Varje regel kan innehålla flera matchningsvillkor. Ett matchningsvillkor kan vara baserad på den nedan *matchar variabler*:
    - RemoteAddr (klient-IP)
    - requestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **Operatorn:** listan innehåller följande:
    - Något: ofta används för att definiera standardåtgärden om inga regler matchar. Alla finns en matchning all-operator.
    - IPMatch: Definiera IP-begränsning för RemoteAddr variabel
    - GeoMatch: definiera geo-filtrering för RemoteAddr variabel
    - Lika med
    - Contains
    - LessThan: storlek begränsning
    - GreaterThan: storlek begränsning
    - LessThanOrEqual: storlek begränsning
    - GreaterThanOrEqual: storlek begränsning
    - BeginsWith
     - EndsWith

Du kan ange *negera* villkor vara uppfyllda om resultatet av ett villkor som ska negeras.

*Matchar värdet* definierar en lista med möjliga matchning värden.
Stöd för HTTP-frågemetoden värden är:
- HÄMTA
- POST
- PUT
- HEAD
- DELETE
- LÅS
- LÅSA UPP
- PROFIL
- ALTERNATIV
- PROPFIND
- PROPPATCH
- MKCOL
- KOPIERA
- FLYTTA

## <a name="examples"></a>Exempel

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>WAF anpassade regler exempel baserat på http-parametrar

Här är ett exempel som visar konfigurationen av en anpassad regel med två matchningsvillkor. Begäranden som kommer från en angiven plats som definieras av referent och frågesträngen innehåller inte ”lösenord”.

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
En exempelkonfiguration för blockering av ”PLACERA” metod visas enligt nedan:

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

### <a name="size-constraint"></a>Storlek-begränsning

Du kan skapa en anpassad regel som anger storleken begränsningen för en del av en inkommande begäran. Till exempel blockerar nedan regeln en Url som är längre än 100 tecken.

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
- Lär dig mer om [Brandvägg för webbaserade program](waf-overview.md)
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).

