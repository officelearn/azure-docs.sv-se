---
title: Syntaxreferens för SQLRuleAction i Azure Service Bus
description: Den här artikeln innehåller en referens för SQLRuleAction-syntax. Åtgärderna skrivs i SQL-språkbaserad syntax som utförs mot ett förmedlat meddelande.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 37615e39577ef60cccc9df91b61a6aa24ca794d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759636"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>Syntaxreferens för SQLRuleAction för Azure Service Bus

En *SqlRuleAction* är en instans av klassen [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) och representerar en uppsättning åtgärder som är skrivna i SQL-språkbaserad syntax som utförs mot en [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
I den här artikeln visas information om SQL-regelåtgärds grammatiken.  
  
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
  
-   `<scope>`är en valfri sträng som `<property_name>`anger omfattningen av . Giltiga värden `sys` `user`är eller . Värdet `sys` anger systemomfång där `<property_name>` är ett offentligt egenskapsnamn för klassen [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user`anger användaromfattning där `<property_name>` är en nyckel till ordlistan för [BrokeredMessage-klassen.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) `user`omfånget är `<scope>` standardomfånget om det inte anges.  
  
### <a name="remarks"></a>Anmärkningar  

Ett försök att komma åt en systemegenskap som inte finns är ett fel, medan ett försök att komma åt en icke-existerande användaregenskap är inte ett fel. I stället utvärderas en icke-existerande användaregenskap internt som ett okänt värde. Ett okänt värde behandlas speciellt under operatörsutvärderingen.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argument  
 `<regular_identifier>`är en sträng som representeras av följande reguljära uttryck:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Det innebär en sträng som börjar med en bokstav och följs av ett eller flera understreck/bokstav/siffra.  
  
 `[:IsLetter:]`betyder alla Unicode-tecken som kategoriseras som en Unicode-bokstav. `System.Char.IsLetter(c)``true` returnerar `c` om är en Unicode-bokstav.  
  
 `[:IsDigit:]`betyder alla Unicode-tecken som kategoriseras som en decimal. `System.Char.IsDigit(c)``true` returnerar `c` om är en Unicode-siffra.  
  
 A `<regular_identifier>` kan inte vara ett reserverat nyckelord.  
  
 `<delimited_identifier>`är en sträng som medföljer vänster/höger hakparenteser ([]). En höger hakparentes representeras som två högra hakparenteser. Följande är exempel `<delimited_identifier>`på:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`är en sträng som omges av dubbla citattecken. Ett dubbelt citattecken i identifieraren representeras som två dubbla citattecken. Det rekommenderas inte att använda citerade identifierare eftersom det lätt kan förväxlas med en strängkonstant. Använd om möjligt en avgränsad identifierare. Följande är ett `<quoted_identifier>`exempel på:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Mönster  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Anmärkningar
  
 `<pattern>`måste vara ett uttryck som utvärderas som en sträng. Det används som ett mönster för operatorn LIKE.      Den kan innehålla följande jokertecken:  
  
-   `%`: Valfri sträng med noll eller fler tecken.  
  
-   `_`: Ett enda tecken.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Anmärkningar
  
 `<escape_char>`måste vara ett uttryck som utvärderas som en sträng med längd 1. Det används som ett escape-tecken för operatorn LIKE.  
  
 Matchar till `property LIKE 'ABC\%' ESCAPE '\'` `ABC%` exempel i stället för `ABC`en sträng som börjar med .  
  
## <a name="constant"></a>Konstant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argument  
  
-   `<integer_constant>`är en sträng med tal som inte omges av citattecken och som inte innehåller decimaler. Värdena lagras `System.Int64` internt och följer samma intervall.  
  
     Följande är exempel på långa konstanter:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`är en sträng med tal som inte omges av citattecken och som innehåller ett decimaltecken. Värdena lagras `System.Double` internt och följer samma intervall/precision.  
  
     I en framtida version kan det här numret lagras i en annan datatyp för att stödja exakt talsemantik, så du bör inte förlita dig på det faktum att den underliggande datatypen är `System.Double` för `<decimal_constant>`.  
  
     Följande är exempel på decimalkonstanter:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`är ett nummer skrivet i vetenskaplig notation. Värdena lagras `System.Double` internt och följer samma intervall/precision. Följande är exempel på ungefärliga talkonstanter:  
  
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
  
Booleska konstanter representeras av `TRUE` `FALSE`nyckelorden eller . Värdena lagras `System.Boolean`som .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Anmärkningar
  
Strängkonstanter omges av enkla citattecken och innehåller giltiga Unicode-tecken. Ett enda citattecken som är inbäddat i en strängkonstant representeras som två enkla citattecken.  
  
## <a name="function"></a>Funktion  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Anmärkningar  

Funktionen `newid()` returnerar ett **System.Guid som** genereras av `System.Guid.NewGuid()` metoden.  
  
Funktionen `property(name)` returnerar värdet för egenskapen `name`som refereras av . Värdet `name` kan vara ett giltigt uttryck som returnerar ett strängvärde.  
  
## <a name="considerations"></a>Överväganden

- SET används för att skapa en ny egenskap eller uppdatera värdet för en befintlig egenskap.
- REMOVE används för att ta bort en egenskap.
- SET utför implicit konvertering om möjligt när uttryckstypen och den befintliga egenskapstypen är olika.
- Åtgärden misslyckas om systemegenskaper som inte finns refererade.
- Åtgärden misslyckas inte om icke-existerande användaregenskaper refererades.
- En icke-existerande användaregenskap utvärderas som "Okänd" internt, efter samma semantik som [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) vid utvärdering av operatorer.

## <a name="next-steps"></a>Nästa steg

- [Klassen SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Klassen SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
