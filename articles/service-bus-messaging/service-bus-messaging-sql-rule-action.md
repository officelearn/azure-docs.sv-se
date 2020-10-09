---
title: SQLRuleAction syntax-referens i Azure Service Bus
description: Den här artikeln innehåller en referens för SQLRuleAction-syntax. Åtgärderna skrivs i SQL-språkbaserad syntax som utförs mot ett Broker-meddelande.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 61fa6e046b4d4a0ba91bf8608c846755026d07ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341583"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>Referens för SQLRuleAction-syntax för Azure Service Bus

En *SqlRuleAction* är en instans av klassen [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) och representerar en uppsättning åtgärder som skrivits i SQL-språkbaserad syntax som utförs mot en [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Den här artikeln innehåller information om grammatik i SQL-regel.  
  
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
  
-   `<scope>` är en valfri sträng som anger omfånget för `<property_name>` . Giltiga värden är `sys` eller `user` . `sys`Värdet anger systemets omfattning där `<property_name>` är ett offentligt egenskaps namn för [klassen BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` anger användar omfång där `<property_name>` är en nyckel för [BrokeredMessage-klass](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) ord listan. `user` omfånget är standard omfånget om `<scope>` inget värde anges.  
  
### <a name="remarks"></a>Kommentarer  

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
  
 Detta innebär en sträng som börjar med en bokstav och som följs av en eller flera under streck/bokstav/siffra.  
  
 `[:IsLetter:]` innebär valfritt Unicode-tecken som kategoriseras som en Unicode-bokstav. `System.Char.IsLetter(c)` Returnerar `true` IF `c` är en Unicode-bokstav.  
  
 `[:IsDigit:]` innebär valfritt Unicode-tecken som kategoriseras som en decimal siffra. `System.Char.IsDigit(c)` Returnerar `true` IF `c` är en Unicode-siffra.  
  
 En `<regular_identifier>` får inte vara ett reserverat nyckelord.  
  
 `<delimited_identifier>` är en sträng som omges av vänster/höger hak paren tes ([]). En höger hak paren tes visas som två höger hak paren tes. Följande är exempel på `<delimited_identifier>` :  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>` är en sträng som omges av dubbla citat tecken. Ett dubbelt citat tecken i identifierare representeras som två dubbla citat tecken. Du bör inte använda citerade identifierare eftersom det enkelt kan förväxlas med en strängkonstant. Använd en avgränsad identifierare om möjligt. Följande är ett exempel på `<quoted_identifier>` :  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>ofta  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Kommentarer
  
 `<pattern>` måste vara ett uttryck som utvärderas som en sträng. Den används som ett mönster för operatorn LIKE.      Den kan innehålla följande jokertecken:  
  
-   `%`: Valfri sträng med noll eller flera tecken.  
  
-   `_`: Alla enskilda bokstäver.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Kommentarer
  
 `<escape_char>` måste vara ett uttryck som utvärderas som en sträng med längden 1. Det används som ett escape-tecken för operatorn LIKE.  
  
 Till exempel `property LIKE 'ABC\%' ESCAPE '\'` matchningar `ABC%` i stället för en sträng som börjar med `ABC` .  
  
## <a name="constant"></a>konstant  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argument  
  
-   `<integer_constant>` är en sträng med tal som inte omges av citat tecken och som inte innehåller några decimaler. Värdena lagras som `System.Int64` internt och följer samma intervall.  
  
     Följande är exempel på långa konstanter:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` är en sträng med tal som inte omges av citat tecken och som innehåller ett decimal tecken. Värdena lagras som `System.Double` internt och följer samma omfång/precision.  
  
     I en framtida version kan det här numret lagras i en annan datatyp för att stödja exakta semantiska tal, så du bör inte förlita dig på det faktum att den underliggande data typen är `System.Double` för `<decimal_constant>` .  
  
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
  
### <a name="remarks"></a>Kommentarer
  
Booleska konstanter representeras av nyckelorden `TRUE` eller `FALSE` . Värdena lagras som `System.Boolean` .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Kommentarer
  
Sträng konstanter omges av enkla citat tecken och innehåller alla giltiga Unicode-tecken. Ett enkelt citat tecken som är inbäddat i en strängkonstant representeras som två enkla citat tecken.  
  
## <a name="function"></a>funktioner  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Kommentarer  

`newid()`Funktionen returnerar ett **system. GUID** som genereras av `System.Guid.NewGuid()` metoden.  
  
`property(name)`Funktionen returnerar värdet för den egenskap som refereras till av `name` . `name`Värdet kan vara ett giltigt uttryck som returnerar ett sträng värde.  
  
## <a name="considerations"></a>Överväganden

- SET används för att skapa en ny egenskap eller uppdatera värdet för en befintlig egenskap.
- TA bort används för att ta bort en egenskap.
- SET utför implicit konvertering om det är möjligt när uttrycks typen och den befintliga egenskaps typen skiljer sig.
- Åtgärden Miss lyckas om det inte finns några refererade system egenskaper.
- Det går inte att utföra åtgärden om det inte finns referenser till användar egenskaper som inte finns.
- En icke-befintlig användar egenskap utvärderas som "okänd" internt och följer samma semantik som [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) vid utvärdering av operatorer.

## <a name="next-steps"></a>Nästa steg

- [SQLRuleAction-klass](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter-klass](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
