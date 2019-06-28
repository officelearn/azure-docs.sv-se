---
title: Skalära uttryck i Azure Cosmos DB SQL-frågor
description: Läs mer om skalärt uttryck som SQL-syntax för Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 4464c39a45c47c680a13f3ebc34841b47ee0d7c6
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342745"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Skalära uttryck i Azure Cosmos DB SQL-frågor

Den [SELECT-satsen](sql-query-select.md) har stöd för skalära uttryck. Ett skalärt uttryck som är en kombination av symboler och operatörer som kan utvärderas för att få ett enskilt värde. Exempel på skaläruttryck är: konstanter, egenskapen referenser, matris referenser, alias referenser eller funktionsanrop. Skalärt uttryck kan kombineras till komplexa uttryck med hjälp av operatörer.

## <a name="syntax"></a>Syntax
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argument
  
- `<constant>`  
  
   Representerar ett konstant värde. Se [konstanter](sql-query-constants.md) information.  
  
- `input_alias`  
  
   Representerar ett värde som definieras av den `input_alias` introducerades i den `FROM` satsen.  
  Det här värdet är säkert att den inte **odefinierat** –**odefinierat** värden i inkommande hoppas över.  
  
- `<scalar_expression>.property_name`  
  
   Representerar ett värde för egenskapen för ett objekt. Om egenskapen inte finns eller egenskapen refererar till ett värde som inte är ett objekt, så uttrycket som utvärderas till **odefinierat** värde.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Representerar ett värde för egenskapen med namnet `property_name` eller matriselementet med index `array_index` i en matris. Om egenskapen/matrisindex finns inte eller egenskapen/matrisindex refereras på ett värde som är inte en objektmatris sedan uttrycket utvärderas till ett odefinierat värde.  
  
- `unary_operator <scalar_expression>`  
  
   Representerar en operatör som tillämpas på ett enda värde. Se [operatörer](sql-query-operators.md) information.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Representerar en operatör som tillämpas på två värden. Se [operatörer](sql-query-operators.md) information.  
  
- `<scalar_function_expression>`  
  
   Representerar ett värde som definieras av ett resultat av ett funktionsanrop.  
  
- `udf_scalar_function`  
  
   Namnet på den användardefinierade skalära funktionen.  
  
- `builtin_scalar_function`  
  
   Namnet på inbyggd skalärfunktion.  
  
- `<create_object_expression>`  
  
   Representerar ett värde som fås genom att skapa ett nytt objekt med angivna egenskaperna och deras värden.  
  
- `<create_array_expression>`  
  
   Representerar ett värde som fås genom att skapa en ny matris med angivna värden som element  
  
- `parameter_name`  
  
   Representerar ett värde för det angivna parameternamnet. Parameternamn måste ha en enda \@ som det första tecknet.  
  
## <a name="remarks"></a>Kommentarer
  
  När du anropar en inbyggda eller användardefinierade skalärfunktion måste alla argument definieras. Om något av argumenten är odefinierad kommer inte att anropa funktionen och resultatet blir odefinierad.  
  
  När du skapar ett objekt ska hoppas över egenskaper som är tilldelad odefinierat värde och inte ingår i det skapade objektet.  
  
  När skapar en matris, ett elementvärde som tilldelas **odefinierat** värde ska hoppas över och inte ingår i det skapade objektet. Detta innebär att nästa definierade element ska utföras så att den Skapa matrisen inte kommer ha hoppas över index.  

## <a name="examples"></a>Exempel

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Resultatet är:

```json
    [{
      "$1": 1.33333
    }]
```

Resultatet av det skalära uttrycket är ett booleskt värde i följande fråga:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Resultatet är:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Underfrågor](sql-query-subquery.md)