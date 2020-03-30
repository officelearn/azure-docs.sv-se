---
title: Principinställningar för brandvägg för webbprogram med Azure Front Door
description: Lär dig brandvägg för webbprogram (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932606"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Principinställningar för brandvägg för webbprogram på Azure Front Door

Med en WAF-princip (Web Application Firewall) kan du styra åtkomsten till dina webbprogram med hjälp av en uppsättning anpassade och hanterade regler. WAF-principnamnet måste vara unikt. Ett valideringsfel visas om du försöker använda ett befintligt namn. Det finns flera inställningar på principnivå som gäller för alla regler som anges för den principen enligt beskrivningen i den här artikeln.

## <a name="waf-state"></a>WAF-tillstånd

En WAF-policy för ytterdörren kan vara i något av följande två stater:
- **Aktiverad:** När en princip är aktiverad inspekterar WAF aktivt inkommande begäranden och vidtar motsvarande åtgärder enligt regeldefinitioner
- **Inaktiverad:** - När en princip är inaktiverad pausas WAF-inspektionen. Inkommande begäranden kommer att kringgå WAF och skickas till back-ends baserat på front door routing.

## <a name="waf-mode"></a>WAF-läge

WAF-principen kan konfigureras för att köras i följande två lägen:

- **Identifieringsläge** När waf körs i identifieringsläge vidtar du inga andra åtgärder än övervakare och loggar begäran och dess matchade WAF-regel till WAF-loggar. Aktivera loggningsdiagnostik för ytterdörren (när du använder portalen kan detta uppnås genom att gå till avsnittet **Diagnostik** i Azure-portalen).

- **Förebyggande läge** När WAF är konfigurerat för att köras i förebyggande läge vidtar den angivna åtgärden om en begäran matchar en regel. Alla matchade begäranden loggas också i WAF-loggarna.

## <a name="waf-response-for-blocked-requests"></a>WAF-svar för blockerade begäranden

Som standard, när WAF blockerar en begäran på grund av en matchad regel, returnerar den en 403-statuskod med - **Begäran blockeras** meddelande. En referenssträng returneras också för loggning.

Du kan definiera en anpassad svarsstatuskod och svarsmeddelande när en begäran blockeras av WAF. Följande anpassade statuskoder stöds:

- 200 OK
- 403 Förbjudet
- 405 Metoden är inte tillåten
- 406 Inte acceptabelt
- 429 För många förfrågningar

Anpassad svarsstatuskod och svarsmeddelande är en principnivåinställning. När den har konfigurerats får alla blockerade begäranden samma anpassade svarsstatus och svarsmeddelande.

## <a name="uri-for-redirect-action"></a>URI för omdirigeringsåtgärder

Du måste definiera en URI för att omdirigera begäranden till om **omdirigera-åtgärden** har valts för någon av reglerna i en WAF-princip. Den här omdirigerings-URI:n måste vara en giltig HTTP(S)-plats och när den har konfigurerats omdirigeras alla matchningsregler med en "OMDIRIGERA"-åtgärd till den angivna platsen.


## <a name="next-steps"></a>Nästa steg
- Lär dig hur du definierar [anpassade WAF-svar](waf-front-door-configure-custom-response-code.md)
