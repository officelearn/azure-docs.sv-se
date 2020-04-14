---
title: Standardregler motorreferens för Azure CDN | Microsoft-dokument
description: Referensdokumentation för matchningsvillkor och åtgärder i standardreglersmotorn för Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 6d4fa4451c3db3d6f2a506eabd5676d18b0219f4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259909"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Referens för standardregelmotor för Azure CDN

I [standardregelmotorn](cdn-standard-rules-engine.md) för Azure Content Delivery Network (Azure CDN) består en regel av ett eller flera matchningsvillkor och en åtgärd. Den här artikeln innehåller detaljerade beskrivningar av matchningsvillkor och funktioner som är tillgängliga i standardreglersmotorn för Azure CDN.

Regelmotorn är utformad för att vara den slutliga behörigheten för hur specifika typer av begäranden bearbetas av Standard Azure CDN.

**Vanliga användningsområden för reglerna:**

- Åsidosätt eller definiera en anpassad cacheprincip.
- Omdirigera begäranden.
- Ändra HTTP-begäranden och svarshuvuden.

## <a name="terminology"></a>Terminologi

Om du vill definiera en regel i regelmotorn ställer du in [matchningsvillkor](cdn-standard-rules-engine-match-conditions.md) och [åtgärder:](cdn-standard-rules-engine-actions.md)

 ![Azure CDN-regelstruktur](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Varje regel kan ha upp till fyra matchvillkor och tre åtgärder. Varje Azure CDN-slutpunkt kan ha upp till fem regler. 

I den aktuella femregelgränsen för en Azure CDN-slutpunkt ingår en *global standardregel*. Den globala regeln har inte matchningsvillkor och åtgärder som definieras i en global regel utlöser alltid.

## <a name="syntax"></a>Syntax

Hur specialtecken behandlas i en regel varierar beroende på hur olika matchningsvillkor och åtgärder hanterar textvärden. Ett matchningsvillkor eller en matchningsåtgärd kan tolka text på något av följande sätt:

- [Litterala värden](#literal-values)
- [Jokerteckenvärden](#wildcard-values)


### <a name="literal-values"></a>Litterala värden

Text som tolkas som ett litteralt värde behandlar alla specialtecken *utom %-symbolen* som en del av värdet som måste matchas i en regel. Ett litteralt matchningsvillkor `'*'` som bara är `'*'` uppfyllt när det exakta värdet hittas.

Ett procenttecken används för att ange URL-kodning (till exempel `%20`).

### <a name="wildcard-values"></a>Jokerteckenvärden

Text som tolkas som ett jokerteckenvärde tilldelar specialtecken ytterligare mening. I följande tabell beskrivs hur specifika specialtecken tolkas i standardreglersmotorn:

Tecken | Beskrivning
----------|------------
\ | Ett omvänt snedstreck används för att undvika något av de tecken som anges i den här tabellen. Ett omvänt snedstreck måste anges direkt före det specialtecken som ska komma ut. Följande syntax undgår till exempel en asterisk:`\*`
% | Ett procenttecken används för att ange URL-kodning (till exempel `%20`).
\* | En asterisk är ett jokertecken som representerar ett eller flera tecken.
Utrymme | Ett blanksteg anger att ett matchningsvillkor kan uppfyllas av något av de angivna värdena eller mönstren.
enkla citattecken | Ett enda citattecken har ingen särskild betydelse. En uppsättning enstaka citattecken anger dock att ett värde ska behandlas som ett litteralt värde. Enkla citattecken kan användas på följande sätt:<ul><li>Så här tillåter du att ett matchningsvillkor uppfylls när det angivna värdet matchar någon del av jämförelsevärdet.  Skulle till `'ma'` exempel matcha någon av följande strängar: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif (på andra)</li><li>/business/template. ma p **(2)**</li></ul><li>Så här tillåter du att ett specialtecken anges som ett litteralt tecken. Du kan till exempel ange ett litteralt blanksteg genom att omge ett`' '` `'<sample value>'`blanksteg i en uppsättning med enkla citattecken ( eller ).</li><li>Så här tillåter du att ett tomt värde anges. Ange ett tomt värde genom att ange en uppsättning enstaka citattecken (**''**).</li></ul>**Viktigt:**<br /><ul><li>Om det angivna värdet inte innehåller ett jokertecken betraktas värdet automatiskt som ett litteralt värde. Du behöver inte ange en uppsättning enstaka citattecken för ett litteralt värde.</li><li>Om ett omvänt snedstreck inte används för att undkomma ett annat tecken i den här tabellen ignoreras omvänt snedstreck när det anges i en uppsättning enkla citattecken.</li><li>Ett annat sätt att ange ett specialtecken som ett bokstavligt tecken`\`är att undkomma det med hjälp av ett omvänt snedstreck ( ).</li></ul>

## <a name="next-steps"></a>Nästa steg

- [Matcha villkor i standardregler motorn](cdn-standard-rules-engine-match-conditions.md)
- [Åtgärder i standardregler motorn](cdn-standard-rules-engine-actions.md)
- [Kräv HTTPS med hjälp av standardregelmotorn](cdn-standard-rules-engine.md)
- [Översikt över Azure CDN](cdn-overview.md)
