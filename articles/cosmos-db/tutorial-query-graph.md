---
title: "Hur man frågar diagramdata i Azure Cosmos DB? | Microsoft Docs"
description: "Lär dig att fråga diagramdata i Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
tags: 
ms.assetid: 8bde5c80-581c-4f70-acb4-9578873c92fa
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: denlee
ms.openlocfilehash: ee420983104f199edf57966bf22aa4d8451b4782
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2017
---
# <a name="azure-cosmos-db-how-to-query-with-the-graph-api-preview"></a>Azure Cosmos DB: Hur man frågan med Graph-API (förhandsgranskning)?

Azure Cosmos DB [Graph API](graph-introduction.md) (förhandsversion) stöder [Gremlin](https://github.com/tinkerpop/gremlin/wiki) frågor. Den här artikeln innehåller exempeldokument och frågor för att komma igång. En detaljerad Gremlin referens har angetts i den [Gremlin stöd](gremlin-support.md) artikel.

Den här artikeln omfattar följande aktiviteter: 

> [!div class="checklist"]
> * Datafrågor med Gremlin

## <a name="prerequisites"></a>Krav

För de här frågorna ska fungera måste du ha ett konto i Azure Cosmos DB och har diagramdata i behållaren. Har inte något av de? Slutför den [5 minuter quickstart](create-graph-dotnet.md) eller [developer kursen](tutorial-query-graph.md) och skapa ett konto som du kan fylla i databasen. Du kan köra följande frågor med hjälp av den [Azure Cosmos DB .NET graph-bibliotek](graph-sdk-dotnet.md), [Gremlin konsolen](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console), eller ditt favoritprogram Gremlin-drivrutinen.

## <a name="count-vertices-in-the-graph"></a>Antal formhörnen i diagrammet

Följande utdrag visar hur du räkna antalet formhörnen i diagrammet:

```
g.V().count()
```

## <a name="filters"></a>Filter

Du kan utföra filter med Gremlin's `has` och `hasLabel` steg och kombinera dem med hjälp av `and`, `or`, och `not` att skapa fler komplexa filter. Azure Cosmos-DB tillhandahåller schema-oberoende indexering av alla egenskaper i din formhörnen och grader för snabb frågor:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projektion

Du kan projicera vissa egenskaper i frågeresultatet med hjälp av den `values` steg:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Hitta relaterade kanter och formhörnen

Hittills har sett vi endast frågeoperatorer som fungerar i en databas. Diagram är snabb och effektiv för traversal åtgärder när du behöver att navigera till relaterade kanter och formhörnen. Vi ska hitta alla vänner till Thomas. Vi kan göra detta med hjälp av Gremlin's `outE` steg för att söka efter alla de kanter ut från Thomas och sedan bläddra till de i formhörnen från dessa kanter med Gremlin's `inV` steg:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

Nästa fråga utför två hopp för att söka efter alla Thomas' ”vänner till vänner”, genom att anropa `outE` och `inV` två gånger. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Du kan skapa mer komplexa frågor och implementera kraftfulla diagrammet traversal logik med Gremlin, inklusive blanda filteruttryck, utför slingor med hjälp av den `loop` steg och implementera villkorlig navigering med hjälp av den `choose` steg. Mer information om vad du kan göra med [Gremlin stöd](gremlin-support.md)!

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande:

> [!div class="checklist"]
> * Lärt dig hur man frågan använder Graph 

Du kan nu fortsätta till nästa kurs att lära dig hur du distribuerar dina data globalt.

> [!div class="nextstepaction"]
> [Distribuera dina data globalt](tutorial-global-distribution-documentdb.md)
