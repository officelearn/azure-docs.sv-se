---
title: Referens för Azure Service Bus SQLFilter-syntax | Microsoft Docs
description: Den här artikeln innehåller information om SQLFilter-grammatik. En SqlFilter stöder en delmängd av SQL-92-standarden.
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
ms.openlocfilehash: d5a8e165fcee23c5feecd5935983dd77d3ec6c30
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759671"
---
# <a name="sqlfilter-syntax"></a>SQLFilter-syntax

Ett *SqlFilter* -objekt är en instans av [klassen SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)och representerar ett SQL language-baserat filter uttryck som utvärderas mot en [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). En SqlFilter stöder en delmängd av SQL-92-standarden.  
  
 Det här avsnittet innehåller information om SqlFilter-grammatik.  
  
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
  
-   `<scope>` är en valfri sträng som anger omfånget för `<property_name>`. Giltiga värden är `sys` eller `user`. Värdet `sys` anger system omfång där `<property_name>` är ett offentligt egenskaps namn för [klassen BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` anger användar omfång där `<property_name>` är en nyckel för [BrokeredMessage-klass](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) ord listan. `user` omfång är standard omfånget om `<scope>` inte har angetts.  
  
## <a name="remarks"></a>Anmärkningar

Ett försök att komma åt en icke-befintlig system egenskap är ett fel, medan ett försök att få åtkomst till en icke-befintlig användar egenskap inte är ett fel. I stället utvärderas en icke-befintlig användar egenskap internt som ett okänt värde. Ett okänt värde behandlas särskilt under utvärderingen av operatorn.  
  
## <a name="property_name"></a>property_name  
  
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
  
Den här grammatiken avser en sträng som börjar med en bokstav och som följs av en eller flera under streck/bokstav/siffra.  
  
`[:IsLetter:]` syftar på Unicode-tecken som kategoriseras som en Unicode-bokstav. `System.Char.IsLetter(c)` returnerar `true` om `c` är en Unicode-bokstav.  
  
`[:IsDigit:]` syftar på Unicode-tecken som kategoriseras som decimal tal. `System.Char.IsDigit(c)` returnerar `true` om `c` är en Unicode-siffra.  
  
Ett `<regular_identifier>` får inte vara ett reserverat nyckelord.  
  
`<delimited_identifier>` är en sträng som omges av vänster/höger hak paren tes ([]). En höger hak paren tes visas som två höger hak paren tes. Följande är exempel på `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` är en sträng som omges av dubbla citat tecken. Ett dubbelt citat tecken i identifierare representeras som två dubbla citat tecken. Du bör inte använda citerade identifierare eftersom det enkelt kan förväxlas med en strängkonstant. Använd en avgränsad identifierare om möjligt. Följande är ett exempel på `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>ofta  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Anmärkningar
  
`<pattern>` måste vara ett uttryck som utvärderas som en sträng. Den används som ett mönster för operatorn LIKE.      Den kan innehålla följande jokertecken:  
  
-   `%`: valfri sträng med noll eller flera tecken.  
  
-   `_`: ett enskilt Character.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Anmärkningar  

`<escape_char>` måste vara ett uttryck som utvärderas som en sträng med längden 1. Det används som ett escape-tecken för operatorn LIKE.  
  
 `property LIKE 'ABC\%' ESCAPE '\'` matchar till exempel `ABC%` i stället för en sträng som börjar med `ABC`.  
  
## <a name="constant"></a>konstant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argument  
  
-   `<integer_constant>` är en sträng med tal som inte omges av citat tecken och som inte innehåller några decimaler. Värdena lagras som `System.Int64` internt och följer samma intervall.  
  
     Dessa är exempel på långa konstanter:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` är en sträng med tal som inte omges av citat tecken och som innehåller ett decimal tecken. Värdena lagras som `System.Double` internt och följer samma omfång/precision.  
  
     I en framtida version kan det här numret lagras i en annan datatyp för att stödja exakta semantiska tal, så du bör inte förlita dig på det faktum att den underliggande data typen är `System.Double` för `<decimal_constant>`.  
  
     Följande är exempel på Decimal konstanter:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` är ett tal skrivet i matematisk notation. Värdena lagras som `System.Double` internt och följer samma omfång/precision. Följande är exempel på ungefärligt antal konstanter:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Anmärkningar  

Booleska konstanter representeras av nyckelorden **True** eller **false**. Värdena lagras som `System.Boolean`.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Anmärkningar  

Sträng konstanter omges av enkla citat tecken och innehåller alla giltiga Unicode-tecken. Ett enkelt citat tecken som är inbäddat i en strängkonstant representeras som två enkla citat tecken.  
  
## <a name="function"></a>funktioner  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Anmärkningar
  
Funktionen `newid()` returnerar ett **system. GUID** som genereras av `System.Guid.NewGuid()`-metoden.  
  
Funktionen `property(name)` returnerar värdet för den egenskap som `name`refererar till. `name`-värdet kan vara ett giltigt uttryck som returnerar ett sträng värde.  
  
## <a name="considerations"></a>Överväganden
  
Tänk på följande [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) -semantik:  
  
-   Egenskaps namn är inte Skift läges känsliga.  
  
-   Operatorer C# följer implicit konvertering när det är möjligt.  
  
-   System egenskaper är offentliga egenskaper som visas i [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) -instanser.  
  
    Tänk på följande `IS [NOT] NULL` semantik:  
  
    -   `property IS NULL` utvärderas som `true` om antingen egenskapen inte finns eller om egenskapens värde är `null`.  
  
### <a name="property-evaluation-semantics"></a>Semantik för egenskaps utvärdering  
  
- Ett försök att utvärdera en icke-befintlig system egenskap genererar ett [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) -undantag.  
  
- En egenskap som inte finns utvärderas internt som **okänd**.  
  
  Okänd utvärdering i aritmetiska operatorer:  
  
- För binära operatorer, om antingen den vänstra och/eller högra sidan av operander utvärderas som **okänd**, är resultatet **Okänt**.  
  
- För unära operatorer är resultatet **Okänt**om en operand utvärderas som **okänd**.  
  
  Okänd utvärdering i binära jämförelse operatorer:  
  
- Om antingen den vänstra och/eller högra sidan av operander utvärderas som **okänd**är resultatet **Okänt**.  
  
  Okänd utvärdering i `[NOT] LIKE`:  
  
- Om en operand utvärderas som **okänd**är resultatet **Okänt**.  
  
  Okänd utvärdering i `[NOT] IN`:  
  
- Om den vänstra operanden utvärderas som **okänd**är resultatet **Okänt**.  
  
  Okänd utvärdering i **och** -operator:  
  
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
  
 Okänd utvärdering i **eller** -operator:  
  
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
  
### <a name="operator-binding-semantics"></a>Operator bindnings-semantik
  
-   Jämförelse operatorer som `>`, `>=`, `<`, `<=`, `!=`och `=` följer samma semantik som C# operator bindningen i data typens kampanjer och implicita konverteringar.  
  
-   Aritmetiska operatorer som `+`, `-`, `*`, `/`och `%` följer samma semantik som C# operator bindningen i data typens kampanjer och implicita konverteringar.

## <a name="next-steps"></a>Nästa steg

- [SQLFilter-klass (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter-klass (.NET standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SQLRuleAction-klass](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
