---
title: "Azure Service Bus SQLFilter söksyntax | Microsoft Docs"
description: Information om SQLFilter grammatik.
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
ms.date: 02/05/2018
ms.author: sethm
ms.openlocfilehash: ec9d728eb31eb979e82bfb53cf619f823750e65c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="sqlfilter-syntax"></a>SQLFilter syntax

En *SqlFilter* objekt är en instans av den [SqlFilter klassen](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), och representerar en SQL-baserad filteruttryck som ska utvärderas mot en [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). En SqlFilter stöder en delmängd av SQL-92-standarden.  
  
 Det här avsnittet innehåller information om SqlFilter grammatik.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
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
  
## <a name="remarks"></a>Kommentarer

Ett försök att komma åt en obefintlig Systemegenskapen är ett fel när ett försök att komma åt en obefintlig egenskap inte är ett fel. I stället utvärderas en obefintlig användaregenskap internt som ett okänt värde. Ett okänt värde behandlas särskilt under utvärdering av operatorn.  
  
## <a name="propertyname"></a>property_name  
  
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
  
Denna grammatik innebär alla strängar som börjar med en bokstav och följs av en eller flera understreck/bokstav/siffra.  
  
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
  
     Det här är exempel på lång konstanter:  
  
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

Booleska konstanter som representeras av nyckelorden **SANT** eller **FALSKT**. Värdena lagras som `System.Boolean`.  
  
## <a name="stringconstant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Kommentarer  

Strängkonstanter omges av enkla citattecken och inkludera alla giltiga Unicode-tecken. Ett enkelt citattecken inbäddat i en strängkonstant representeras som två enkla citattecken.  
  
## <a name="function"></a>funktion  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Kommentarer
  
Den `newid()` fungerar returnerar en **System.Guid** genereras av den `System.Guid.NewGuid()` metoden.  
  
Den `property(name)` funktionen returnerar värdet på egenskapen som refereras av `name`. Den `name` värdet kan vara ett uttryck som returnerar ett strängvärde.  
  
## <a name="considerations"></a>Överväganden
  
Tänk på följande [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) semantik:  
  
-   Egenskapsnamnen är skiftlägeskänsliga.  
  
-   Operatörer Följ C# implicit konvertering semantik när det är möjligt.  
  
-   Systemegenskaper är offentliga egenskaper som exponeras i [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) instanser.  
  
    Tänk på följande `IS [NOT] NULL` semantik:  
  
    -   `property IS NULL`utvärderas som `true` om egenskapen finns inte eller egenskapens värde är `null`.  
  
### <a name="property-evaluation-semantics"></a>Egenskapen utvärdering semantik  
  
-   Ett försök att utvärdera en obefintlig Systemegenskapen utlöser en [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) undantag.  
  
-   En egenskap som inte finns internt utvärderas som **okänd**.  
  
 Okänd utvärdering i aritmetiska operatorerna:  
  
-   För binära operatorer, om de vänster eller höger sida av operander utvärderas som **okänd**, och sedan resultatet är **okänd**.  
  
-   För unära operatorer, om en operand utvärderas som **okänd**, och sedan resultatet är **okänd**.  
  
 Okänd utvärdering i binära operatorer:  
  
-   Om de vänster eller höger sida av operander utvärderas som **okänd**, och sedan resultatet är **okänd**.  
  
 Okänd utvärdering i `[NOT] LIKE`:  
  
-   Om operanden alla utvärderas som **okänd**, och sedan resultatet är **okänd**.  
  
 Okänd utvärdering i `[NOT] IN`:  
  
-   Om den vänstra operanden utvärderas som **okänd**, och sedan resultatet är **okänd**.  
  
 Okänd utvärdering i **och** operator:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Okänd utvärdering i **eller** operator:  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Operatorn bindning semantik
  
-   Jämförelseoperatorer som `>`, `>=`, `<`, `<=`, `!=`, och `=` följer samma semantik som C#-operatör som bindningen i data typen kampanjer och implicita konverteringar.  
  
-   Aritmetiska operatorer som `+`, `-`, `*`, `/`, och `%` följer samma semantik som C#-operatör som bindningen i data typen kampanjer och implicita konverteringar.

## <a name="next-steps"></a>Nästa steg

- [SQLFilter klass (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter klass (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.filters.sqlfilter)
- [SQLRuleAction-klass](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)