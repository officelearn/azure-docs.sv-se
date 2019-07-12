---
title: Azure CDN regelmotor – referens | Microsoft Docs
description: Referensdokumentation för Azure CDN regelmotor – matchningsvillkor och funktioner.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593153"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Azure CDN från Verizon Premium regler – referens

Den här artikeln innehåller detaljerade beskrivningar av tillgängliga matchningsvillkor och funktioner för Azure Content Delivery Network (CDN) [regelmotor](cdn-verizon-premium-rules-engine.md).

Regelmotorn är avsett att vara den sista utfärdaren på hur vissa typer av begäranden bearbetas av CDN.

**Vanliga användningsområden**:

- Åsidosättning eller definiera en anpassad princip.
- Säkra eller neka förfrågningar för känsligt innehåll.
- Omdirigeringsbegäranden.
- Store anpassade loggdata.

## <a name="terminology"></a>Terminologi

En regel definieras med [ **villkorsuttryck**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [ **matchar de villkor som**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), och [ **funktioner**](cdn-verizon-premium-rules-engine-reference-features.md). Dessa element är markerade på följande bild:

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
'value' | Enkla citattecken har inte särskild innebörd. En uppsättning enkla citattecken används dock för att indikera att ett värde ska behandlas som ett literalvärde. Det kan användas på följande sätt:<br><br/>– Det kan ett matchningsvillkor uppfyllas när det angivna värdet matchar någon del av värdet för jämförelse.  Till exempel `'ma'` matchar någon av följande strängar: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/ företag/mall. **ma**p<br /><br />– Det kan ett specialtecken anges som en teckensträng. Du kan till exempel ange en literal blanksteg genom att skriva ett blanksteg i en uppsättning enkla citattecken (det vill säga `' '` eller `'sample value'`).<br/>– Det kan ett tomt värde anges. Ange ett tomt värde genom att ange en uppsättning enkla citattecken (det vill säga '').<br /><br/>**Viktigt:**<br/>– Om det angivna värdet inte innehåller jokertecken, är det automatiskt vara ett exakt värde, vilket innebär att det inte är nödvändigt att ange en uppsättning enkla citattecken.<br/>– Om ett omvänt snedstreck inte escape-tecknet i den här tabellen, ignoreras den när den har angetts i en uppsättning enkla citattecken.<br/>– Ett annat sätt att ange specialtecken som ett enkelt tecken är att undvika den med hjälp av ett omvänt snedstreck (det vill säga `\`).

### <a name="regular-expressions"></a>Reguljära uttryck

Reguljära uttryck definierar ett mönster som söks igenom efter inom ett textvärde. Vanliga uttryck definierar särskild innebörd till en mängd olika symboler. Följande tabell visar hur specialtecken behandlas av matchningsvillkor och funktioner som har stöd för reguljära uttryck.

Specialtecken | Beskrivning
------------------|------------
\ | Ett omvänt snedstreck du lämnar tecknet som följer IT-avdelningen, vilket gör att tecknet ska behandlas som ett exakt värde i stället för att ta enligt dess innebörder reguljärt uttryck. Exempelvis kan du följande syntax lämnar en asterisk: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | An asterisk allows the preceding character to be matched zero or more times.
Space | A space character is typically treated as a literal character.
'value' | Single quotes are treated as literal characters. A set of single quotes does not have special meaning.

## Next steps

- [Rules engine match conditions](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)
- [Azure CDN overview](cdn-overview.mdEnligt en procentandel symbol beror på dess användning. Docs
descr`%{HTTPVariable}`: Den här syntaxen identifierar en HTTP-variabel.match`%{HTTPVariable%Pattern}`: Den här syntaxen använder en procentsymbol för att identifiera HTTP variabeln och som avgränsare.1/2019`\``: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\`20`).f the available match conditions and features for the Azure Content Delivery Network (CDN) [rules engine](cdn-verizon-premium-rules-engine.md).

The rules engine is designed to be the final authority on how specific types of requests are processed by the CDN.

**Common uses**:

- Override or define a custom cache policy.
- Secure or deny requests for sensitive content.
- Redirect requests.
- Store custom log data.

## Terminology

A rule is defined through the use of [**conditional expressions**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**match conditions**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), and [**features**](cdn-verizon-premium-rules-engine-reference-features.md). These elements are highlighted in the following illustration:

 ![CDN match condition](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## Syntax

The manner in which special characters are treated varies according to how a match condition or feature handles text values. A match condition or feature may interpret text in one of the following ways:

1. [**Literal values**](#literal-values)
2. [**Wildcard values**](#wildcard-values)
3. [**Regular expressions**](#regular-expressions)

### Literal values

Text that is interpreted as a literal value treats all special characters, with the exception of the % symbol, as a part of the value that must be matched. In other words, a literal match condition set to `\'*'\` is only satisfied when that exact value (that is, `\'*'\`) is found.

A percentage symbol is used to indicate URL encoding (for example, `%20`).

### Wildcard values

Text that is interpreted as a wildcard value assigns additional meaning to special characters. The following table describes how the following set of characters is interpreted:

Character | Description
----------|------------
\ | A backslash is used to escape any of the characters specified in this table. A backslash must be specified directly before the special character that should be escaped.<br/>For example, the following syntax escapes an asterisk: `\*`
% | A percentage symbol is used to indicate URL encoding (for example, `%20`).
\* | An asterisk is a wildcard that represents one or more characters.
Space | A space character indicates that a match condition may be satisfied by either of the specified values or patterns.
'value' | A single quote does not have special meaning. However, a set of single quotes is used to indicate that a value should be treated as a literal value. It can be used in the following ways:<br><br/>- It allows a match condition to be satisfied whenever the specified value matches any portion of the comparison value.  For example, `'ma'` would match any of the following strings: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- It allows a special character to be specified as a literal character. For example, you may specify a literal space character by enclosing a space character within a set of single quotes (that is, `' '` or `'sample value'`).<br/>- It allows a blank value to be specified. Specify a blank value by specifying a set of single quotes (that is, '').<br /><br/>**Important:**<br/>- If the specified value does not contain a wildcard, then it is automatically considered a literal value, which means that it is not necessary to specify a set of single quotes.<br/>- If a backslash does not escape another character in this table, it is ignored when it is specified within a set of single quotes.<br/>- Another way to specify a special character as a literal character is to escape it using a backslash (that is, `\`).

### Regular expressions

Regular expressions define a pattern that is searched for within a text value. Regular expression notation defines specific meanings to a variety of symbols. The following table indicates how special characters are treated by match conditions and features that support regular expressions.

Special Character | Description
------------------|------------
\ | A backslash escapes the character the follows it, which causes that character to be treated as a literal value instead of taking on its regular expression meaning. For example, the following syntax escapes an asterisk: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | En asterisk kan föregående tecken som ska matchas noll eller flera gånger.
Rymd | Blanksteg är vanligtvis behandlas som en teckensträng.
'value' | Enkla citattecken behandlas som litteraler. En uppsättning enkla citattecken har inte särskild innebörd.

## <a name="next-steps"></a>Nästa steg

- [Regelmotor – matchningsvillkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regelmotor – villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regelmotor – funktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Åsidosätt HTTP beteende med regler-motor](cdn-verizon-premium-rules-engine.md)
- [Översikt över Azure CDN](cdn-overview.md)