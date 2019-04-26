---
title: Azure CDN regelmotor – referens | Microsoft Docs
description: Referensdokumentation för Azure CDN regelmotor – matchningsvillkor och funktioner.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 3163b33f69f4cc2d6cd4127253c7b6fadfddd6b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324049"
---
# <a name="azure-cdn-rules-engine-reference"></a>Azure CDN regelmotor – referens
Den här artikeln innehåller detaljerade beskrivningar av tillgängliga matchningsvillkor och funktioner för Azure Content Delivery Network (CDN) [regelmotor](cdn-rules-engine.md).

Regelmotorn är avsett att vara den sista utfärdaren på hur vissa typer av begäranden bearbetas av CDN.

**Vanliga användningsområden**:

- Åsidosättning eller definiera en anpassad princip.
- Säkra eller neka förfrågningar för känsligt innehåll.
- Omdirigeringsbegäranden.
- Store anpassade loggdata.

## <a name="terminology"></a>Terminologi
En regel definieras med [ **villkorsuttryck**](cdn-rules-engine-reference-conditional-expressions.md), [ **matchar de villkor som**](cdn-rules-engine-reference-match-conditions.md), och [ **funktioner**](cdn-rules-engine-reference-features.md). Dessa element är markerade på följande bild:

 ![CDN-matchningsvillkor](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntax

Det sätt som specialtecken behandlas varierar beroende på hur en matchningsvillkor eller funktion hanterar textvärden. En matchningsvillkor eller funktion kan tolka text på något av följande sätt:

1. [**Exakta värden**](#literal-values) 
2. [**Jokertecken värden**](#wildcard-values)
3. [**Reguljära uttryck**](#regular-expressions)

### <a name="literal-values"></a>Exakta värden
Text som ska tolkas som ett literalvärde behandlar alla specialtecken, med undantag för symbolen % som en del av det värde som måste matchas. Med andra ord en literal matchar villkoret har angetts som `\'*'\` uppfylls endast när som exakt värde (det vill säga `\'*'\`) finns.
 
En procentsymbol som används för att ange URL-kodning (till exempel `%20`).

### <a name="wildcard-values"></a>Jokertecken värden
Text som ska tolkas som ett jokerteckenvärde tilldelas specialtecken ytterligare betydelse. I följande tabell beskrivs hur följande uppsättning tecken tolkas:

Tecken | Beskrivning
----------|------------
\ | Ett omvänt snedstreck för escape-tecken som anges i den här tabellen. Du måste ange ett omvänt snedstreck före det specialtecken som ska undantas.<br/>Exempelvis kan du följande syntax lämnar en asterisk: `\*`
% | En procentsymbol som används för att ange URL-kodning (till exempel `%20`).
\* | En asterisk är ett jokertecken som representerar ett eller flera tecken.
Rymd | Ett blanksteg anger att ett matchningsvillkor kan uppfyllas med någon av de angivna värdena eller mönster.
'value' | Enkla citattecken har inte särskild innebörd. En uppsättning enkla citattecken används dock för att indikera att ett värde ska behandlas som ett literalvärde. Det kan användas på följande sätt:<br><br/>– Det kan ett matchningsvillkor uppfyllas när det angivna värdet matchar någon del av värdet för jämförelse.  Till exempel `'ma'` matchar någon av följande strängar: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/ företag/mall. **ma**p<br /><br />– Det kan ett specialtecken anges som en teckensträng. Du kan till exempel ange en literal blanksteg genom att skriva ett blanksteg i en uppsättning enkla citattecken (det vill säga `' '` eller `'sample value'`).<br/>– Det kan ett tomt värde anges. Ange ett tomt värde genom att ange en uppsättning enkla citattecken (det vill säga '').<br /><br/>**Viktigt!**<br/>– Om det angivna värdet inte innehåller jokertecken, är det automatiskt vara ett exakt värde, vilket innebär att det inte är nödvändigt att ange en uppsättning enkla citattecken.<br/>– Om ett omvänt snedstreck inte escape-tecknet i den här tabellen, ignoreras den när den har angetts i en uppsättning enkla citattecken.<br/>– Ett annat sätt att ange specialtecken som ett enkelt tecken är att undvika den med hjälp av ett omvänt snedstreck (det vill säga `\`).

### <a name="regular-expressions"></a>Reguljära uttryck

Reguljära uttryck definierar ett mönster som söks igenom efter inom ett textvärde. Vanliga uttryck definierar särskild innebörd till en mängd olika symboler. Följande tabell visar hur specialtecken behandlas av matchningsvillkor och funktioner som har stöd för reguljära uttryck.

Specialtecken | Beskrivning
------------------|------------
\ | Ett omvänt snedstreck du lämnar tecknet som följer IT-avdelningen, vilket gör att tecknet ska behandlas som ett exakt värde i stället för att ta enligt dess innebörder reguljärt uttryck. Exempelvis kan du följande syntax lämnar en asterisk: `\*`
% | Enligt en procentandel symbol beror på dess användning.<br/><br/> `%{HTTPVariable}`: Den här syntaxen identifierar en HTTP-variabel.<br/>`%{HTTPVariable%Pattern}`: Den här syntaxen använder en procentsymbol för att identifiera HTTP variabeln och som avgränsare.<br />`\%`: Undantagstecken symbolen procent kan det som ska användas som ett exakt värde eller för att ange URL-kodning (till exempel `\%20`).
\* | En asterisk kan föregående tecken som ska matchas noll eller flera gånger. 
Rymd | Blanksteg är vanligtvis behandlas som en teckensträng. 
'value' | Enkla citattecken behandlas som litteraler. En uppsättning enkla citattecken har inte särskild innebörd.


## <a name="next-steps"></a>Nästa steg
* [Regelmotor – matchningsvillkor](cdn-rules-engine-reference-match-conditions.md)
* [Regelmotor – villkorliga uttryck](cdn-rules-engine-reference-conditional-expressions.md)
* [Regelmotor – funktioner](cdn-rules-engine-reference-features.md)
* [Åsidosätt HTTP beteende med regler-motor](cdn-rules-engine.md)
* [Översikt över Azure CDN](cdn-overview.md)
