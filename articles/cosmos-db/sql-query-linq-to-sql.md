---
title: LINQ to SQL översättning i Azure Cosmos DB
description: Mappar LINQ-frågor till Azure Cosmos DB SQL-frågor.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: ce9d96a90a2463d1ab8e1a9774a019e38ca681f4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036036"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL-översättning

Azure Cosmos DB Query-providern utför en mappning från en LINQ-fråga till en Cosmos DB SQL-fråga. Följande beskrivning förutsätter en grundläggande kunskap om LINQ.

Typ systemet för fråged provider stöder endast JSON primitiva typer: numeric, Boolean, String och null.

Frågans provider stöder följande skalära uttryck:

- Konstanta värden, inklusive konstanta värden för primitiva data typer vid frågans utvärderings tid.
  
- Index uttryck för egenskap/mat ris som refererar till egenskapen för ett objekt eller ett mat ris element. Exempel:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetiska uttryck, inklusive vanliga aritmetiska uttryck för numeriska och booleska värden. En fullständig lista finns i [Azure Cosmos DB SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612)-specifikationen.
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Sträng jämförelse uttryck, som inkluderar jämförelse av ett sträng värde till ett konstant sträng värde.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Uttryck för att skapa objekt/matris, som returnerar ett objekt av sammansatt värde typ eller anonym typ, eller en matris med sådana objekt. Du kan kapsla dessa värden.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>LINQ-operatorer som stöds

LINQ-providern som ingår i SQL .NET SDK stöder följande operatorer:

- **Select**: Projektioner översätts till SQL SELECT, inklusive objekt konstruktion.
- **Where**: Filter översätts till SQL WHERE, och stöder `&&`översättning `||`mellan, `!` , och till SQL-operatörerna
- **SelectMany**: Tillåter uppspolning av matriser till SQL JOIN-satsen. Används för att kedja eller kapsla uttryck för att filtrera på mat ris element.
- **OrderBy** och **OrderByDescending**: Översätt till ORDER BY med ASC eller DESC.
- Operatorerna **Count**, **Sum**, **Min**, **Max** och **Average** för sammansättning och deras async-motsvarigheter **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** och **AverageAsync**.
- **CompareTo**: Översätts till intervalljämförelser. Används ofta för strängar, eftersom de inte är jämförbara i .NET.
- **Take**: Översätter till SQL TOP för att begränsa resultat från en fråga.
- **Matematik funktioner**: Stöder översättning från .NET `Abs`, `Acos` `Atan` ,`Ceiling` ,,`Cos`, ,,`Log`,, ,,`Log10` `Exp` `Floor` `Pow` `Asin` `Round`, `Sign`, ,,`Sin`och tillmotsvarandeinbyggda`Truncate` SQL-funktioner. `Sqrt` `Tan`
- **Sträng funktioner**: Stöder översättning från .NET `Concat`, `Contains` `EndsWith`,`IndexOf` ,,`Replace`, ,,`SubString`,, ,,`ToLower` `Reverse` `StartsWith` `ToUpper` `Count` `TrimEnd`, och `TrimStart` till motsvarande inbyggda SQL-funktioner.
- **Mat ris funktioner**: Stöder översättning från .net `Concat`, `Contains`och `Count` till motsvarande inbyggda SQL-funktioner.
- **Geospatiala utöknings funktioner**: Stöder översättning från stub- `Distance`metoder `IsValid` `IsValidDetailed`,,, `Within` och till motsvarande inbyggda SQL-funktioner.
- **Användardefinierad funktions utöknings funktion**: Stöder översättning från stub-metoden `UserDefinedFunctionProvider.Invoke` till motsvarande användardefinierad funktion.
- **Miscellaneous** (Övrigt): Stöder översättning av `Coalesce` och villkorliga operatorer. Kan översättas `Contains` till String innehåller, ARRAY_CONTAINS eller SQL i, beroende på kontext.

## <a name="examples"></a>Exempel

I följande exempel visas hur några av de standardiserade LINQ Query-operatörerna översätts till Cosmos DB frågor.

### <a name="select-operator"></a>Välj operator

Syntaxen är `input.Select(x => f(x))`, där `f` är ett skalärt uttryck.

**Välj operatör, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Välj Operator, exempel 2:** 

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Välj Operator, exempel 3:**

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

**Where-Operator, exempel 1:**

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
  
**Where-Operator, exempel 2:**

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

Du kan skapa föregående operatorer för att bilda mer kraftfulla frågor. Eftersom Cosmos DB stöder kapslade behållare kan du sammanfoga eller kapsla sammansättningen.

### <a name="concatenation"></a>Sammanfogning

Syntaxen är `input(.|.SelectMany())(.Select()|.Where())*`. En sammanfogad fråga kan börja med en valfri `SelectMany` fråga, följt av flera `Select` eller `Where` -operatorer.

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

Syntaxen är `input.SelectMany(x=>x.Q())` där `Q` är en `Select`, `SelectMany`-eller `Where` -operator.

En kapslad fråga använder den inre frågan för varje element i den yttre behållaren. En viktig funktion är att den inre frågan kan referera till fälten för elementen i den yttre behållaren, t. ex. en själv koppling.

**Kapsling, exempel 1:**

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

**Kapsling, exempel 2:**

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

**Kapsling, exempel 3:**

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

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modell dokument data](modeling-data.md)
