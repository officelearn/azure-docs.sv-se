---
title: LINQ till SQL-översättning i Azure Cosmos DB
description: Mappa LINQ-frågor till Azure Cosmos DB SQL-frågor.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 057614da8fd29e1208c2788049c5d6d1a985eed5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342787"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL-översättning

Azure Cosmos DB-frågeprovider utför en bästa arbete-mappning från en LINQ-fråga till en Cosmos DB SQL-fråga. Följande beskrivning förutsätter en grundläggande kunskaper om LINQ.

Typsystemet fråga providern stöder endast JSON primitiva typer: numeriska och booleskt, sträng, och null.

Fråga providern har stöd för följande skalära uttryck:

- Konstanta värden, inklusive konstanta värden för de primitiva datatyperna vid frågetiden för utvärdering.
  
- Egenskapen/matris indexuttryck som refererar till egenskapen för ett objekt eller ett matriselement. Exempel:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetiska uttryck, inklusive vanliga matematiska uttryck på numeriska och booleska värden. Fullständig lista finns i den [Azure Cosmos DB SQL-specifikationen](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Sträng jämförelseuttryck bland annat jämför ett strängvärde till vissa konstant strängvärde.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Objektmatris/skapande uttryck som returnerar ett objekt av typ sammansatt värde eller anonym typ eller en matris med sådana objekt. Du kan kapsla dessa värden.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>LINQ-operatorer som stöds

LINQ-provider som ingår i SQL .NET SDK stöder följande operatorer:

- **Select**: Projektioner översätts till SQL SELECT, inklusive objektkonstruktion.
- **Where**: Filter översätter till SQL WHERE och stöd mellan `&&`, `||`, och `!` till SQL-operatorer
- **SelectMany**: Tillåter uppspolning av matriser till SQL JOIN-satsen. Använd för att länka eller kapsla uttryck för att filtrera på matriselement.
- **OrderBy** och **OrderByDescending**: Omvandla till ORDER BY-med ASC eller DESC.
- Operatorerna **Count**, **Sum**, **Min**, **Max** och **Average** för sammansättning och deras async-motsvarigheter **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** och **AverageAsync**.
- **CompareTo**: Översätts till intervalljämförelser. Används vanligtvis för strängar, eftersom de inte är jämförbar i .NET.
- **Take**: Översätts till SQL längst upp för att begränsa resultaten av en fråga.
- **Matematikfunktioner**: Har stöd för översättning från .NET `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan`, och `Truncate` till motsvarande SQL inbyggda funktioner.
- **Sträng funktioner**: Har stöd för översättning från .NET `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`, och `TrimStart` till motsvarande SQL inbyggda funktioner.
- **Matrisen funktioner**: Har stöd för översättning från .NET `Concat`, `Contains`, och `Count` till motsvarande SQL inbyggda funktioner.
- **Tillägg för geospatiala funktioner**: Har stöd för översättning av stub-metoder `Distance`, `IsValid`, `IsValidDetailed`, och `Within` till motsvarande SQL inbyggda funktioner.
- **En användardefinierad funktion tilläggsfunktion**: Har stöd för översättning från metoden stub- `UserDefinedFunctionProvider.Invoke` till motsvarande användardefinierade funktionen.
- **Miscellaneous** (Övrigt): Har stöd för översättning av `Coalesce` och villkorlig operatörer. Kan översätta `Contains` strängen innehåller, ARRAY_CONTAINS eller IN-SQL, beroende på kontext.

## <a name="examples"></a>Exempel

I följande exempel visas hur några av standard LINQ-frågeoperatorer översätta på Cosmos DB-frågor.

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

**Där operatör, exempel 1:**

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
  
**Där operatör, exempel 2:**

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

Du kan skapa föregående operatorer för att skapa mer kraftfulla frågor. Eftersom Cosmos DB stöder kapslade behållare, kan du sammanfoga eller kapsla sammansättning.

### <a name="concatenation"></a>Sammanfogning

Syntaxen är `input(.|.SelectMany())(.Select()|.Where())*`. En sammansatt fråga kan börja med en valfri `SelectMany` fråga, följt av flera `Select` eller `Where` operatörer.

**Sammanfoga, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Sammanfoga exempel 2:**

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

**Sammanfoga exempel 3:**

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

**Sammanfoga exempel 4:**

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

Syntaxen är `input.SelectMany(x=>x.Q())` där `Q` är en `Select`, `SelectMany`, eller `Where` operator.

En kapslad fråga gäller den inre frågan för varje element i behållaren yttre. En viktig funktion är att den inre frågan kan referera till fält i element i behållaren yttre som en självkoppling.

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
- [Modelldokumentdata](modeling-data.md)
