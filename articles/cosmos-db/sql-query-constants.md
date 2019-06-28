---
title: SQL-konstanter i Azure Cosmos DB
description: Lär dig mer om SQL-konstanter i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: ed337c8330eb8b3e4ad97c92cca1d0c5977f9588
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342575"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL-fråga konstanter  

 En konstant, även kallat en literal eller ett skalärt värde är en symbol som representerar ett värde för specifika data. Formatet för en konstant beror på datatypen för det värde som representerar.  
  
 **Skalära datatyper som stöds:**  
  
|**Typ**|**Värden ordning**|  
|-|-|  
|**Odefinierad**|Enstaka värde: **Odefinierad**|  
|**Null**|Enstaka värde: **null**|  
|**Boolesk**|Värden: **FALSKT**, **SANT**.|  
|**Nummer**|Ett dubbel precision Flyttalsnummer, IEEE-754 som standard.|  
|**Sträng**|En sekvens med noll eller flera Unicode-tecken. Strängar måste stå inom enkla eller dubbla citattecken.|  
|**Matris**|En sekvens med noll eller flera element. Varje element kan vara ett värde av olika skalära datatyper, utom **Undefined**.|  
|**Objekt**|En osorterad uppsättning noll eller flera namn/värde-par. Namnet är en Unicode-sträng, värdet kan vara av olika skalära datatyper, utom **Undefined**.|  
  
## <a name="bk_syntax"></a>Syntax
  
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
  
##  <a name="bk_arguments"></a> argument
  
* `<undefined_constant>; Undefined`  
  
  Representerar en odefinierad värde av typen odefinierad.  
  
* `<null_constant>; null`  
  
  Representerar **null** värde av typen **Null**.  
  
* `<boolean_constant>`  
  
  Representerar konstant av typen Boolean.  
  
* `false`  
  
  Representerar **FALSKT** värde av typen Boolean.  
  
* `true`  
  
  Representerar **SANT** värde av typen Boolean.  
  
* `<number_constant>`  
  
  Representerar en konstant.  
  
* `decimal_literal`  
  
  Decimal litteraler är värden som representeras med hjälp av decimalform eller matematisk notation.  
  
* `hexadecimal_literal`  
  
  Hexadecimala strängar är värden som representeras med prefixet ”0 x” följt av en eller flera hexadecimala siffror.  
  
* `<string_constant>`  
  
  Representerar en konstant av typen String.  
  
* `string _literal`  
  
  Stränglitteraler är Unicode-strängar som representeras av en sekvens med noll eller flera Unicode-tecken eller escape-sekvenser. Stränglitteraler är inom enkla citattecken (apostrof ”:) eller dubbla citattecken (citattecken”:).  
  
  Följande escape-sekvenser tillåts:  
  
|**Escape-sekvensen**|**Beskrivning**|**Unicode-tecken**|  
|-|-|-|  
|\\'|apostrof (')|U+0027|  
|\\"|citattecken (”)|U+0022|  
|\\\ |omvänd solidus (\\)|U + 005C|  
|\\/|solidus (/)|U + 002F|  
|\b|BACKSTEG|U + 0008|  
|\f|formuläret feed|U + 000C|  
|\n|radmatning|U + 000A|  
|\r|vagnretur|U + 000D|  
|\t|tabb|U + 0009|  
|\uXXXX|En Unicode-tecken som definieras av 4 hexadecimala siffror.|U + XXXX|  

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modelldokumentdata](modeling-data.md)
