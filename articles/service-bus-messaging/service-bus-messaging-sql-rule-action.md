---
title: SQLRuleAction syntax referens i Azure | Microsoft Docs
description: Information om SQLRuleAction grammatik.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: sethm
ms.openlocfilehash: 7379b7f58563675f28d77928d933c0d9c7992e71
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="sqlruleaction-syntax"></a>SQLRuleAction syntax

En *SqlRuleAction* är en instans av den [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) klass och representerar uppsättning åtgärder på SQL-språk baserat syntax som utförs mot en [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Det här avsnittet innehåller information om SQL-grammatik regeln åtgärd.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Argument  
  
-   `<scope>`är en valfri sträng som anger omfånget för den `<property_name>`. Giltiga värden är `sys` eller `user`. Den `sys` värdet anger scope för system där `<property_name>` är en offentlig egenskapsnamnet på den [BrokeredMessage klassen](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`Anger användarens scope där `<property_name>` är en nyckel för den [BrokeredMessage klassen](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) ordlistan. `user`omfånget är standardvärde om `<scope>` har inte angetts.  
  
### <a name="remarks"></a>Kommentarer  

Ett försök att komma åt en obefintlig Systemegenskapen är ett fel när ett försök att komma åt en obefintlig egenskap inte är ett fel. I stället utvärderas en obefintlig användaregenskap internt som ett okänt värde. Ett okänt värde behandlas särskilt under utvärdering av operatorn.  
  
## <a name="propertyname"></a>kubattributbindningen  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argument  
 `<regular_identifier>`en sträng som representeras av följande reguljära uttryck:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Det innebär att alla strängar som börjar med en bokstav och följs av en eller flera understreck/bokstav/siffra.  
  
 `[:IsLetter:]`innebär att alla Unicode-tecken som är kategoriserade som ett Unicode-tecken. `System.Char.IsLetter(c)`Returnerar `true` om `c` är ett Unicode-tecken.  
  
 `[:IsDigit:]`innebär att alla Unicode-tecken som är kategoriserade som en siffra. `System.Char.IsDigit(c)`Returnerar `true` om `c` är en Unicode-siffra.  
  
 En `<regular_identifier>` får inte vara ett reserverat nyckelord.  
  
 `<delimited_identifier>`är en sträng som medföljde åt vänster och höger hakparentes ([]). Höger hakparentes representeras som två höger hakparentes. Följande är exempel på `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`är en sträng som omges av dubbla citattecken. Ett dubbelt citattecken i identifierare representeras av dubbla citattecken. Det rekommenderas inte att använda identifierare inom citattecken eftersom den enkelt kan förväxlas med en strängkonstant. Använd om möjligt en avgränsad identifierare. Följande är ett exempel på `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Mönstret  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Kommentarer
  
 `<pattern>`måste vara ett uttryck som utvärderas som en sträng. Den används som ett mönster för operatorn.      Det kan innehålla följande jokertecken:  
  
-   `%`: En sträng med noll eller flera tecken.  
  
-   `_`: Ett valfritt tecken.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Kommentarer
  
 `<escape_char>`måste vara ett uttryck som utvärderas som en sträng med längden 1. Den används som escape-tecken för LIKE-operator.  
  
 Till exempel `property LIKE 'ABC\%' ESCAPE '\'` matchar `ABC%` i stället för en sträng som börjar med `ABC`.  
  
## <a name="constant"></a>konstant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argument  
  
-   `<integer_constant>`är en sträng som inte är inom citattecken och inte innehåller decimaltecken. Värdena lagras som `System.Int64` internt, och följ samma intervall.  
  
     Följande är exempel på lång konstanter:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`är en sträng som inte är inom citattecken och innehåller ett decimaltecken. Värdena lagras som `System.Double` internt, och följ samma intervall/precision.  
  
     I en framtida version siffran kan lagras i en annan datatyp för att stödja exakta antalet semantik, så du inte bör lita på faktumet den underliggande-datatypen är `System.Double` för `<decimal_constant>`.  
  
     Följande är exempel på decimal konstanter:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`är ett antal skrivna i matematisk notation. Värdena lagras som `System.Double` internt, och följ samma intervall/precision. Följande är exempel på ungefärliga numeriska konstanter:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="booleanconstant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Kommentarer
  
Booleska konstanter som representeras av nyckelorden `TRUE` eller `FALSE`. Värdena lagras som `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Kommentarer
  
Strängkonstanter omges av enkla citattecken och inkludera alla giltiga Unicode-tecken. Ett enkelt citattecken inbäddat i en strängkonstant representeras som två enkla citattecken.  
  
## <a name="function"></a>Funktionen  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Kommentarer  

Den `newid()` fungerar returnerar en **System.Guid** genereras av den `System.Guid.NewGuid()` metoden.  
  
Den `property(name)` funktionen returnerar värdet på egenskapen som refereras av `name`. Den `name` värdet kan vara ett uttryck som returnerar ett strängvärde.  
  
## <a name="considerations"></a>Överväganden

- UPPSÄTTNINGEN används för att skapa en ny egenskap eller uppdatera värdet för en befintlig egenskap.
- Ta bort används för att ta bort en egenskap.
- Ange utför implicit konvertering om möjligt när uttryckstypen och befintliga egenskapstypen är olika.
- Åtgärden misslyckas om obefintlig Systemegenskaper har referenser till.
- Åtgärden misslyckas inte om ej existerande användaregenskaper har referenser till.
- En egenskap för obefintlig utvärderas som ”okänt” internt, följa samma semantik som [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) vid utvärdering av operatörer.

## <a name="next-steps"></a>Nästa steg

- [SQLRuleAction-klass](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter-klass](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
