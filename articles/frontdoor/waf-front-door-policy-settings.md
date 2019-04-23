---
title: Inställningar för Brandvägg för webbaserade program med Azure ytterdörren
description: Läs om Brandvägg för webbaserade program (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797471"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Inställningar för Brandvägg för webbaserade program med Azure ytterdörren

En princip för Web Application Firewall (WAF) kan du styra åtkomsten till dina webbprogram genom en uppsättning anpassade och hanterade regler. WAF principens namn måste vara unikt. Du får ett valideringsfel om du försöker använda ett befintligt namn. Det finns flera på inställningar som gäller för alla regler som angetts för principen som beskrivs i den här artikeln.

## <a name="waf-state"></a>WAF-tillstånd

En WAF-princip för ytterdörren kan vara i något av följande två tillstånd:
- **Aktiverad:** När en princip aktiveras WAF är aktivt att granska inkommande begäranden och tar motsvarande åtgärder enligt Regeldefinitioner
- **Inaktiverat:** – när en princip har inaktiverats kan WAF-kontroll har pausats. Inkommande begäranden kringgå WAF och skickas till serverprogram utifrån ytterdörren routning.

## <a name="waf-mode"></a>WAF-läge

WAF-princip kan konfigureras för att köras i följande två lägen:

- **Identifieringsläge** när i identifieringsläge, WAF inte vidta några åtgärder förutom Övervakare och loggar begäran och dess matchade WAF-regel på WAF-loggar. Aktivera loggningsdiagnostik för ytterdörren (när du använder portalen detta kan uppnås genom att gå till den **diagnostik** avsnitt i Azure-portalen).

- **Förhindringsläge** när den konfigurerats för att köras i förhindringsläge, WAF tar den angivna åtgärden om en begäran matchar en regel. Alla matchade förfrågningar loggas också i WAF-loggar.

## <a name="waf-response-for-blocked-requests"></a>WAF-svar för blockerade begäranden

Som standard när WAF blockeras av en begäran på grund av en matchande regel returnerar statuskoden 403 med - **begäran blockeras** meddelande. En referenssträng returneras också för loggning.

Du kan definiera en anpassad svarsstatuskod och svarsmeddelandet när en begäran har blockerats av WAF. Följande anpassade statuskoder som stöds:

- 200    OK
- 403 Åtkomst nekas
- 405 Metoden tillåts inte
- 406 inte godkänd
- 429 för många begäranden

Anpassade svar och svarstiden statusmeddelande är en nivå principinställning. När den har konfigurerats kan hämta alla blockerade begäranden samma anpassade svarsstatus och svarsmeddelande.

## <a name="uri-for-redirect-action"></a>URI för omdirigeringen

Du måste definiera en URI för att omdirigera begäranden till om den **OMDIRIGERA** åtgärd har valts för någon av reglerna i en WAF-princip. Omdirigeringen URI: N måste vara en giltig HTTP (S)-plats och när tjänsten har konfigurerats, alla begäranden som matchningsregler med en ”OMDIRIGERING”-åtgärd kommer att omdirigeras till den angivna platsen.


## <a name="next-steps"></a>Nästa steg
- Lär dig hur du definierar WAF [anpassade svar](waf-front-door-configure-custom-response-code.md)
