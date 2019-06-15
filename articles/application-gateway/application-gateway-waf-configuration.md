---
title: Storleksgränser för Web application firewall begäran och undantagslistor i Azure Application Gateway – Azure-portalen
description: Den här artikeln innehåller information om storleksgränser för web application firewall begäran och undantag visar konfigurationen i Application Gateway med Azure-portalen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/15/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 5ddcdeca41e2f21fa27db25f7e0721c7ef87e491
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65620289"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Storleksgränser för Web application firewall begäran och undantagslistor

Azure Application Gateway brandväggen för webbaserade program (WAF) ger skydd för webbprogram. Den här artikeln beskriver storleksgränser för WAF-begäran och undantag visar konfigurationen.

## <a name="waf-request-size-limits"></a>Storleksgränser för WAF-begäran

![Storleksgränser för begäran](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Brandvägg för webbaserade program kan du konfigurera storleksbegränsningar för begäran i lägre och övre gränser. Följande två storlek gränser konfigurationer finns:

- Fältet tillåtna brödtext storleken anges i kilobyte och kontroller övergripande storleksgränsen för begäran exkludera en fil laddas upp. Det här fältet kan variera mellan 1 KB minst 128 KB maximala värdet. Standardvärdet för textstorleken för begäran är 128 KB.
- Fältet Spara uppladdning gränsen anges i MB och styr den maximalt tillåtna storleken för filöverföring. Det här fältet kan ha ett minsta värde på 1 MB och högst 500 MB för stora SKU-instanser även medel SKU har högst 100 MB. Standardvärdet för överföring filgränsen är 100 MB.

WAF erbjuder även en konfigurerbar ratten om du vill aktivera begäran brödtext inspektion eller inaktivera. Begäran brödtext granskning är aktiverat som standard. Om begäran brödtext inspektion är avstängd, utvärdera inte WAF innehållet i meddelandetexten för HTTP. I sådana fall kan fortsätter WAF WAF-regler på rubriker, cookies och URI: N. Om begäran brödtext-kontroll är inaktiverad fältet för tillåtna brödtext storlek som inte är tillämplig och kan inte anges. Stänga av begäran brödtext inspektion större än 128 KB skickas till WAF gör för meddelanden, men meddelandetexten kontrolleras inte sårbarheter.

## <a name="waf-exclusion-lists"></a>WAF-undantagslistor

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF-undantagslistor kan du utelämna vissa begärandeattribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory infogas token som används för autentisering eller lösenorden. Attribut är felbenägna innehåller specialtecken som kan utlösa en falsk positiv identifiering från WAF-regler. När ett attribut har lagts till i undantagslistan för WAF, anses det är inte av någon konfigurerad och aktiv WAF-regel. Undantagslistor är globala omfång.

Följande attribut kan läggas till undantagslistor:

* Begärandehuvuden
* Begäran om Cookies
* Begäran attributnamn (argument)

   * Flera delar formulärdata
   * XML
   * JSON
   * URL: en fråga argument

Du kan ange en exakt begärandehuvudet, brödtext, cookie eller fråga strängmatchning för attributet.  Eller alternativt kan du ange delmatchningar. Undantaget är alltid på ett fält med rubriken, aldrig på dess värde. Undantagsregler är globala omfång och gäller för alla sidor och alla regler.

Här följer stöds matchning kriterier operatorer:

- **Är lika med**:  Den här operatorn används efter en exakt matchning. Till exempel för att välja en rubrik med namnet **bearerToken**, använda equals-operatorn med väljaren som **bearerToken**.
- **Börjar med**: Den här operatorn matchar alla fält som börjar med det angivna väljarvärdet.
- **Slutar med**:  Den här operatorn matchar alla fält i begäran som slutar med det angivna väljarvärdet.
- **Innehåller**: Den här operatorn matchar alla fält i begäran som innehåller det angivna väljarvärdet.
- **Lika med valfritt**: Den här operatorn matchar alla fält i begäran. * kommer att väljarvärdet för.

I samtliga fall matchar är skiftlägeskänsligt och reguljära uttryck tillåts inte som väljare.

### <a name="examples"></a>Exempel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I följande exempel demonstrerar användningen av undantag.

### <a name="example-1"></a>Exempel 1

I det här exemplet som du vill undanta användar-agent-huvudet. Användaragent rubriken innehåller en karakteristisk sträng som gör att nätverket protokollet peer-datorer att identifiera programtyp, operativsystem, programvaruleverantör eller programvaruversion av den begärande användaren programvaruagenten. Mer information finns i [användaragent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Det kan finnas en mängd skäl att inaktivera utvärdering av den här rubriken. Det kan vara en sträng som WAF ser och antas den vara skadlig. Till exempel klassiska SQL-angrepp ”x = x” i en sträng. I vissa fall kan vara detta legitima trafik. Så kan du behöva undanta den här rubriken från WAF utvärdering.

Följande Azure PowerShell-cmdlet utesluter Användaragent-rubriken från utvärdering:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```

### <a name="example-2"></a>Exempel 2

Det här exemplet omfattar inte värdet i den *användaren* parameter som anges i begäran via URL: en. Anta exempelvis att det är vanligt i din miljö för användarfältet ska innehålla en sträng som WAF-vyer som skadligt, så att den blockerar den.  Du kan utesluta användarparametern i det här fallet så att WAF inte utvärdera vad som helst i fältet.

Följande Azure PowerShell-cmdlet utesluter user-parameter från utvärdering:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "user"
```
Om URL: en **http://www.contoso.com/?user=fdafdasfda** skickas till WAF, inte den utvärdera strängen **fdafdasfda**.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat inställningarna WAF du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway-diagnostik](application-gateway-diagnostics.md#diagnostic-logging).
