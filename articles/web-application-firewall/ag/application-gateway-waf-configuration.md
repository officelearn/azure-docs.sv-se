---
title: Brandväggsbegäran för webbprogram storleksbegränsningar och undantagslistor i Azure Application Gateway - Azure-portal
description: Den här artikeln innehåller information om storleksbegränsningar för webbprogramsbrandväggen och undantagslistor konfiguration i Application Gateway med Azure-portalen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7244788bbc7431c7f26363b2852babb72d5697e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526798"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Brandvägg för webbprogram begär storleksbegränsningar och undantagslistor

Azure Application Gateway Web Application Firewall (WAF) skyddar webbprogram. I den här artikeln beskrivs konfigurationen för WAF-begärandens storlek och undantagslistor. Dessa inställningar finns i WAF-principen som är kopplad till programgatewayen. Mer information om WAF-principer finns i [Azure Web Application Firewall på Azure Application Gateway](ag-overview.md) och Skapa principer för brandvägg för [webbprogram för Application Gateway](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>UNDANTAGSLISTOR FÖR WAF

![Storleksbegränsningar för begäran](../media/application-gateway-waf-configuration/waf-policy.png)

MED WAF-undantagslistor kan du utelämna vissa begäranattribut från en WAF-utvärdering. Ett vanligt exempel är Infogade token med Active Directory som används för autentiserings- eller lösenordsfält. Sådana attribut är benägna att innehålla specialtecken som kan utlösa ett falskt positivt från WAF-reglerna. När ett attribut har lagts till i WAF-undantagslistan beaktas det inte av någon konfigurerad och aktiv WAF-regel. Undantagslistor är globala i omfattning.

Följande attribut kan läggas till i undantagslistor efter namn. Värdena för det valda fältet utvärderas inte mot WAF-regler, men deras namn är fortfarande (se exempel 1 nedan, värdet för användaragenthuvudet är uteslutet från WAF-utvärdering). Undantagslistorna tar bort granskning av fältets värde.

* Rubriker för begäran
* Begär cookies
* Attributnamn för begäran (args) kan läggas till som ett undantagselement, till exempel:

   * Formulärfältsnamn
   * XML-entitet
   * JSON-enhet
   * URL-frågesträng args

Du kan ange en exakt begäranhetsrubrik, brödtext, cookie eller frågesträngattributmatchning.  Du kan också ange partiella matchningar. Undantagsregler är globala och gäller för alla sidor och alla regler.

Följande är de matchningsvillkor som stöds:

- **Lika med**: Den här operatorn används för en exakt matchning. Om du till exempel vill välja ett huvud med namnet **bearerToken**använder du operatorn likhet med väljaren som **bärareToken**.
- **Börjar med**: Den här operatorn matchar alla fält som börjar med det angivna väljarvärdet.
- **Slutar med**: Den här operatorn matchar alla begärandefält som slutar med det angivna väljarvärdet.
- **Innehåller**: Den här operatorn matchar alla begärandefält som innehåller det angivna väljarvärdet.
- **Lika med alla**: Den här operatorn matchar alla begärandefält. * kommer att vara väljaren värde.

I alla fall är matchning skiftlägesokänslig och reguljära uttryck är inte tillåtna som väljare.

> [!NOTE]
> Mer information och felsökning finns i FELSÖKNING AV [WAF](web-application-firewall-troubleshoot.md).

### <a name="examples"></a>Exempel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Följande exempel visar användningen av undantag.

#### <a name="example-1"></a>Exempel 1

I det här exemplet vill du utesluta användaragenthuvudet. Begäranden för användaragent innehåller en karakteristisk sträng som gör att nätverksprotokoll peers kan identifiera programtyp, operativsystem, programvaruleverantör eller programvaruversion av den begärande programvaruanvändaragenten. Mer information finns i [User-Agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).

Det kan finnas många orsaker till att inaktivera utvärdering av det här huvudet. Det kan finnas en sträng som WAF ser och antar att det är skadligt. Till exempel den klassiska SQL-attacken "x=x" i en sträng. I vissa fall kan detta vara legitim trafik. Så du kan behöva utesluta det här huvudet från WAF-utvärderingen.

Följande Azure PowerShell-cmdlet utesluter användaragenthuvudet från utvärderingen:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Exempel 2

I det här exemplet *user* utesluts värdet i användarparametern som skickas i begäran via URL:en. Anta till exempel att det är vanligt i din miljö att användarfältet innehåller en sträng som WAF visar som skadligt innehåll, så det blockerar det.  Du kan utesluta användarparametern i det här fallet så att WAF inte utvärderar något i fältet.

Följande Azure PowerShell-cmdlet utesluter användarparametern från utvärderingen:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Så om `http://www.contoso.com/?user%281%29=fdafdasfda` webbadressen skickas till WAF, kommer det inte att utvärdera strängen **fdafdasfda**, men det kommer fortfarande att utvärdera parameternamnet **användaren%281%29**. 

## <a name="waf-request-size-limits"></a>WAF-storleksbegränsningar för begäran



Med brandvägg för webbprogram kan du konfigurera storleksbegränsningar för begäran inom nedre och övre gränser. Följande konfigurationer för två storleksbegränsningar är tillgängliga:

- Fältet för maximal begärandestorlek anges i kilobyte och styr den totala storleksgränsen för begäran, exklusive filöverföringar. Det här fältet kan variera från 1 KB minimum till 128 KB högsta värde. Standardvärdet för brödtextstorlek för begäran är 128 kB.
- Fältet för filöverföringsgräns anges i MB och styr den högsta tillåtna filöverföringsstorleken. Det här fältet kan ha ett minimivärde på 1 MB och följande maximum:

   - 100 MB för v1 Medium WAF gateways
   - 500 MB för v1 Stora WAF gateways
   - 750 MB för v2 WAF gateways 

 Standardvärdet för filöverföringsgränsen är 100 MB.

WAF erbjuder också en konfigurerbar knopp för att slå på eller stänga av förfrågan kroppsinspektionen. Som standard är inspektionen av begäranden aktiverad. Om kontroll av begärandens brödtext är inaktiverat utvärderar WAF inte innehållet i HTTP-meddelandetexten. I sådana fall fortsätter WAF att tillämpa WAF-regler för rubriker, cookies och URI. Om kontroll av begärandeorganet är inaktiverat är fältet för maximal begäranhet inte tillämpligt och kan inte ställas in. Om du inaktiverar kontroll av begäranden kan meddelanden som är större än 128 kB skickas till WAF, men meddelandetexten inspekteras inte för säkerhetsproblem.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat WAF-inställningarna kan du läsa om hur du visar dina WAF-loggar. Mer information finns i [Diagnostik för Programgateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
