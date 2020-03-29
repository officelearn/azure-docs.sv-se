---
title: SQL-konstanter i Azure Cosmos DB
description: Lär dig mer om hur SQL-frågekonstanterna i Azure Cosmos DB används för att representera ett visst datavärde
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: cca62c358037dbe99fd16746ee081b1540161df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873428"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL-frågekonstanter  

 En konstant, även känd som ett litteralt eller ett skalärvärde, är en symbol som representerar ett visst datavärde. Formatet på en konstant beror på datatypen för det värde som den representerar.  
  
 **Skaldatatyper som stöds:**  
  
|**Typ**|**Värden ordning**|  
|-|-|  
|**Odefinierad**|Ett värde: **odefinierat**|  
|**Null**|Ett värde: **null**|  
|**Boolean**|Värden: **false**, **true**.|  
|**Nummer**|Ett flyttalsnummer med dubbel precision, IEEE 754-standard.|  
|**Sträng**|En sekvens med noll eller fler Unicode-tecken. Strängar måste omges av enkla eller dubbla citattecken.|  
|**Matris**|En sekvens med noll eller fler element. Varje element kan vara ett värde av valfri skalärdatatyp, förutom **Odefinierad**.|  
|**Objekt**|En oordnad uppsättning med noll eller fler namn-/värdepar. Namn är en Unicode-sträng, värdet kan vara av vilken skalär datatyp som helst, förutom **Odefinierad**.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Syntax
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="arguments"></a><a name="bk_arguments"></a>Argument
  
* `<undefined_constant>; Undefined`  
  
  Representerar odefinierat värde av typen Odefinierad.  
  
* `<null_constant>; null`  
  
  Representerar **null-värdet** av typen **Null**.  
  
* `<boolean_constant>`  
  
  Representerar konstant av typen Boolean.  
  
* `false`  
  
  Representerar **falskt** värde av typen Boolean.  
  
* `true`  
  
  Representerar **sant** värde av typen Boolean.  
  
* `<number_constant>`  
  
  Representerar en konstant.  
  
* `decimal_literal`  
  
  Decimallitteraler är tal som representeras med antingen decimalnotation eller vetenskaplig notation.  
  
* `hexadecimal_literal`  
  
  Hexadecimala litteraler är tal som representeras med prefixet "0x" följt av en eller flera hexadecimala siffror.  
  
* `<string_constant>`  
  
  Representerar en konstant av typen String.  
  
* `string _literal`  
  
  Stränglitteraler är Unicode-strängar som representeras av en sekvens av noll eller fler Unicode-tecken eller escape-sekvenser. Stränglitteraler omges av enstaka citattecken (apostrof: ' ) eller dubbla citattecken (citattecken: ").  
  
  Följande utrymningssekvenser är tillåtna:  
  
|**Escape-sekvens**|**Beskrivning**|**Unicode-tecken**|  
|-|-|-|  
|\\'|apostrof (')|U+0027|  
|\\"|citattecken (")|U+0022|  
|\\\ |omvänd solidus\\( )|U+005C|  
|\\/|solidus (/)|U+002F|  
|\b|Backsteg|U+0008|  
|\f|formulärmatning|U+000C|  
|\n|linjematning|U+000A|  
|\r (på)|Vagnretur|U+000D|  
|\t|Tab|U+0009|  
|\uXXXX|Ett Unicode-tecken definierat med 4 hexadecimala siffror.|U+XXXX|  

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdata](modeling-data.md)
