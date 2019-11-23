---
title: FROM-sats i Azure Cosmos DB
description: Läs om SQL FROM-satsen för Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: tisande
ms.openlocfilehash: 79bb17277a041f71c095ed724737012f9501f16f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326999"
---
# <a name="from-clause-in-azure-cosmos-db"></a>FROM-sats i Azure Cosmos DB

From (`FROM <from_specification>`)-satsen är valfri, om inte källan filtreras eller projiceras senare i frågan. En fråga som `SELECT * FROM Families` räknar upp över hela `Families` containern. Du kan också använda den särskilda identifierande roten för behållaren i stället för att använda behållar namnet.

FROM-satsen framtvingar följande regler per fråga:

* Containern kan vara ett alias, till exempel `SELECT f.id FROM Families AS f` eller bara `SELECT f.id FROM Families f`. Här `f` är aliaset för `Families`. SOM är ett valfritt nyckelord för att [aliasa](sql-query-aliasing.md) identifieraren.  

* Det går inte att binda det ursprungliga käll namnet när det har alias. Till exempel är `SELECT Families.id FROM Families f` syntaktiskt ogiltigt eftersom identifieraren `Families` har fått ett alias och inte längre kan lösas.  

* Alla refererade egenskaper måste vara fullständigt kvalificerade för att undvika tvetydiga bindningar i frånvaro av strikt schema. Till exempel är `SELECT id FROM Families f` syntaktiskt ogiltigt eftersom egenskapen `id` inte är kopplad.

## <a name="syntax"></a>Syntax
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>Argument
  
- `<from_source>`  
  
  Anger en datakälla, med eller utan ett alias. Om alias inte har angetts, kommer den härledas från den `<container_expression>` med hjälp av följande regler:  
  
  -  Om uttrycket är en container_name, kommer container_name att användas som ett alias.  
  
  -  Om uttrycket är `<container_expression>`, %{Property_Name/ och %{Property_Name/ används som ett alias. Om uttrycket är en container_name, kommer container_name att användas som ett alias.  
  
- PRECIS SOM `input_alias`  
  
  Anger att den `input_alias` är en uppsättning värden som returneras av det underliggande container-uttrycket.  
 
- `input_alias` INDIEN  
  
  Anger att den `input_alias` bör representerar uppsättningen med värden som hämtas av iterera över alla matriselement av varje matrisen som returneras av det underliggande container-uttrycket. Ett värde som returneras av underliggande container-uttryck som inte är en matris ignoreras.  
  
- `<container_expression>`  
  
  Anger container-uttrycket som används för att hämta dokument.  
  
- `ROOT`  
  
  Anger det dokumentet ska hämtas från standardbehållaren, just nu anslutna.  
  
- `container_name`  
  
  Anger det dokumentet ska hämtas från den angivna behållaren. Namnet på behållaren måste matcha namnet på den behållare som är anslutna till.  
  
- `input_alias`  
  
  Anger det dokumentet ska hämtas från den källa som definieras av det angivna aliaset.  
  
- `<container_expression> '.' property_`  
  
  Anger det dokumentet ska hämtas genom att öppna den `property_name` egenskap eller array_index matriselement för alla dokument som hämtas av angivna behållaren uttryck.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Anger det dokumentet ska hämtas genom att öppna den `property_name` egenskap eller array_index matriselement för alla dokument som hämtas av angivna behållaren uttryck.  
  
## <a name="remarks"></a>Kommentarer
  
Alla alias har angetts eller härledd i den `<from_source>(`s) måste vara unikt. Syntaxen `<container_expression>.`%{Property_Name/ är samma som `<container_expression>' ['"property_name"']'`. Denna syntax kan dock användas om ett egenskapsnamn innehåller ett icke-ID-tecken.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>hantera saknade egenskaper, matriselement och odefinierad värden som saknas
  
Om ett uttryck för behållaren har åtkomst till egenskaper eller matriselement och att värdet inte finns, ignoreras värdet och inte fortsätta bearbetningen.  
  
### <a name="container-expression-context-scoping"></a>Behållaren uttryck kontext omfång  
  
Ett uttryck för behållare kan vara behållare omfattar eller dokumentet omfattar:  
  
-   Ett uttryck är container-omfattande, om den underliggande källan för container-uttryck är antingen ROTEN eller `container_name`. Sådana ett uttryck representerar en uppsättning dokument som hämtas från behållaren direkt och är inte beroende av bearbetning av andra behållare-uttryck.  
  
-   Ett uttryck är dokumentet-omfattande, om den underliggande källan för container-uttryck är `input_alias` introducerade tidigare i frågan. Sådana ett uttryck representerar en uppsättning dokument som hämtas av utvärderingen av behållaren uttryck i omfånget för varje dokument som hör till den uppsättning som är associerade med ett alias-behållaren.  Den resulterande uppsättningen blir en union av uppsättningar som erhålls av utvärderingen av container-uttryck för varje dokument i den underliggande uppsättningen. 

## <a name="examples"></a>Exempel

### <a name="get-subitems-by-using-the-from-clause"></a>Hämta under objekt med hjälp av from-satsen

FROM-satsen kan minska källan till en mindre delmängd. Om du bara vill räkna upp ett under träd i varje objekt kan underroten bli källan, vilket visas i följande exempel:

```sql
    SELECT *
    FROM Families.children
```

Resultaten är:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Föregående fråga använde en matris som källa, men du kan också använda ett objekt som källa. Frågan tar alla giltiga, definierade JSON-värden i källan som ska ingå i resultatet. Följande exempel skulle utesluta `Families` som inte har något `address.state`-värde.

```sql
    SELECT *
    FROM Families.address.state
```

Resultaten är:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [SELECT-satsen](sql-query-select.md)
- [WHERE-satsen](sql-query-where.md)