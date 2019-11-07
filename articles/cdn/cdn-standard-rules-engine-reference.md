---
title: Azure CDN Standard regel motor referens | Microsoft Docs
description: Referens dokumentation för Azure CDN Standard regel motor matchar villkor och åtgärder.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615941"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>Azure CDN från Microsofts regel motor referens

Den här artikeln innehåller detaljerade beskrivningar av de tillgängliga matchnings villkoren och funktionerna för [standard regel motorn](cdn-standard-rules-engine.md)för Azure Content Delivery Network (CDN).

Regel motorn är utformad för att vara den slutliga behörigheten för hur vissa typer av begär Anden bearbetas av CDN.

**Vanliga användnings områden**:

- Åsidosätt eller definiera en anpassad princip för cachelagring.
- Omdirigera begär Anden.
- Ändra HTTP-begäran och svarshuvuden

## <a name="terminology"></a>Terminologi

En regel definieras genom användning av [**matchnings villkor**](cdn-standard-rules-engine-match-conditions.md)och [**åtgärder**](cdn-standard-rules-engine-actions.md). Dessa element är markerade i följande bild:

 ![Struktur för CDN-regler](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Varje regel kan ha upp till 4 matchnings villkor och tre åtgärder. Det finns högst 5 regler per CDN-slutpunkt. Det finns dessutom en regel på plats som standard som kallas **Global regel**. Det här är en regel utan matchnings villkor, där de åtgärder som definierats inom alltid ska utlösas. Den här regeln ingår i den aktuella 5-regel begränsningen.

## <a name="syntax"></a>Syntax

Det sätt på vilket specialtecken behandlas varierar beroende på hur ett matchnings villkor eller actopm hanterar text värden. Ett matchnings villkor eller en funktion kan tolka text på något av följande sätt:

1. [**Litterala värden**](#literal-values)
2. [**Jokertecken**](#wildcard-values)


### <a name="literal-values"></a>Litterala värden

Text som tolkas som ett litteralt värde behandlar alla specialtecken, med undantag för symbolen%, som en del av värdet som måste matchas. Ett exakt matchnings villkor som är inställt på `\'*'\` uppfylls bara när det exakta värdet (`\'*'\`) hittas.

Ett procent tecken används för att indikera URL-kodning (till exempel `%20`).

### <a name="wildcard-values"></a>Jokertecken

Text som tolkas som ett jokertecken tilldelar ytterligare en mening till specialtecken. I följande tabell beskrivs hur följande tecken uppsättning tolkas:

Jokerteck | Beskrivning
----------|------------
\ | Ett omvänt snedstreck används för att undanta de tecken som anges i den här tabellen. Ett omvänt snedstreck måste anges direkt före det specialtecken som ska undantas.<br/>Följande syntax kan till exempel undanta en asterisk: `\*`
% | Ett procent tecken används för att indikera URL-kodning (till exempel `%20`).
\* | En asterisk är ett jokertecken som representerar ett eller flera tecken.
Rymd | Ett blank steg anger att ett matchnings villkor kan uppfyllas av något av de angivna värdena eller mönstren.
värde | Ett enkelt citat har ingen särskild betydelse. En uppsättning enkla citat tecken används dock för att ange att ett värde ska behandlas som ett litteralt värde. Den kan användas på följande sätt:<br><br/>– Det gör att ett matchnings villkor uppfylls när det angivna värdet matchar någon del av jämförelse värdet.  `'ma'` skulle till exempel matcha någon av följande strängar: <br/><br/>/Business/**ma**rathon/Asset.htm<br/>**ma**p. gif<br/>/business/template. **ma**p<br /><br />– Det gör att ett specialtecken kan anges som ett litteralt tecken. Du kan till exempel ange ett tecken för tecken avstånd genom att omsluta ett blank steg i en uppsättning enkla citat tecken (`' '` eller `'sample value'`).<br/>– Det gör att du kan ange ett tomt värde. Ange ett tomt värde genom att ange en uppsättning enkla citat tecken (det vill säga).<br /><br/>**Viktigt!**<br/>-Om det angivna värdet inte innehåller ett jokertecken betraktas det automatiskt som ett tecken värde, vilket innebär att det inte är nödvändigt att ange en uppsättning enkla citat tecken.<br/>– Om ett omvänt snedstreck inte översätter ett annat tecken i tabellen, ignoreras det när det anges i en uppsättning enkla citat tecken.<br/>Ett annat sätt att ange ett specialtecken som ett litteralt tecken är att kringgå det med ett omvänt snedstreck (det vill säga `\`).

## <a name="next-steps"></a>Nästa steg

- [Standard regel motor matchnings villkor](cdn-standard-rules-engine-match-conditions.md)
- [Motor åtgärder för standard regler](cdn-standard-rules-engine-actions.md)
- [Använd HTTPS med standard regel motorn](cdn-standard-rules-engine.md)
- [Översikt över Azure CDN](cdn-overview.md)
