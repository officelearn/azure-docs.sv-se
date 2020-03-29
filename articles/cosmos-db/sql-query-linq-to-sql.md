---
title: LINQ till SQL-översättning i Azure Cosmos DB
description: Lär dig vilka LINQ-operatorer som stöds och hur LINQ-frågorna mappas till SQL-frågor i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441258"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL-översättning

Azure Cosmos DB-frågeprovidern utför en bäst operatörsmappning från en LINQ-fråga till en Cosmos DB SQL-fråga. Följande beskrivning förutsätter en grundläggande förtrogenhet med LINQ.

Frågeprovidertypsystemet stöder endast JSON-primitiva typer: numeriska, booleska, sträng och null.

Frågeprovidern stöder följande skaluttryck:

- Konstanta värden, inklusive konstanta värden för de primitiva datatyperna vid frågeutvärderingstid.
  
- Egenskaps-/matrisindexuttryck som refererar till egenskapen för ett objekt eller ett matriselement. Ett exempel:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetiska uttryck, inklusive vanliga aritmetiska uttryck på numeriska och booleska värden. Den fullständiga listan finns i [Azure Cosmos DB SQL-specifikationen](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Strängjämnningsuttryck, som inkluderar att jämföra ett strängvärde med ett konstant strängvärde.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Objekt-/arrayskapande uttryck, som returnerar ett objekt av sammansatt värdetyp eller anonym typ, eller en matris med sådana objekt. Du kan kapsla dessa värden.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>LINQ-operatörer som stöds

LINQ-providern som ingår i SQL .NET SDK stöder följande operatorer:

- **Välj**: Projektioner översätter till SQL SELECT, inklusive objektkonstruktion.
- **Var:** Filter översätter till SQL `&&`WHERE `||`och `!` stöder översättning mellan , och till SQL-operatorerna
- **SelectMany**: Tillåter uppspolning av matriser till SQL JOIN-satsen. Används för att kedja eller kapsla uttryck för att filtrera på matriselement.
- **OrderBy** och **OrderByDescending**: Översätt till ORDER BY med ASC eller DESC.
- Operatorerna **Count**, **Sum**, **Min**, **Max** och **Average** för sammansättning och deras async-motsvarigheter **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** och **AverageAsync**.
- **CompareTo**: Översätts till intervalljämförelser. Används ofta för strängar, eftersom de inte är jämförbara i .NET.
- **Hoppa över** och **ta:** Översätter till SQL OFFSET och LIMIT för att begränsa resultat från en fråga och göra sidnumrering.
- **Matematiska funktioner**: Stöder `Abs` `Acos`översättning `Asin` `Atan`från `Ceiling` `Cos`.NET , `Pow`, `Round` `Sign`, `Sin` `Sqrt`, `Tan` `Exp`, `Floor`, , `Log`, `Log10`, , , , , , , , , och `Truncate` till motsvarande SQL-inbyggda funktioner.
- **Strängfunktioner**: Stöder översättning `Concat` `Contains`från `Count` `EndsWith`.NET `Replace` `Reverse`, `StartsWith` `SubString`, `ToLower` `ToUpper`, `TrimEnd``IndexOf`, `TrimStart` , , , , , , , , och till motsvarande SQL-inbyggda funktioner.
- **Matrisfunktioner:** Stöder översättning `Concat`från `Contains`.NET , och `Count` till motsvarande SQL-inbyggda funktioner.
- **Geospatial Extension-funktioner**: Stöder `Distance` `IsValid`översättning `IsValidDetailed`från `Within` stub-metoder , , och till motsvarande SQL-inbyggda funktioner.
- **Funktionstillägg för användare**: Stöder översättning `UserDefinedFunctionProvider.Invoke` från stub-metoden till motsvarande användardefinierade funktion.
- **Övrigt**: Stöder översättning `Coalesce` av och villkorade operatorer. Kan `Contains` översätta till sträng-inneslutningar, ARRAY_CONTAINS eller SQL IN, beroende på kontext.

## <a name="examples"></a>Exempel

Följande exempel illustrerar hur några av de vanliga LINQ-frågeoperatorerna översätter till Cosmos DB-frågor.

### <a name="select-operator"></a>Välj operator

Syntaxen är `input.Select(x => f(x))`, där `f` är ett skalärt uttryck.

**Välj operator, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Välj operator, exempel 2:** 

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Välj operator, exempel 3:**

- **LINQ-lambdauttryck**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>Operatorn SelectMany

Syntaxen är `input.SelectMany(x => f(x))`, där `f` är ett skalärt uttryck som returnerar en containertyp.

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Operatorn Where

Syntaxen är `input.Where(x => f(x))`, där `f` är ett skalärt uttryck som returnerar ett booleskt värde.

**Om operatör, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Om operatör, exempel 2:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Sammansatta SQL-frågor

Du kan komponera de föregående operatorerna för att skapa mer kraftfulla frågor. Eftersom Cosmos DB stöder kapslade behållare kan du sammanfoga eller kapsla kompositionen.

### <a name="concatenation"></a>Sammanfogning

Syntaxen är `input(.|.SelectMany())(.Select()|.Where())*`. En sammanfogad fråga kan börja `SelectMany` med en valfri `Select` `Where` fråga, följt av flera eller operatorer.

**Sammanfogning, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Sammanfogning, exempel 2:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Sammanfogning, exempel 3:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Sammanfogning, exempel 4:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Kapsling

Syntaxen `input.SelectMany(x=>x.Q())` är `Q` där `Select` `SelectMany`är `Where` en , , eller operator.

En kapslad fråga tillämpar den inre frågan på varje element i den yttre behållaren. En viktig funktion är att den inre frågan kan referera till fälten för elementen i den yttre behållaren, till exempel en självkoppling.

**Kapsla, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Kapsla, exempel 2:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Kapsla, exempel 3:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdata](modeling-data.md)
