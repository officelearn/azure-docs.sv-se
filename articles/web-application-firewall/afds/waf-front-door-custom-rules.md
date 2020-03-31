---
title: Anpassad regel för brandvägg för webbprogram för Azure Front Door
description: Lär dig hur du använder waf-anpassade regler (Web Application Firewall) som skyddar dina webbprogram från skadliga attacker.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475832"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Anpassade regler för brandvägg för webbprogram med Azure Front Door

Med WAF (Azure Web Application Firewall) med Ytterdörren kan du styra åtkomsten till dina webbprogram baserat på de villkor du definierar. En anpassad WAF-regel består av ett prioritetsnummer, regeltyp, matchningsvillkor och en åtgärd. Det finns två typer av anpassade regler: matcha regler och regler för hastighetsbegränsning. En matchningsregel styr åtkomsten baserat på en uppsättning matchande villkor medan en hastighetsgränsregel styr åtkomsten baserat på matchningsvillkor och antalet inkommande begäranden. Du kan inaktivera en anpassad regel för att förhindra att den utvärderas, men ändå behålla konfigurationen. 

## <a name="priority-match-conditions-and-action-types"></a>Prioritet, matchningsvillkor och åtgärdstyper

Du kan styra åtkomsten med en anpassad WAf-regel som definierar ett prioritetsnummer, en regeltyp, en matris med matchningsvillkor och en åtgärd. 

- **Prioritet:** är ett unikt heltal som beskriver ordningen för utvärdering av WAF-regler. Regler med lägre prioritetsvärden utvärderas före regler med högre värden. Prioritetsnummer måste vara unika bland alla anpassade regler.

- **Åtgärd:** definierar hur en begäran ska dirigeras om en WAF-regel matchas. Du kan välja en av nedanstående åtgärder som ska tillämpas när en begäran matchar en anpassad regel.

    - *Tillåt* - WAF vidarebefordrar questen till backend, loggar en post i WAF loggar och utgångar.
    - *Block* - Begäran är blockerad, WAF skickar svar till klienten utan att vidarebefordra begäran till backend. WAF loggar en post i WAF-loggar.
    - *Log* - WAF loggar en post i WAF-loggar och fortsätter att utvärdera nästa regel.
    - *Omdirigera* - WAF omdirigerar begäran till en angiven URI, loggar en post i WAF-loggar och utgångar.

- **Matchningsvillkor:** definierar en matchningsvariabel, en operator och matchningsvärde. Varje regel kan innehålla flera matchningsvillkor. Ett matchningsvillkor kan baseras på geografisk plats, klient-IP-adresser (CIDR), storlek eller strängmatchning. Strängmatchning kan vara mot en lista över matchningsvariabler.
  - **Matchvariabel:**
    - BegäranMethod
    - QueryString
    - PostArgs (andra)
    - RequestUri (förfråganuri)
    - RequestHeader
    - RequestBody
    - Cookies
  - **Operatör:**
    - Alla: används ofta för att definiera standardåtgärd om inga regler matchas. Alla är en matchning alla operatör.
    - Lika med
    - Innehåller
    - LessThan: storleksbegränsning
    - GreaterThan: storleksbegränsning
    - LessThanOrEqual: storleksbegränsning
    - GreaterThanOrEqual: storleksbegränsning
    - BeginsWith
    - EndsWith
    - Regex
  
  - **Regex** stöder inte följande åtgärder: 
    - Bakåtreferenser och fånga underuttryck
    - Godtyckliga nollbreddspåståenden
    - Subrutinreferenser och rekursiva mönster
    - Villkorliga mönster
    - Bakåtspårande kontrollverb
    - Direktivet \C med enkel byte
    - Matchdirektivet \R newline
    - Direktivet \K start av matchningsåterställning
    - Bildtexter och inbäddad kod
    - Atom- gruppering och possessiva kvantifierare

  - **Förneka [valfritt]:** Du kan ställa in *förneka villkoret* till sant om resultatet av ett villkor ska förnekas.
      
  - **Transformera [valfritt]:** En lista med strängar med namn på omvandlingar att göra innan matchningen görs. Dessa kan vara följande omvandlingar:
     - Versaler 
     - Gemener
     - Trim
     - Ta bortUller
     - UrlDecode (UrlDecode)
     - UrlEncode (UrlEncode)
     
   - **Matcha värde:** Http-begärandevärden som stöds omfattar:
     - HÄMTA
     - POST
     - PUT
     - HEAD
     - DELETE
     - Låsa
     - Låsa upp
     - PROFIL
     - Alternativ
     - PROPFIND
     - Proppatch
     - Mkcol
     - Kopiera
     - Flytta

## <a name="examples"></a>Exempel

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Exempel på anpassade regler för WAF baserat på httpparametrar

Här är ett exempel som visar konfigurationen av en anpassad regel med två matchningsvillkor. Begäranden kommer från en angiven plats som definieras av referen, och frågesträngen innehåller inte "lösenord".

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
En exempelkonfiguration för att blockera metoden "PUT" visas nedan:

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

### <a name="size-constraint"></a>Storleksbegränsning

Du kan skapa en anpassad regel som anger storleksbegränsning för en del av en inkommande begäran. Under regeln blockerar till exempel en url som är längre än 100 tecken.

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
- [Konfigurera en brandvägg för webbprogram med Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Läs mer om [brandvägg för webbapplikationer med ytterdörr](afds-overview.md)
- Läs hur du [skapar en Front Door](../../frontdoor/quickstart-create-front-door.md).

