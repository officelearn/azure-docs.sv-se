---
title: FRÅN-satsen i Azure Cosmos DB
description: Lär dig mer om SQL-syntaxen och till exempel för FROM-satsen för Azure Cosmos DB. Den här artikeln visar också exempel på scoperesultat och hämta underobjekt med hjälp av FROM-satsen.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 3939594064b63c567720378b9d316acca64d3266
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587693"
---
# <a name="from-clause-in-azure-cosmos-db"></a>FRÅN-satsen i Azure Cosmos DB

FROM ()`FROM <from_specification>`är valfri, såvida inte källan filtreras eller projiceras senare i frågan. En fråga `SELECT * FROM Families` som räknas upp `Families` över hela behållaren. Du kan också använda den särskilda identifieraren ROOT för behållaren i stället för att använda behållarnamnet.

FROM-satsen tillämpar följande regler per fråga:

* Containern kan vara ett alias, till exempel `SELECT f.id FROM Families AS f` eller bara `SELECT f.id FROM Families f`. Här `f` är alias `Families`för . AS är ett valfritt nyckelord för att [alias](sql-query-aliasing.md) identifieraren.  

* När det ursprungliga källnamnet har aliasats kan det inte bindas. Till exempel `SELECT Families.id FROM Families f` är syntaktiskt ogiltig eftersom `Families` identifieraren har aliasats och inte kan lösas längre.  

* Alla refererade egenskaper måste vara fullt kvalificerade för att undvika tvetydiga bindningar i avsaknad av strikt schemaefterlevnad. Till exempel `SELECT id FROM Families f` är syntaktiskt ogiltig `id` eftersom egenskapen inte är bunden.

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
  
  Anger en datakälla, med eller utan ett alias. Om alias inte anges härleds det `<container_expression>` från följande regler:  
  
  -  Om uttrycket är en container_name används container_name som ett alias.  
  
  -  Om uttrycket `<container_expression>`är , property_name, kommer property_name att användas som ett alias. Om uttrycket är en container_name används container_name som ett alias.  
  
- Som`input_alias`  
  
  Anger att `input_alias` är en uppsättning värden som returneras av det underliggande behållaruttrycket.  
 
- `input_alias`I  
  
  Anger att `input_alias` värdet ska representera den uppsättning värden som erhålls genom att iterera över alla matriselement i varje matris som returneras av det underliggande behållaruttrycket. Alla värden som returneras av underliggande behållaruttryck som inte är en matris ignoreras.  
  
- `<container_expression>`  
  
  Anger det behållaruttryck som ska användas för att hämta dokumenten.  
  
- `ROOT`  
  
  Anger att dokumentet ska hämtas från standardbehållaren för närvarande ansluten.  
  
- `container_name`  
  
  Anger att dokumentet ska hämtas från den angivna behållaren. Namnet på behållaren måste matcha namnet på den behållare som för närvarande är ansluten till.  
  
- `input_alias`  
  
  Anger att dokumentet ska hämtas från den andra källan som definieras av det angivna aliaset.  
  
- `<container_expression> '.' property_name`  
  
  Anger att dokumentet ska hämtas genom `property_name` åtkomst till egenskapen.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  Anger att dokumentet ska hämtas genom `property_name` att använda egenskapen eller array_index matriselementet för alla dokument som hämtas av angivet behållaruttryck.  
  
## <a name="remarks"></a>Anmärkningar
  
Alla alias som tillhandahålls eller `<from_source>(`härledas i s) måste vara unika. Syntaxen `<container_expression>.`property_name är samma `<container_expression>' ['"property_name"']'`som . Den senare syntaxen kan dock användas om ett egenskapsnamn innehåller ett tecken som inte är identifierare.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>Hantera saknade egenskaper, saknade matriselement och odefinierade värden
  
Om ett behållaruttryck kommer åt egenskaper eller matriselement och det värdet inte finns ignoreras det värdet och bearbetas inte ytterligare.  
  
### <a name="container-expression-context-scoping"></a>Omfång för kontext för behållaruttryck  
  
Ett behållaruttryck kan vara behållarscoped eller dokumentomfattat:  
  
-   Ett uttryck är behållarbestä ingett, om den underliggande källan till behållaruttrycket antingen är ROOT eller `container_name`. Ett sådant uttryck representerar en uppsättning dokument som hämtas direkt från behållaren och är inte beroende av bearbetningen av andra behållaruttryck.  
  
-   Ett uttryck är dokumentomfattat om den underliggande `input_alias` källan till behållaruttrycket introduceras tidigare i frågan. Ett sådant uttryck representerar en uppsättning dokument som erhålls genom att utvärdera behållaruttrycket i omfattningen för varje dokument som tillhör den uppsättning som är associerad med behållaren med alias.  Den resulterande uppsättningen blir en union av uppsättningar som erhålls genom att utvärdera behållaruttrycket för vart och ett av dokumenten i den underliggande uppsättningen. 

## <a name="examples"></a>Exempel

### <a name="get-subitems-by-using-the-from-clause"></a>Hämta underobjekt med hjälp av FROM-satsen

FRÅN-satsen kan minska källan till en mindre delmängd. Om du bara vill räkna upp ett underträd i varje objekt kan underroten bli källan, vilket visas i följande exempel:

```sql
    SELECT *
    FROM Families.children
```

Resultatet är:

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

Föregående fråga använde en matris som källa, men du kan också använda ett objekt som källa. Frågan tar hänsyn till alla giltiga, definierade JSON-värden i källan för att inkluderas i resultatet. I följande exempel `Families` skulle utesluta att `address.state` inte ha ett värde.

```sql
    SELECT *
    FROM Families.address.state
```

Resultatet är:

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [SELECT-sats](sql-query-select.md)
- [WHERE-klausul](sql-query-where.md)
