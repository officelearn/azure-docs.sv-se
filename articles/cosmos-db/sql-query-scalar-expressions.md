---
title: Skalsatser i Azure Cosmos DB SQL-frågor
description: Lär dig mer om skalensuttryck SQL-syntaxen för Azure Cosmos DB. I den här artikeln beskrivs också hur du kombinerar skaluttryck till komplexa uttryck med hjälp av operatorer.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: f8c98915ad3b682af00492acc7bc51672ec874a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870742"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Skalsatser i Azure Cosmos DB SQL-frågor

[SELECT-satsen](sql-query-select.md) stöder skaläruttryck. Ett skaläruttryck är en kombination av symboler och operatorer som kan utvärderas för att erhålla ett enda värde. Exempel på skaluttryck är: konstanter, egenskapsreferenser, matriselementreferenser, aliasreferenser eller funktionsanrop. Skaläruttryck kan kombineras till komplexa uttryck med hjälp av operatorer.

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
  
   Representerar ett konstant värde. Se avsnittet [Konstanter](sql-query-constants.md) för mer information.  
  
- `input_alias`  
  
   Representerar ett värde `input_alias` som definieras `FROM` av den som införs i satsen.  
  Det här värdet är garanterat att det inte är **odefinierat** –**odefinierade** värden i indata hoppas över.  
  
- `<scalar_expression>.property_name`  
  
   Representerar ett värde för egenskapen för ett objekt. Om egenskapen inte finns eller om egenskapen refereras till för ett värde, som inte är ett objekt, utvärderas uttrycket till **odefinierat** värde.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Representerar ett värde för `property_name` egenskapen med `array_index` namn eller matriselement med index för en matris. Om egenskaps-/matrisindexet inte finns eller om egenskaps-/matrisindexet refereras till för ett värde som inte är ett objekt/matris, utvärderas uttrycket till odefinierat värde.  
  
- `unary_operator <scalar_expression>`  
  
   Representerar en operator som tillämpas på ett enda värde. Mer information finns i avsnittet [Operatörer.](sql-query-operators.md)  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Representerar en operator som tillämpas på två värden. Mer information finns i avsnittet [Operatörer.](sql-query-operators.md)  
  
- `<scalar_function_expression>`  
  
   Representerar ett värde som definieras av ett resultat av ett funktionsanrop.  
  
- `udf_scalar_function`  
  
   Namn på den användardefinierade skalärfunktionen.  
  
- `builtin_scalar_function`  
  
   Namn på den inbyggda skalärfunktionen.  
  
- `<create_object_expression>`  
  
   Representerar ett värde som erhålls genom att skapa ett nytt objekt med angivna egenskaper och deras värden.  
  
- `<create_array_expression>`  
  
   Representerar ett värde som erhålls genom att skapa en ny matris med angivna värden som element  
  
- `parameter_name`  
  
   Representerar ett värde för det angivna parameternamnet. Parameternamn måste ha \@ en enda som första tecken.  
  
## <a name="remarks"></a>Anmärkningar
  
  När du anropar en inbyggd eller användardefinierad skalärfunktion måste alla argument definieras. Om något av argumenten är odefinierat anropas inte funktionen och resultatet kommer att vara odefinierat.  
  
  När du skapar ett objekt hoppas alla egenskaper som har tilldelats odefinierat värde över och inkluderas inte i det skapade objektet.  
  
  När du skapar en matris hoppas alla elementvärde som tilldelas **odefinierat** värde över och inkluderas inte i det skapade objektet. Detta medför att nästa definierade element tar dess plats på ett sådant sätt att den skapade matrisen inte har hoppat över index.  

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

I följande fråga är resultatet av skaluttrycket booleskt:

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
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Underfrågor](sql-query-subquery.md)