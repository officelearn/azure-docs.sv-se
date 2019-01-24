---
title: SQLRuleAction-syntaxreferens i Azure | Microsoft Docs
description: Information om SQLRuleAction grammatik.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: 0f9365b72da1cec81eed82756097d32b1d72ca71
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847738"
---
# <a name="sqlruleaction-syntax"></a>SQLRuleAction-syntax

En *SqlRuleAction* är en instans av den [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) klass och representerar uppsättning åtgärder som är skrivna på SQL-språk-baserad syntax som utförs mot en [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Den här artikeln innehåller information om SQL-grammatik regeln åtgärd.  
  
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
  
-   `<scope>` är en valfri sträng som anger omfattningen för den `<property_name>`. Giltiga värden är `sys` eller `user`. Den `sys` värdet anger system omfång där `<property_name>` är en offentlig egenskapsnamnet på den [BrokeredMessage klass](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` Anger användaromfattning där `<property_name>` är en nyckel för den [BrokeredMessage klass](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) ordlista. `user` omfånget är standardvärde om `<scope>` har inte angetts.  
  
### <a name="remarks"></a>Kommentarer  

Ett försök att komma åt en icke-existerande systemegenskap är ett fel när ett försök att komma åt en icke-existerande användaregenskap inte är ett fel. I stället utvärderas en egenskap för icke-existerande internt som ett okänt värde. Ett okänt värde behandlas särskilt under operatorn utvärdering.  
  
## <a name="propertyname"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argument  
 `<regular_identifier>` är en sträng som representeras av följande reguljära uttryck:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Det innebär att alla strängar som börjar med en bokstav och följs av en eller flera understreck/bokstav/siffra.  
  
 `[:IsLetter:]` innebär att alla Unicode-tecken som kategoriseras som en Unicode-bokstav. `System.Char.IsLetter(c)` Returnerar `true` om `c` är en Unicode-bokstav.  
  
 `[:IsDigit:]` innebär att alla Unicode-tecken som kategoriseras som en hexadecimal siffra. `System.Char.IsDigit(c)` Returnerar `true` om `c` är en Unicode-siffra.  
  
 En `<regular_identifier>` får inte vara ett reserverat nyckelord.  
  
 `<delimited_identifier>` är en sträng som omges av hakparenteser ([]) för åt vänster och höger. Höger hakparentes representeras av två rätt hakparenteser. Följande är exempel på `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` är valfri sträng som omges av dubbla citattecken. Ett dubbelt citattecken i identifierare representeras av två dubbla citattecken. Du bör inte använda identifierare eftersom den enkelt kan blandas ihop med en strängkonstant. Använd om möjligt en avgränsad identifierare. Följande är ett exempel på `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Mönstret  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Kommentarer
  
 `<pattern>` måste vara ett uttryck som utvärderas som en sträng. Den används som ett mönster för operatorn.      Den kan innehålla följande jokertecken:  
  
-   `%`:  Valfri sträng med noll eller flera tecken.  
  
-   `_`: Ett enskilt tecken.  
  
## <a name="escapechar"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Kommentarer
  
 `<escape_char>` måste vara ett uttryck som utvärderas som en sträng med längden 1. Den används som escape-tecken för operatorn.  
  
 Till exempel `property LIKE 'ABC\%' ESCAPE '\'` matchar `ABC%` i stället för en sträng som börjar med `ABC`.  
  
## <a name="constant"></a>konstant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argument  
  
-   `<integer_constant>` är en sträng som inte är inom citattecken och inte innehålla punkter. Värdena lagras som `System.Int64` internt, och följer samma intervall.  
  
     Här följer några exempel på lång konstanter:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` är en sträng med tal som inte är inom citattecken och innehåller ett decimaltecken. Värdena lagras som `System.Double` internt, och följer samma intervall/precisionen.  
  
     I en framtida version numret kan lagras i en annan datatyp för exakta numeriska semantik, så du inte bör förlita dig på faktumet den underliggande-datatypen är `System.Double` för `<decimal_constant>`.  
  
     Här följer några exempel på decimal konstanter:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` är ett antal skriftliga i matematisk notation. Värdena lagras som `System.Double` internt, och följer samma intervall/precisionen. Här följer några exempel på ungefärliga numeriska konstanter:  
  
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
  
Booleska konstanter representeras av nyckelord `TRUE` eller `FALSE`. Värdena lagras som `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Kommentarer
  
Strängkonstanter är inom enkla citattecken och innehåller giltig Unicode-tecken. Ett enkelt citattecken inbäddad i en strängkonstant representeras som två enkla citattecken.  
  
## <a name="function"></a>funktion  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Kommentarer  

Den `newid()` returnerar en **System.Guid** genereras av den `System.Guid.NewGuid()` metoden.  
  
Den `property(name)` funktionen returnerar värdet på egenskapen som refereras av `name`. Den `name` värdet kan vara ett uttryck som returnerar ett strängvärde.  
  
## <a name="considerations"></a>Överväganden

- Ställ in används för att skapa en ny egenskap eller uppdatera värdet för en befintlig egenskap.
- Ta bort används för att ta bort en egenskap.
- SET utför implicit konvertering om möjligt när uttryckstyp och befintliga egenskapstypen är olika.
- Åtgärden misslyckas om icke-existerande Systemegenskaper refererade.
- Åtgärden misslyckas inte om icke-existerande användaregenskaper refererade.
- En icke-existerande användaregenskap utvärderas som ”okänt” internt, följa samma semantik som [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) vid utvärdering av operatörer.

## <a name="next-steps"></a>Nästa steg

- [SQLRuleAction-klass](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter-klass](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
