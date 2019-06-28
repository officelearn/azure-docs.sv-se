---
title: SQL-frågeoperatorer för Azure Cosmos DB
description: Läs mer om SQL-operatörer för Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: eecc1522f8c260286c7dd7fc4c2e58d5d8caa8fb
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342869"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatörer i Azure Cosmos DB

Den här artikeln beskriver de olika operatorer som stöds av Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Likhet och jämförelseoperatorer

I följande tabell visas resultatet av likhetsjämförelser i SQL API mellan två godtyckliga JSON-typer.

| **Op** | **Odefinierad** | **Null** | **Boolesk** | **Nummer** | **Sträng** | **Objekt** | **Matris** |
|---|---|---|---|---|---|---|---|
| **Odefinierad** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Null** | Undefined (Odefinierad) | **Ok** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Boolesk** | Undefined (Odefinierad) | Undefined (Odefinierad) | **Ok** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Nummer** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Ok** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Sträng** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Ok** | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Objekt** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Ok** | Undefined (Odefinierad) |
| **Matris** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Ok** |

För jämförelseoperatorer som `>`, `>=`, `!=`, `<`, och `<=`, jämförelse över typer eller mellan två objekt eller matriser ger `Undefined`.  

Om resultatet av det skalära uttrycket är `Undefined`, objektet inte ingår i resultatet, eftersom `Undefined` är inte lika med `true`.

## <a name="logical-and-or-and-not-operators"></a>Logiska operatorer (AND, OR och NOT) (och, eller samt inte)

Logiska operatorer arbetar med booleska värden. Följande tabeller visar de logiska tabellerna sanningen för de här operatorerna:

**Operatorn OR** (ELLER)

| ELLER | True | False | Undefined (Odefinierad) |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined (Odefinierad) |
| Undefined (Odefinierad) |True |Undefined (Odefinierad) |Undefined (Odefinierad) |

**Operatorn AND** (OCH)

| AND | True | False | Undefined (Odefinierad) |
| --- | --- | --- | --- |
| True |True |False |Undefined (Odefinierad) |
| False |False |False |False |
| Undefined (Odefinierad) |Undefined (Odefinierad) |False |Undefined (Odefinierad) |

**Operatorn NOT** (INTE)

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined (Odefinierad) |Undefined (Odefinierad) |


## <a name="-operator"></a>* operator

Operatorn särskilda * projekt hela objektet skick. När den används måste den vara det enda projicerade fältet. En fråga som `SELECT * FROM Families f` är giltig, men `SELECT VALUE * FROM Families f` och `SELECT *, f.id FROM Families f` är inte giltiga.

## <a name="-and--operators"></a>? och rubrikrad? Operatörer

Du kan använda Ternär (?) och slå samman operatorer (?) för att skapa villkorsuttryck som programmeringsspråk som C# och JavaScript. 

Du kan använda den? operator för att skapa nya JSON-egenskaper i farten. Till exempel följande fråga klassificerar i företagsklass nivåer i `elementary` eller `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Du kan även kapsla anrop till den? operatorn, som i följande fråga: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Precis som med andra frågeoperatorer den? operatorn utesluter objekt om de refererade egenskaperna saknas eller de typer som jämförs är olika.

Använd den rubrikrad? operator för att effektivt söka efter en egenskap i ett objekt vid frågor mot halvstrukturerade eller olika typer av data. Till exempel följande fråga returnerar `lastName` om TPM finns, eller `surname` om `lastName` saknas.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [nyckelord](sql-query-keywords.md)
- [SELECT-satsen](sql-query-select.md)
