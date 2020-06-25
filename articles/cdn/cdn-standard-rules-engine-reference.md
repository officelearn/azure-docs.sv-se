---
title: Motor referens för standard regler för Azure CDN | Microsoft Docs
description: Referens dokumentation för matchnings villkor och åtgärder i standard regel motorn för Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/22/2020
ms.author: allensu
ms.openlocfilehash: 5cb053a87293a4309a393bd9e0e76bf0d881dd71
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85322188"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Referens för standardregelmotor för Azure CDN

I [standard regel motorn](cdn-standard-rules-engine.md) för Azure Content Delivery Network (Azure CDN) består en regel av ett eller flera matchnings villkor och en åtgärd. Den här artikeln innehåller detaljerade beskrivningar av matchnings villkor och-funktioner som är tillgängliga i standard regel motorn för Azure CDN.

Regel motorn är utformad för att vara den slutliga behörigheten för hur vissa typer av begär Anden bearbetas av standard Azure CDN.

**Vanliga användnings områden för reglerna**:

- Åsidosätt eller definiera en anpassad princip för cachelagring.
- Omdirigera begär Anden.
- Ändra HTTP-begäran och svarshuvuden.

## <a name="terminology"></a>Terminologi

Ange [matchnings villkor](cdn-standard-rules-engine-match-conditions.md) och [åtgärder](cdn-standard-rules-engine-actions.md)för att definiera en regel i regel motorn:

 ![Regel struktur för Azure CDN](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Varje regel kan ha upp till tio matchnings villkor och fem åtgärder. Varje Azure CDN slut punkt kan ha upp till 25 regler. 

Som ingår i den här gränsen är en *Global standard regel*. Den globala regeln har inga matchnings villkor. åtgärder som definieras i en global regel utlöses alltid.

## <a name="syntax"></a>Syntax

Hur specialtecken behandlas i en regel beror på hur olika matchnings villkor och åtgärder hanterar text värden. Ett matchnings villkor eller en åtgärd kan tolka text på något av följande sätt:

- [Litterala värden](#literal-values)
- [Jokertecken](#wildcard-values)


### <a name="literal-values"></a>Litterala värden

Text som tolkas som ett litteralt värde behandlar alla specialtecken *förutom symbolen%* som en del av värdet som måste matchas i en regel. Ett villkor för en litteral matchning är till exempel `'*'` uppfyllt endast när det exakta värdet `'*'` hittas.

Ett procent tecken används för att indikera URL-kodning (till exempel `%20` ).

### <a name="wildcard-values"></a>Jokertecken

Text som tolkas som ett jokertecken tilldelar ytterligare en mening till specialtecken. I följande tabell beskrivs hur specifika specialtecken tolkas i standard regel motorn:

Tecken | Beskrivning
----------|------------
\ | Ett omvänt snedstreck används för att undanta de tecken som anges i den här tabellen. Ett omvänt snedstreck måste anges direkt före det specialtecken som ska undantas. Följande syntax kan till exempel undanta en asterisk:`\*`
% | Ett procent tecken används för att indikera URL-kodning (till exempel `%20` ).
\* | En asterisk är ett jokertecken som representerar ett eller flera tecken.
fält | Ett blank stegs tecken anger att ett matchnings villkor kan uppfyllas av något av de angivna värdena eller mönstren.
enkla citat tecken | Ett enkelt citat tecken har ingen särskild betydelse. En uppsättning enkla citat tecken indikerar dock att ett värde ska behandlas som ett litteralt värde. Enkla citat tecken kan användas på följande sätt:<ul><li>Om du vill att ett matchnings villkor ska uppfyllas när det angivna värdet matchar någon del av jämförelse värdet.  `'ma'`Skulle exempelvis matcha någon av följande strängar: <ul><li>/Business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template. **ma**p</li></ul><li>Om du vill tillåta att ett specialtecken anges som ett litteralt tecken. Du kan till exempel ange ett tecken för tecken avstånd genom att omsluta ett blank stegs tecken i en uppsättning enkla citat tecken ( `' '` eller `'<sample value>'` ).</li><li>Om du vill tillåta att ett tomt värde anges. Ange ett tomt värde genom att ange en uppsättning enkla citat tecken (**' '**).</li></ul>**Viktigt**:<br /><ul><li>Om det angivna värdet inte innehåller ett jokertecken betraktas värdet automatiskt som ett litteralt värde. Du behöver inte ange en uppsättning enkla citat tecken för ett tecken värde.</li><li>Om ett omvänt snedstreck inte används för att undvika ett annat tecken i tabellen, ignoreras omvänt snedstreck när det anges i en uppsättning enkla citat tecken.</li><li>Ett annat sätt att ange ett specialtecken som ett litteralt tecken är att kringgå det genom att använda ett omvänt snedstreck ( `\` ).</li></ul>

## <a name="next-steps"></a>Nästa steg

- [Matchnings villkor i standard regel motorn](cdn-standard-rules-engine-match-conditions.md)
- [Åtgärder i standard regel motorn](cdn-standard-rules-engine-actions.md)
- [Kräv HTTPS med hjälp av standardregelmotorn](cdn-standard-rules-engine.md)
- [Översikt över Azure CDN](cdn-overview.md)
