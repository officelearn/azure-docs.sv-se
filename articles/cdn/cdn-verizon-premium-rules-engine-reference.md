---
title: Azure CDN regler motorreferens | Microsoft-dokument
description: Referensdokumentation för Azure CDN regler motorn matchar villkor och funktioner.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aab93204c850223756f28a56ea550f912e28e0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69996762"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN från Verizon Premium regler motorreferens

I den här artikeln visas detaljerade beskrivningar av tillgängliga matchningsvillkor och funktioner för [CDN-regelmotorn](cdn-verizon-premium-rules-engine.md)(Azure Content Delivery Network).

Regelmotorn är utformad för att vara den slutliga myndigheten för hur specifika typer av begäranden bearbetas av CDN.

**Vanliga användningsområden:**

- Åsidosätt eller definiera en anpassad cacheprincip.
- Skydda eller neka begäranden om känsligt innehåll.
- Omdirigera begäranden.
- Lagra anpassade loggdata.

## <a name="terminology"></a>Terminologi

En regel definieras med hjälp av [**villkorsuttryck,**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md) [**matchningsvillkor**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)och [**funktioner**](cdn-verizon-premium-rules-engine-reference-features.md). Dessa element markeras i följande bild:

 ![VILLKOR för CDN-matchning](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntax

Det sätt på vilket specialtecken behandlas varierar beroende på hur ett matchningsvillkor eller en funktion hanterar textvärden. Ett matchningsvillkor eller en funktion kan tolka text på något av följande sätt:

1. [**Litterala värden**](#literal-values)
2. [**Jokerteckenvärden**](#wildcard-values)
3. [**Reguljära uttryck**](#regular-expressions)

### <a name="literal-values"></a>Litterala värden

Text som tolkas som ett litteralt värde behandlar alla specialtecken, med undantag för %-symbolen, som en del av värdet som måste matchas. Med andra ord `\'*'\` är ett litteralt matchningsvillkor inställt på `\'*'\`bara uppfyllt när det exakta värdet (det vill säga ) hittas.

En procentsymbol används för att ange URL-kodning (till exempel `%20`).

### <a name="wildcard-values"></a>Jokerteckenvärden

Text som tolkas som ett jokerteckenvärde tilldelar specialtecken ytterligare mening. I följande tabell beskrivs hur följande teckenuppsättning tolkas:

Tecken | Beskrivning
----------|------------
\ | Ett omvänt snedstreck används för att undvika något av de tecken som anges i den här tabellen. Ett omvänt snedstreck måste anges direkt före det specialtecken som ska komma ut.<br/>Följande syntax undgår till exempel en asterisk:`\*`
% | En procentsymbol används för att ange URL-kodning (till exempel `%20`).
\* | En asterisk är ett jokertecken som representerar ett eller flera tecken.
Space | Ett blanksteg anger att ett matchningsvillkor kan uppfyllas av något av de angivna värdena eller mönstren.
"värde" | Ett enda citat har ingen särskild betydelse. En uppsättning enstaka citattecken används dock för att ange att ett värde ska behandlas som ett litteralt värde. Den kan användas på följande sätt:<br><br/>- Det gör att ett matchningsvillkor kan uppfyllas när det angivna värdet matchar någon del av jämförelsevärdet.  Skulle till `'ma'` exempel matcha någon av följande strängar: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif (på andra)<br/>/business/template. ma p **(2)**<br /><br />- Det gör att ett specialtecken kan anges som ett bokstavligt tecken. Du kan till exempel ange ett litteralt blanksteg genom att omge ett blanksteg `' '` `'sample value'`inom en uppsättning enstaka citattecken (det vill säga eller ).<br/>- Det gör att ett tomt värde kan anges. Ange ett tomt värde genom att ange en uppsättning enstaka citattecken (det vill vara "").<br /><br/>**Viktigt:**<br/>- Om det angivna värdet inte innehåller ett jokertecken betraktas det automatiskt som ett litteralt värde, vilket innebär att det inte är nödvändigt att ange en uppsättning enstaka citattecken.<br/>- Om ett omvänt snedstreck inte undgår ett annat tecken i den här tabellen ignoreras det när det anges inom en uppsättning med enstaka citattecken.<br/>- Ett annat sätt att ange ett specialtecken som ett bokstavligt tecken `\`är att undkomma det med ett omvänt snedstreck (det vill).

### <a name="regular-expressions"></a>Reguljära uttryck

Reguljära uttryck definierar ett mönster som söks efter i ett textvärde. Notation för reguljära uttryck definierar specifika betydelser för en mängd olika symboler. I följande tabell visas hur specialtecken behandlas med matchningsvillkor och funktioner som stöder reguljära uttryck.

Specialtecken | Beskrivning
------------------|------------
\ | Ett omvänt snedstreck undgår tecknet följer det, vilket gör att tecknet behandlas som ett bokstavligt värde istället för att ta på sig dess reguljära uttryck mening. Följande syntax undgår till exempel en asterisk:`\*`
% | Innebörden av en procentuell symbol beror på dess användning.<br/><br/> `%{HTTPVariable}`: Den här syntaxen identifierar en HTTP-variabel.<br/>`%{HTTPVariable%Pattern}`: Den här syntaxen använder en procentsymbol för att identifiera en HTTP-variabel och som avgränsare.<br />`\%`: Om du flyr från en procentsymbol kan den användas som ett litteralt `\%20`värde eller för att ange URL-kodning (till exempel ).
\* | En asterisk gör att föregående tecken kan matchas noll eller fler gånger.
Space | Ett blanksteg behandlas vanligtvis som ett litteralt tecken.
"värde" | Enstaka citattecken behandlas som litterala tecken. En uppsättning enstaka citattecken har ingen särskild betydelse.

Matcha villkor och funktioner som stöder reguljära uttryck accepterar mönster som definieras av PcRE (Perl Compatible Regular Expressions).

## <a name="next-steps"></a>Nästa steg

- [Regler motor match villkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regler motor villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regler motorfunktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Åsidosätt HTTP-beteende med hjälp av regelmotorn](cdn-verizon-premium-rules-engine.md)
- [Översikt över Azure CDN](cdn-overview.md)
