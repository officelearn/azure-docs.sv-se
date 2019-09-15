---
title: SQL-fråga operatörer för Azure Cosmos DB
description: Läs om SQL-operatörer för Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 899355ad7331a3df8cd5d647a573dc15e3a0bb14
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003386"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatorer i Azure Cosmos DB

Den här artikeln beskriver de olika operatörer som stöds av Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Likhets-och jämförelse operatorer

I följande tabell visas resultatet av likhetsjämförelser i SQL API mellan två godtyckliga JSON-typer.

| **Op** | **Odefinierad** | **Null** | **Boolesk** | **Nummer** | **Sträng** | **Objekt** | **Matris** |
|---|---|---|---|---|---|---|---|
| **Odefinierad** | Odefinierat | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Odefinierat |
| **Null** | Odefinierat | **Ok** | Odefinierat | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Odefinierat |
| **Boolesk** | Odefinierat | Odefinierat | **Ok** | Odefinierat | Undefined (Odefinierad) | Undefined (Odefinierad) | Odefinierat |
| **Nummer** | Odefinierat | Undefined (Odefinierad) | Odefinierat | **Ok** | Odefinierat | Undefined (Odefinierad) | Odefinierat |
| **Sträng** | Odefinierat | Undefined (Odefinierad) | Undefined (Odefinierad) | Odefinierat | **Ok** | Odefinierat | Odefinierat |
| **Objekt** | Odefinierat | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Odefinierat | **Ok** | Odefinierat |
| **Matris** | Odefinierat | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Odefinierat | **Ok** |

Jämförelse `>`operatorer som `>=` `Undefined`, `!=`,, och`<=`, jämförelse mellan typer eller mellan två objekt eller matriser skapar. `<`  

Om resultatet av det skalära uttrycket är `Undefined`är objektet inte inkluderat i resultatet, eftersom `Undefined` det inte är `true`lika med.

## <a name="logical-and-or-and-not-operators"></a>Logiska operatorer (AND, OR och NOT) (och, eller samt inte)

Logiska operatorer arbetar med booleska värden. I följande tabeller visas de logiska sanningen-tabellerna för dessa operatörer:

**Operatorn OR** (ELLER)

| ELLER | Sant | False | Odefinierat |
| --- | --- | --- | --- |
| Sant |Sant |Sant |Sant |
| False |Sant |False |Odefinierat |
| Odefinierat |Sant |Odefinierat |Undefined (Odefinierad) |

**Operatorn AND** (OCH)

| AND | Sant | False | Odefinierat |
| --- | --- | --- | --- |
| Sant |Sant |False |Odefinierat |
| False |False |False |False |
| Odefinierat |Odefinierat |False |Odefinierat |

**Operatorn NOT** (INTE)

| NOT |  |
| --- | --- |
| Sant |False |
| False |Sant |
| Odefinierat |Odefinierat |


## <a name="-operator"></a>* Operator

Den speciella operatorn * projekterar hela objektet som det är. När den används måste den vara det enda projicerade fältet. En fråga som `SELECT * FROM Families f` är giltig, `SELECT *, f.id FROM Families f` men `SELECT VALUE * FROM Families f` som inte är giltig.

## <a name="-and--operators"></a>? och?? Operatörer

Du kan använda operatorerna ternär (?) och sammanslagning (??) för att bygga villkors uttryck som i programmeringsspråk som C# och Java Script. 

Du kan använda? operatör för att skapa nya JSON-egenskaper i farten. Följande fråga klassificerar till exempel betygs nivåer i `elementary` eller: `other`

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Kan du även kapsla anrop till? Operator, som i följande fråga: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Precis som med andra fråga-operatörer är? operatorn utesluter objekt om de refererade egenskaperna saknas eller om typerna som jämförs är olika.

Använd?? operatör för att effektivt söka efter en egenskap i ett objekt vid frågor mot halv strukturer eller blandade data. Till exempel returnerar `lastName` följande fråga om den finns, eller `surname` om `lastName` den inte finns.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [nyckelord](sql-query-keywords.md)
- [SELECT-satsen](sql-query-select.md)
