---
title: Azure CDN regler motorn referens | Microsoft Docs
description: "I referensdokumentationen för Azure CDN regler motorn matchar villkoren och funktioner."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: c10145661a8c575381493c9aaa901c3ef92c2e81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine"></a>Azure CDN regelmotor
Det här avsnittet innehåller detaljerade beskrivningar av tillgängliga matchar villkoren och funktioner för Azure Content Delivery Network (CDN) [regelmotor](cdn-rules-engine.md).

HTTP-regelmotor är avsedd att vara den sista myndigheten på hur vissa typer av begäranden bearbetas av CDN.

**Vanliga användningsområden**:

- Åsidosätta eller definiera en anpassad princip.
- Skydda eller neka begäranden för känsligt innehåll.
- Omdirigeringsbegäranden.
- Lagra anpassade loggdata.

## <a name="terminology"></a>Terminologi
En regel definieras med [ **villkorsuttryck**](cdn-rules-engine-reference-conditional-expressions.md), [ **matchar**](cdn-rules-engine-reference-match-conditions.md), och [ **funktioner**](cdn-rules-engine-reference-features.md). Dessa element markeras i följande bild.

 ![CDN matchar villkoret](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntax

Det sätt som specialtecken behandlas varierar beroende på hur en matchar villkoret eller funktion hanterar textvärden. En matchar villkoret eller funktion kan tolka text i något av följande sätt:

1. [**Teckenvärden**](#literal-values) 
2. [**Jokertecken värden**](#wildcard-values)
3. [**Reguljära uttryck**](#regular-expressions)

### <a name="literal-values"></a>Teckenvärden
Text som ska tolkas som ett litteralvärde hanterar specialtecken felaktigt, med undantag för symbolen % som en del av det värde som måste matchas. Med andra ord en literal matchar villkoret har angetts som `\'*'\` bara uppfyllas om som exakt värde (d.v.s. `\'*'\`) hittas.
 
En procentsymbol som används för att ange URL-kodning (t.ex. `%20`).

### <a name="wildcard-values"></a>Jokertecken värden
Text som ska tolkas som ett jokerteckenvärde tilldelas ytterligare betydelse specialtecken. I följande tabell beskrivs hur följande uppsättning tecken tolkas.

Tecken | Beskrivning
----------|------------
\ | Ett omvänt snedstreck för escape-tecken som anges i den här tabellen. Ett omvänt snedstreck måste anges direkt före det specialtecken som ska undantas (escaped).<br/>Till exempel hoppas följande syntax en asterisk:`\*`
% | En procentsymbol som används för att ange URL-kodning (t.ex. `%20`).
* | En asterisk är ett jokertecken som representerar ett eller flera tecken.
Diskutrymme | Ett blankstegstecken anger att en matchar villkoret kan uppfyllas med någon av de angivna värdena eller mönster.
'value' | Enkla citattecken har inte någon särskild innebörd. En uppsättning enkla citattecken används dock för att indikera att ett värde ska behandlas som ett litteralvärde. Den kan användas på följande sätt:<br><br/>-Kan matcha villkor vara uppfyllt när det angivna värdet matchar någon del av värdet för jämförelse.  Till exempel `'ma'` matchar någon av följande: <br/><br/>/Business/**ma**rathon/asset.htm<br/>**Ma**p.gif<br/>/ företag mallen. **ma**p<br /><br />-Kan ett specialtecken anges som en teckensträng. Du kan exempelvis ange literal blanksteg genom att skriva ett blanksteg i en uppsättning enkla citattecken (d.v.s. `' '` eller `'sample value'`).<br/>-Kan ett tomt värde anges. Ange ett tomt värde genom att ange en uppsättning enkla citattecken (d.v.s. '').<br /><br/>**Viktigt:**<br/>– Om det angivna värdet inte innehåller ett jokertecken, sedan betraktas automatiskt ett litteralvärde. Det innebär att det inte är nödvändigt att ange en uppsättning med enkla citattecken.<br/>– Om ett omvänt snedstreck inte escape-tecknet i den här tabellen, kommer sedan den att ignoreras när anges i en uppsättning med enkla citattecken.<br/>-Ett annat sätt att ange ett specialtecken som en teckensträng att undanta den med ett omvänt snedstreck (d.v.s. `\`).

### <a name="regular-expressions"></a>Reguljära uttryck

Reguljära uttryck definiera ett mönster som ska genomsökas för inom ett textvärde. Vanliga uttryck definierar särskild innebörd till en mängd olika symboler. Följande tabell visar hur specialtecken behandlas som matchar villkoren och funktioner som har stöd för reguljära uttryck.

Specialtecken | Beskrivning
------------------|------------
\ | Ett omvänt snedstreck hoppas tecknet du följande den. Detta gör det tecknet som ska behandlas som litteralt värde i stället för att ta med på innebörd reguljärt uttryck. Till exempel hoppas följande syntax en asterisk:`\*`
% | Enligt en procentandel symbol beror på dess användning.<br/><br/> `%{HTTPVariable}`: Den här syntaxen identifierar en HTTP-variabel.<br/>`%{HTTPVariable%Pattern}`: Den här syntaxen använder en procentsymbol för att identifiera en HTTP-variabel och som avgränsare.<br />`\%`: Undantagstecken symbolen procent gör att det ska användas som ett litteralvärde eller för att ange URL-kodning (t.ex. `\%20`).
* | En asterisk kan föregående tecken som ska matchas noll eller flera gånger. 
Diskutrymme | Ett blankstegstecken behandlas vanligtvis som en teckensträng. 
'value' | Enkla citattecken behandlas som litteraler. En uppsättning enkla citattecken har inte någon särskild innebörd.


## <a name="next-steps"></a>Nästa steg
* [Regler motorn matchar villkor](cdn-rules-engine-reference-match-conditions.md)
* [Regler motorn villkorsuttryck](cdn-rules-engine-reference-conditional-expressions.md)
* [Regler motorn funktioner](cdn-rules-engine-reference-features.md)
* [Åsidosätta standardbeteendet i HTTP-motorn regler](cdn-rules-engine.md)
* [Azure CDN-översikt](cdn-overview.md)
