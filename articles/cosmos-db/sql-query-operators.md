---
title: SQL-frågeoperatorer för Azure Cosmos DB
description: Lär dig mer om SQL-operatörer som likhet, jämförelse och logiska operatörer som stöds av Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063563"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatörer i Azure Cosmos DB

I den här artikeln beskrivs de olika operatörer som stöds av Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Aktörer för jämlikhet och jämförelse

I följande tabell visas resultatet av likhetsjämförelser i SQL API mellan två godtyckliga JSON-typer.

| **Op** | **Odefinierad** | **Null** | **Boolean** | **Nummer** | **Sträng** | **Objekt** | **Matris** |
|---|---|---|---|---|---|---|---|
| **Odefinierad** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Null** | Undefined (Odefinierad) | **Okej** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Boolean** | Undefined (Odefinierad) | Undefined (Odefinierad) | **Okej** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Nummer** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Okej** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Sträng** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Okej** | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Objekt** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Okej** | Undefined (Odefinierad) |
| **Matris** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Okej** |

För jämförelseoperatorer `>`som `>=` `!=`, `<`, `<=`, och , jämförelse mellan olika `Undefined`typer eller mellan två objekt eller matriser producerar .  

Om resultatet av skaläruttrycket är `Undefined`, inkluderas inte objektet `Undefined` i resultatet, eftersom det inte är lika med `true`.

## <a name="logical-and-or-and-not-operators"></a>Logiska operatorer (AND, OR och NOT) (och, eller samt inte)

Logiska operatorer arbetar med booleska värden. I följande tabeller visas de logiska sanningstabellerna för dessa operatorer:

**Operatorn OR** (ELLER)

Returnerar `true` när något `true`av villkoren är .

|  | **True** | **False** | **Odefinierad** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |Undefined (Odefinierad) |
| **Odefinierad** |True |Undefined (Odefinierad) |Undefined (Odefinierad) |

**Operatorn AND** (OCH)

Returnerar `true` när båda `true`uttrycken är .

|  | **True** | **False** | **Odefinierad** |
| --- | --- | --- | --- |
| **True** |True |False |Undefined (Odefinierad) |
| **False** |False |False |False |
| **Odefinierad** |Undefined (Odefinierad) |False |Undefined (Odefinierad) |

**Operatorn NOT** (INTE)

Återför värdet för alla booleska uttryck.

|  | **Inte** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Odefinierad** |Undefined (Odefinierad) |

**Operatörsprioritet**

De logiska `OR` `AND`operatorerna `NOT` och har prioritetsnivån nedan:

| **Operator** | **Prioritet** |
| --- | --- |
| **Inte** |1 |
| **Och** |2 |
| **Eller** |3 |

## <a name="-operator"></a>* operatör

Den särskilda operatorn * projicerar hela artikeln som den är. När den används måste den vara det enda projicerade fältet. En fråga `SELECT * FROM Families f` som är `SELECT VALUE * FROM Families f` `SELECT *, f.id FROM Families f` giltig, men och är inte giltig.

## <a name="-and--operators"></a>? Och?? operatorer

Du kan använda operatorerna Ternary (?) och Coalesce (??) för att skapa villkorsuttryck, som i programmeringsspråk som C# och JavaScript.

Du kan använda den ? för att bygga nya JSON-egenskaper i farten. Följande fråga klassificerar till exempel resultatnivåer till `elementary` eller: `other`

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Du kan också kapsla anrop till ? som i följande fråga: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Som med andra frågeoperatorer, ? inte objekt om de refererade egenskaperna saknas eller vilka typer som jämförs är olika.

Använd ?? för att effektivt söka efter en egenskap i ett objekt när du frågar mot halvstrukturerade eller blandade data. Följande fråga returneras `lastName` till exempel `surname` om `lastName` det finns eller om den inte finns.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Nyckelord](sql-query-keywords.md)
- [SELECT-sats](sql-query-select.md)
