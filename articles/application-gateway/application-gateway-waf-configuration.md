---
title: Storleksgränser för Web application firewall begäran och undantagslistor i Azure Application Gateway – Azure-portalen
description: Den här artikeln innehåller information om storleksgränser för web application firewall begäran och undantag visar konfigurationen i Application Gateway med Azure-portalen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: victorh
ms.openlocfilehash: 12115770959c3869184f0af78c4feba2fd6f2be4
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984901"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists-public-preview"></a>Storleksgränser för Web application firewall begäran- och uteslutningsprinciperna visar (offentlig förhandsversion)

Azure Application Gateway brandväggen för webbaserade program (WAF) ger skydd för webbprogram. Den här artikeln beskriver storleksgränser för WAF-begäran och undantag visar konfigurationen.

> [!IMPORTANT]
> Konfiguration av WAF storleksgränser för begäran och undantagslistor är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="waf-request-size-limits"></a>Storleksgränser för WAF-begäran

![Storleksgränser för begäran](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Brandvägg för webbaserade program kan du konfigurera storleksbegränsningar för begäran i lägre och övre gränser. Följande två storlek gränser konfigurationer finns:

- Fältet tillåtna brödtext storleken anges i KB-artiklar och kontroller övergripande storleksgränsen för begäran exkludera en fil laddas upp. Det här fältet kan variera mellan 1 KB minst 128 KB maximala värdet. Standardvärdet för textstorleken för begäran är 128 KB.
- Fältet Spara uppladdning gränsen anges i MB och styr den maximalt tillåtna storleken för filöverföring. Det här fältet kan ha ett minsta värde på 1 MB och högst 500 MB. Standardvärdet för överföring filgränsen är 100 MB.

WAF erbjuder även en konfigurerbar ratten om du vill aktivera begäran brödtext inspektion eller inaktivera. Begäran brödtext granskning är aktiverat som standard. Om begäran brödtext-kontroll är inaktiverad, utvärderar WAF inte innehållet i meddelandetexten för HTTP. I sådana fall kan fortsätter WAF WAF-regler på rubriker, cookies och URI: N. Om begäran brödtext-kontroll är inaktiverad fältet för tillåtna brödtext storlek som inte är tillämplig och kan inte anges. Om du inaktiverar begäran brödtext inspektion kan meddelanden större än 128 KB skickas till WAF. Meddelandetexten är inte dock kontrolleras sårbarheter.

## <a name="waf-exclusion-lists"></a>WAF-undantagslistor

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

WAF-undantagslistor Tillåt användare att utelämna vissa begärandeattribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory infogas token som används för autentisering eller lösenorden. Attribut är felbenägna innehåller specialtecken som kan utlösa en falsk positiv identifiering från WAF-regler. När ett attribut har lagts till i undantagslistan för WAF kan beaktas den inte av någon konfigurerad och aktiv WAF-regel. Undantagslistor är globala omfång.
Du kan lägga till begärandehuvuden, begärandetexten, begäran cookies eller begäran fråga strängargument undantagslistor för WAF. Om innehållet har formulärdata eller XML/JSON (nyckelvärdepar) kan begäran attributtyp undantag användas.

Du kan ange en exakt begärandehuvudet body, cookie eller fråga för attributet strängmatchning eller kan du ange delmatchningar.

Här följer stöds matchning kriterier operatorer:

- **Är lika med**: den här operatorn används efter en exakt matchning. Till exempel för att välja rubrik som heter **bearerToken** användning är lika med operatorn med väljaren som **bearerToken**.
- **Börjar med**: den här operatorn matchar alla fält som börjar med angivna väljare värde. 
- **Slutar med**: den här operatorn matchar alla fält i begäran som slutar med angivna väljare värde. 
- **Innehåller**: den här operatorn matchar alla fält i begäran som innehåller angivna väljare värde.

I samtliga fall matchar är skiftlägeskänsligt och reguljära uttryck är inte tillåtna som väljare.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat inställningarna WAF du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway-diagnostik](application-gateway-diagnostics.md#diagnostic-logging).