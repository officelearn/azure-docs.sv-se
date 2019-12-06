---
title: SQL-fråga operatörer för Azure Cosmos DB
description: Lär dig om SQL-operatorer som likheter, jämförelse och logiska operatörer som stöds av Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870946"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatorer i Azure Cosmos DB

Den här artikeln beskriver de olika operatörer som stöds av Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Likhets-och jämförelse operatorer

I följande tabell visas resultatet av likhetsjämförelser i SQL API mellan två godtyckliga JSON-typer.

| **Op** | **Odefinierad** | **Null** | **Boolesk** | **Nummer** | **Sträng** | **Objekt** | **Matris** |
|---|---|---|---|---|---|---|---|
| **Odefinierad** | Odefinierat | Odefinierat | Odefinierat | Odefinierat | Odefinierat | Odefinierat | Odefinierat |
| **Null** | Odefinierat | **Ok** | Odefinierat | Odefinierat | Odefinierat | Odefinierat | Odefinierat |
| **Boolesk** | Odefinierat | Odefinierat | **Ok** | Odefinierat | Odefinierat | Odefinierat | Odefinierat |
| **Nummer** | Odefinierat | Odefinierat | Odefinierat | **Ok** | Odefinierat | Odefinierat | Odefinierat |
| **Sträng** | Odefinierat | Odefinierat | Odefinierat | Odefinierat | **Ok** | Odefinierat | Odefinierat |
| **Objekt** | Odefinierat | Odefinierat | Odefinierat | Odefinierat | Odefinierat | **Ok** | Odefinierat |
| **Matris** | Odefinierat | Odefinierat | Odefinierat | Odefinierat | Odefinierat | Odefinierat | **Ok** |

För jämförelse operatorer som `>`, `>=`, `!=`, `<`och `<=`, genererar jämförelse mellan typer eller mellan två objekt eller matriser `Undefined`.  

Om resultatet av det skalära uttrycket är `Undefined`inkluderas inte objektet i resultatet eftersom `Undefined` inte är lika med `true`.

## <a name="logical-and-or-and-not-operators"></a>Logiska operatorer (AND, OR och NOT) (och, eller samt inte)

Logiska operatorer arbetar med booleska värden. I följande tabeller visas de logiska sanningen-tabellerna för dessa operatörer:

**Operatorn OR** (ELLER)

| ELLER | Sant | Falskt | Odefinierat |
| --- | --- | --- | --- |
| Sant |Sant |Sant |Sant |
| Falskt |Sant |Falskt |Odefinierat |
| Odefinierat |Sant |Odefinierat |Odefinierat |

**Operatorn AND** (OCH)

| AND | Sant | Falskt | Odefinierat |
| --- | --- | --- | --- |
| Sant |Sant |Falskt |Odefinierat |
| Falskt |Falskt |Falskt |Falskt |
| Odefinierat |Odefinierat |Falskt |Odefinierat |

**Operatorn NOT** (INTE)

| NOT |  |
| --- | --- |
| Sant |Falskt |
| Falskt |Sant |
| Odefinierat |Odefinierat |


## <a name="-operator"></a>* Operator

Den speciella operatorn * projekterar hela objektet som det är. När den används måste den vara det enda projicerade fältet. En fråga som `SELECT * FROM Families f` är giltig, men `SELECT VALUE * FROM Families f` och `SELECT *, f.id FROM Families f` är inte giltiga.

## <a name="-and--operators"></a>? och?? operatorer

Du kan använda operatorerna ternär (?) och sammanslagning (??) för att bygga villkors uttryck som i programmeringsspråk som C# och Java Script. 

Du kan använda? operatör för att skapa nya JSON-egenskaper i farten. Följande fråga klassificerar till exempel betygs nivåer i `elementary` eller `other`:

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

Använd?? operatör för att effektivt söka efter en egenskap i ett objekt vid frågor mot halv strukturer eller blandade data. Till exempel returnerar följande fråga `lastName` om den finns, eller `surname` om `lastName` inte finns.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [nyckelord](sql-query-keywords.md)
- [SELECT-satsen](sql-query-select.md)
