---
title: WAF (Azure Web Application Firewall) v2 anpassade regler för Application Gateway
description: Den här artikeln innehåller en översikt över anpassade regler för brand vägg för webbaserade program (WAF) V2 på Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/04/2019
ms.author: victorh
ms.openlocfilehash: 844e24466e9a9b46be3212690767a408e75f234d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516622"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Anpassade regler för WebApplication-brandväggen v2 på Azure Application Gateway

Azure Application Gateway Web Application Firewall (WAF) v2 levereras med en förkonfigurerad, plattforms oberoende ruleset som ger skydd från många olika typer av attacker. Dessa attacker omfattar Cross Site Scripting, SQL-inmatning och andra. Om du är WAF-administratör kanske du vill skriva egna regler för att utöka reglerna för kärn regel uppsättning (DATORISERAde regler). Reglerna kan antingen blockera eller tillåta begärd trafik baserat på matchnings villkor.

Med anpassade regler kan du skapa egna regler som utvärderas för varje begäran som passerar genom WAF. Dessa regler innehåller högre prioritet än resten av reglerna i de hanterade regel uppsättningarna. De anpassade reglerna innehåller ett regel namn, regel prioritet och en matris med matchnings villkor. Om dessa villkor är uppfyllda vidtas en åtgärd (för att tillåta eller blockera).

Du kan till exempel blockera alla förfrågningar från en IP-adress i intervallet 192.168.5.4/24. I den här regeln är operatören *IPMatch*, MATCHVALUES är IP-adressintervallet (192.168.5.4/24) och åtgärden är att blockera trafiken. Du anger också regelns namn och prioritet.

Anpassade regler stöder användning av sammansatt logik för att skapa mer avancerade regler som åtgärdar dina säkerhets behov. Till exempel (villkor 1 **och** villkor 2) **eller** villkor 3).  Det här exemplet innebär att om villkor 1 **och** villkor 2 är uppfyllda, **eller** om villkoret 3 är uppfyllt, bör WAF vidta den åtgärd som anges i den anpassade regeln.

Olika matchnings villkor i samma regel är alltid sammansatta med **och**. Du kan till exempel blockera trafik från en specifik IP-adress och endast om de använder en viss webbläsare.

Om du vill **eller** två olika villkor måste de två villkoren finnas i olika regler. Blockera till exempel trafik från en speciell IP-adress eller blockera trafik om de använder en speciell webbläsare.

> [!NOTE]
> Det maximala antalet anpassade WAF-regler är 100. Mer information om Application Gateway gränser finns i [Azure-prenumerationer och tjänst begränsningar, kvoter och begränsningar](../../azure-subscription-service-limits.md#application-gateway-limits).

Reguljära uttryck stöds också i anpassade regler, precis som i det datoriserade boknings systemet rulesets. Exempel på dessa finns i exempel 3 och 5 i [skapa och använda anpassade brand Väggs regler för webb program](create-custom-waf-rules.md).

## <a name="allowing-vs-blocking"></a>Tillåta kontra blockera

Det är enkelt att tillåta och blockera trafik med anpassade regler. Du kan till exempel blockera all trafik från ett intervall med IP-adresser. Du kan skapa en annan regel för att tillåta trafik om begäran kommer från en speciell webbläsare.

Se till att parametern `-Action` är inställd på **Tillåt**för att tillåta något. Om du vill blockera något kontrollerar du att parametern `-Action` är inställd på **blockera**.

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

Den här anpassade regeln innehåller ett namn, en prioritet, en åtgärd och matrisen med matchnings villkor som måste uppfyllas för att åtgärden ska äga rum. Mer förklaringar av de här fälten finns i följande fält beskrivningar. Till exempel anpassade regler, se [skapa och använda anpassade brand Väggs regler för webb program](create-custom-waf-rules.md).

## <a name="fields-for-custom-rules"></a>Fält för anpassade regler

### <a name="name-optional"></a>Namn [valfritt]

Detta är namnet på regeln. Det här namnet visas i loggarna.

### <a name="priority-required"></a>Prioritet [krävs]

- Fastställer regel värderings ordningen. Ju lägre värde, den tidigare utvärderings versionen av regeln. Det tillåtna intervallet är från 1-100. 
- Måste vara unikt för alla anpassade regler. En regel med prioritet 40 utvärderas före en regel med prioritet 80.

### <a name="rule-type-required"></a>Regeltyp [obligatoriskt]

För närvarande måste vara **MatchRule**.

### <a name="match-variable-required"></a>Matcha variabel [obligatorisk]

Måste vara en av variablerna:

- RemoteAddr – IP-adress/värdnamn för fjärran sluten dator anslutning
- RequestMethod – HTTP-metod för begäran (Hämta, skicka, placera, ta bort och så vidare.)
- QueryString – variabel i URI: n
- PostArgs – argument skickas i POST-texten. Anpassade regler som använder den här matchnings variabeln används bara om Content-Type-huvudet är inställt på "Application/x-www-form-urlencoded" och "flerdelade/form-data".
- RequestUri – URI för begäran
- RequestHeaders – huvuden för begäran
- RequestBody – detta innehåller hela begär ande texten som helhet. Anpassade regler som använder den här matchnings variabeln används bara om Content-Type-huvudet är inställt på ' Application/x-www-form-urlencoded '. 
- RequestCookies – cookies för begäran

### <a name="selector-optional"></a>Väljar [valfritt]

Beskriver fältet i matchVariable-samlingen. Om matchVariable till exempel är RequestHeaders kan väljaren finnas i *användar agentens* huvud.

### <a name="operator-required"></a>Operator [nödvändig]

Måste vara någon av följande operatorer:

- IPMatch – används endast när match-variabeln är *RemoteAddr*
- Är lika med – indatamängden är samma som MatchValue
- Contains
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- verifiering
- Övermatchning (för hands version)

### <a name="negate-condition-optional"></a>Negera villkor [valfritt]

Negerar det aktuella villkoret.

### <a name="transform-optional"></a>Transformering [valfritt]

En lista med strängar med namn på omvandlingar som ska utföras innan matchningen görs. Dessa omvandlingar kan vara följande:

- Gemener
- Reducera
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>Matchnings värden [required]

Lista med värden som ska matchas mot, som kan betraktas som *eller*' ed '. Det kan till exempel vara IP-adresser eller andra strängar. Värde formatet beror på föregående operator.

### <a name="action-required"></a>Åtgärd [krävs]

- Allow – auktoriserar transaktionen och hoppar över alla efterföljande regler. Det innebär att den angivna begäran läggs till i listan över tillåtna och när den matchas avbryts begäran ytterligare utvärdering och skickas till backend-poolen. Regler som finns på listan över tillåtna utvärderas inte för några ytterligare anpassade regler eller hanterade regler.
- Blockera – blockerar transaktionen baserat på *SecDefaultAction* (identifiering/skydds läge). Precis som med åtgärden Tillåt stoppas utvärderings versionen och begäran blockeras när begäran utvärderas och läggs till i blockeringslistan. Alla begär Anden som uppfyller samma villkor kommer inte att utvärderas och kommer bara att blockeras. 
- Log – tillåter att regeln skriver till loggen, men låter resten av reglerna köras för utvärdering. Efterföljande anpassade regler utvärderas i prioritetsordning, följt av de hanterade reglerna.

## <a name="geomatch-custom-rules-preview"></a>Ommatchnings anpassade regler (förhands granskning)

Om du använder en kors matchnings operator kan väljare vara någon av följande tvåsiffriga lands koder. 

|Landskod | Lands namn |
| ----- | ----- |
| AD | Andorra |
| AE | Förenade Arabemiraten|
| AF | Afghanistan|
| AG | Antigua och Barbuda|
| AL | Albanien|
| AM | Armenien|
| AO | Angola|
| AR | Argentina|
| AS | Amerikanska Samoa|
| AT | Österrike|
| AU | Australien|
| AZ | Azerbajdzjan|
| BA | Bosnien och Hercegovina|
| BB | Barbados|
| BD | Bangladesh|
| BE | Belgien|
| BF | Burkina Faso|
| BG | Bulgarien|
| BH | Bahrain|
| BI | Burundi|
| BJ | Benin|
| BL | Sankt Barthélemy|
| BN | Brunei Darussalam|
| BO | Bolivia|
| BR | Brasilien|
| BS | Bahamas|
| BT | Bhutan|
| BW | Botswana|
| BY | Vitryssland|
| BZ | Belize|
| CA | Kanada|
| CD | Demokratiska republiken Kongo|
| CF | Centralafrikanska republiken|
| CH | Schweiz|
| CI | Cote d'Ivoire|
| CL | Chile|
| CM | Kamerun|
| CN | Kina|
| CO | Colombia|
| CR | Costa Rica|
| CU | Kuba|
| CV | Cabo Verde|
| CY | Cypern|
| CZ | Tjeckien|
| DE | Tyskland|
| DK | Danmark|
| DO | Dominikanska republiken|
| DZ | Algeriet|
| EC | Ecuador|
| EE | Estland|
| EG | Egypten|
| ES | Spanien|
| ET | Etiopien|
| FI | Finland|
| FJ | Fiji|
| FM | Mikronesien|
| FR | Frankrike|
| GB | Storbritannien|
| GE | Georgien|
| GF | Franska Guyana|
| GH | Ghana|
| GN | Guinea|
| GP | Guadeloupe|
| GR | Grekland|
| GT | Guatemala|
| GY | Guyana|
| HK | Hong Kong SAR|
| HN | Honduras|
| HR | Kroatien|
| HT | Haiti|
| HU | Ungern|
| ID | Indonesien|
| IE | Irland|
| IL | Israel|
| IN | Indien|
| IQ | Irak|
| IR | Islamiska republiken Iran|
| IS | Island|
| IT | Italien|
| JM | Jamaica|
| JO | Jordanien|
| JP | Japan|
| KE | Kenya|
| KG | Kirgizistan|
| KH | Kambodja|
| KI | Kiribati|
| KN | Saint Kitts och Nevis|
| KP | Nordkorea|
| KR | Sydkorea|
| KW | Kuwait|
| KY | Caymanöarna|
| KZ | Kazakhstan|
| LA | Demokratiska folkrepubliken Laos|
| LB | Libanon|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Litauen|
| LU | Luxemburg|
| LV | Lettland|
| LY | Libyen |
| MA | Marocko|
| MD | Moldavien|
| MG | Madagaskar|
| MK | Nord Makedonien|
| ML | Mali|
| MM | Myanmar|
| MN | Mongoliet|
| MO | Macao SAR|
| MQ | Martinique|
| MR | Mauretanien|
| MT | Malta|
| MV | Maldiverna|
| MW | Malawi|
| MX | Mexiko|
| MY | Malaysia|
| MZ | Moçambique|
| Ej tillämpligt | Namibia|
| NE | Niger|
| NG | Nigeria|
| NI | Nicaragua|
| NL | Nederländerna|
| NO | Norge|
| NP | Nepal|
| NR | Nauru|
| NZ | Nya Zeeland|
| OM | Oman|
| PA | Panama|
| PE | Peru|
| PH | Filippinerna|
| PK | Pakistan|
| PL | Polen|
| PR | Puerto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguay|
| QA | Qatar|
| RE | Réunion|
| RO | Rumänien|
| RS | Serbien|
| RU | Ryska federationen|
| RW | Rwanda|
| SA | Saudiarabien|
| SD | Sudan|
| SE | Sverige|
| SG | Singapore|
| SI | Slovenien|
| SK | Slovakien|
| SN | Senegal|
| SO | Somalia|
| SR | Surinam|
| SS | Sydsudan|
| SV | El Salvador|
| SY | Arabrepubliken Syrien|
| SZ | Swaziland|
| TC | Turks- och Caicosöarna|
| TG | Togo|
| TH | Thailand|
| TN | Tunisien|
| TR | Turkiet|
| TT | Trinidad och Tobago|
| TW | Taiwan|
| TZ | Tanzania|
| UA | Ukraina|
| UG | Uganda|
| USA | USA|
| UY | Uruguay|
| UZ | Uzbekistan|
| VC | Saint Vincent och Grenadinerna|
| VE | Venezuela|
| VG | Jungfruöarna, Storbritannien|
| VI | Jungfruöarna, USA|
| VN | Vietnam|
| ZA | Sydafrika|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Nästa steg

När du har lärt dig om anpassade regler [skapar du dina egna anpassade regler](create-custom-waf-rules.md).
