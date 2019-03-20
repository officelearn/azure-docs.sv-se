---
title: Referens för Azure Service Bus SQLFilter-syntax | Microsoft Docs
description: Information om SQLFilter grammatik.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: e490c7c24ed38e2988c1f097b09b508746f08178
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118143"
---
# <a name="sqlfilter-syntax"></a>SQLFilter-syntax

En *SqlFilter* objekt är en instans av den [SqlFilter klass](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), och representerar en SQL-baserad filteruttryck som ska utvärderas mot en [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Ett SqlFilter stöder en delmängd av den SQL-92-standarden.  
  
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
  
-   `<scope>` är en valfri sträng som anger omfattningen för den `<property_name>`. Giltiga värden är `sys` eller `user`. Den `sys` värdet anger system omfång där `<property_name>` är en offentlig egenskapsnamnet på den [BrokeredMessage klass](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` Anger användaromfattning där `<property_name>` är en nyckel för den [BrokeredMessage klass](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) ordlista. `user` omfånget är standardvärde om `<scope>` har inte angetts.  
  
## <a name="remarks"></a>Kommentarer

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
  
Den här grammatik innebär att alla strängar som börjar med en bokstav och följs av en eller flera understreck/bokstav/siffra.  
  
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
  
     Det här är exempel på lång konstanter:  
  
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

Booleska konstanter representeras av nyckelord **SANT** eller **FALSKT**. Värdena lagras som `System.Boolean`.  
  
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
  
Tänk på följande [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) semantik:  
  
-   Egenskapsnamnen är skiftlägeskänsliga.  
  
-   Operatörer Följ C# implicit konvertering semantik när det är möjligt.  
  
-   Systemegenskaper är offentliga egenskaper som visas i [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) instanser.  
  
    Tänk på följande `IS [NOT] NULL` semantik:  
  
    -   `property IS NULL` utvärderas som `true` om egenskapen finns inte eller egenskapens värde är `null`.  
  
### <a name="property-evaluation-semantics"></a>Egenskapen utvärdering semantik  
  
- Ett försök att utvärdera en obefintlig systemegenskap utlöser en [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) undantag.  
  
- En egenskap som inte finns utvärderas internt som **okänd**.  
  
  Okänd utvärdering i aritmetiska operatorer:  
  
- För binära operatorerna som om de vänster eller höger sida av operander utvärderas som **okänd**, och sedan resultatet är **okänd**.  
  
- För unära operatorer, om en operand utvärderas som **okänd**, och sedan resultatet är **okänd**.  
  
  Okänd utvärdering i binär jämförelseoperatorer:  
  
- Om antingen vänster eller höger sida av operander utvärderas som **okänd**, och sedan resultatet är **okänd**.  
  
  Okänd utvärdering i `[NOT] LIKE`:  
  
- Om operanden alla utvärderas som **okänd**, och sedan resultatet är **okänd**.  
  
  Okänd utvärdering i `[NOT] IN`:  
  
- Om den vänstra operanden utvärderas som **okänd**, och sedan resultatet är **okänd**.  
  
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
  
-   Jämförelseoperatorer som `>`, `>=`, `<`, `<=`, `!=`, och `=` följer samma semantik som C#-operatör som bindningen i data typ kampanjer och implicita konverteringar.  
  
-   Aritmetiska operatorer som `+`, `-`, `*`, `/`, och `%` följer samma semantik som C#-operatör som bindningen i data typ kampanjer och implicita konverteringar.

## <a name="next-steps"></a>Nästa steg

- [SQLFilter klass (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter klass (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SQLRuleAction-klass](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
