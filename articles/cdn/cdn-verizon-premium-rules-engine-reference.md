---
title: Referens för Azure CDNs regel motor | Microsoft Docs
description: Referens dokumentation för Azure CDN regel motor matchar villkor och funktioner.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "67593153"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN från Verizon Premium-regel motor referens

Den här artikeln innehåller detaljerade beskrivningar av de tillgängliga matchnings villkoren och funktionerna för [regel motorn](cdn-verizon-premium-rules-engine.md)för Azure Content Delivery Network (CDN).

Regel motorn är utformad för att vara den slutliga behörigheten för hur vissa typer av begär Anden bearbetas av CDN.

**Vanliga användnings områden**:

- Åsidosätt eller definiera en anpassad princip för cachelagring.
- Skydda eller neka begär Anden om känsligt innehåll.
- Omdirigera begär Anden.
- Lagra anpassade logg data.

## <a name="terminology"></a>Terminologi

En regel definieras genom användning av villkors [**uttryck**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**matchnings villkor**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)och [**funktioner**](cdn-verizon-premium-rules-engine-reference-features.md). Dessa element är markerade i följande bild:

 ![Villkor för CDN-matchning](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntax

Det sätt på vilket specialtecken behandlas varierar beroende på hur ett matchnings villkor eller en funktion hanterar text värden. Ett matchnings villkor eller en funktion kan tolka text på något av följande sätt:

1. [**Litterala värden**](#literal-values)
2. [**Jokertecken**](#wildcard-values)
3. [**Reguljära uttryck**](#regular-expressions)

### <a name="literal-values"></a>Litterala värden

Text som tolkas som ett litteralt värde behandlar alla specialtecken, med undantag för symbolen%, som en del av värdet som måste matchas. Ett exakt matchnings villkor `\'*'\` är dock bara uppfyllt när det exakta värdet (det `\'*'\`vill säga) hittas.

En procent symbol används för att indikera URL-kodning (till exempel `%20`).

### <a name="wildcard-values"></a>Jokertecken

Text som tolkas som ett jokertecken tilldelar ytterligare en mening till specialtecken. I följande tabell beskrivs hur följande tecken uppsättning tolkas:

Tecken | Beskrivning
----------|------------
\ | Ett omvänt snedstreck används för att undanta de tecken som anges i den här tabellen. Ett omvänt snedstreck måste anges direkt före det specialtecken som ska undantas.<br/>Följande syntax kan till exempel undanta en asterisk:`\*`
% | En procent symbol används för att indikera URL-kodning (till exempel `%20`).
\* | En asterisk är ett jokertecken som representerar ett eller flera tecken.
Blanksteg | Ett blank steg anger att ett matchnings villkor kan uppfyllas av något av de angivna värdena eller mönstren.
värde | Ett enkelt citat har ingen särskild betydelse. En uppsättning enkla citat tecken används dock för att ange att ett värde ska behandlas som ett litteralt värde. Den kan användas på följande sätt:<br><br/>– Det gör att ett matchnings villkor uppfylls när det angivna värdet matchar någon del av jämförelse värdet.  `'ma'` Skulle exempelvis matcha någon av följande strängar: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template. **ma**p<br /><br />– Det gör att ett specialtecken kan anges som ett litteralt tecken. Du kan till exempel ange ett tecken för tecken avstånd genom att omsluta ett blank steg i en uppsättning enkla citat tecken (det `' '` vill `'sample value'`säga eller).<br/>– Det gör att du kan ange ett tomt värde. Ange ett tomt värde genom att ange en uppsättning enkla citat tecken (det vill säga).<br /><br/>**Viktigt:**<br/>-Om det angivna värdet inte innehåller ett jokertecken betraktas det automatiskt som ett tecken värde, vilket innebär att det inte är nödvändigt att ange en uppsättning enkla citat tecken.<br/>– Om ett omvänt snedstreck inte översätter ett annat tecken i tabellen, ignoreras det när det anges i en uppsättning enkla citat tecken.<br/>Ett annat sätt att ange ett specialtecken som ett litteralt tecken är att kringgå det med ett omvänt snedstreck (det `\`vill säga).

### <a name="regular-expressions"></a>Reguljära uttryck

Reguljära uttryck definierar ett mönster som genomsöks i ett text värde. Reguljär uttrycks notation definierar olika betydelser för olika symboler. I följande tabell visas hur specialtecken behandlas av matchnings villkor och funktioner som stöder reguljära uttryck.

Specialtecken | Beskrivning
------------------|------------
\ | Ett omvänt snedstreck utvärderar det tecken som följer, vilket gör att tecken behandlas som ett litteralt värde i stället för dess reguljära uttryck. Följande syntax kan till exempel undanta en asterisk:`\*`
% | Innebörden av en procent symbol beror på dess användning.<br/><br/> `%{HTTPVariable}`: Den här syntaxen identifierar en HTTP-variabel.<br/>`%{HTTPVariable%Pattern}`: I den här syntaxen används ett procent tecken för att identifiera en HTTP-variabel och som avgränsare.<br />`\%`: Om du hoppar över ett procent tecken kan det användas som ett litteralt värde eller för att indikera URL-kodning ( `\%20`till exempel).
\* | En asterisk tillåter att föregående tecken matchas noll eller flera gånger.
Blanksteg | Ett blank stegs tecken behandlas vanligt vis som ett tecken.
värde | Enkla citat tecken behandlas som litterala tecken. En uppsättning enkla citat tecken har ingen särskild betydelse.

## <a name="next-steps"></a>Nästa steg

- [Regelmotor – matchningsvillkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regelmotor – villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regelmotor – funktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Åsidosätt HTTP-beteende med regel motorn](cdn-verizon-premium-rules-engine.md)
- [Översikt över Azure CDN](cdn-overview.md)