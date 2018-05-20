---
title: Hur frågar man efter diagramdata SQL i Azure Cosmos DB? | Microsoft Docs
description: Lär dig att fråga efter diagramdata SQL i Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: luisbosquez
manager: kfile
editor: ''
tags: ''
ms.assetid: 8bde5c80-581c-4f70-acb4-9578873c92fa
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 449821d6121f8fec40b151ae06f687586133c3d1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="tutorial-query-azure-cosmos-db-graph-api-by-using-gremlin"></a>Självstudie: Fråga Azure Cosmos DB Graph API med hjälp av Gremlin

Azure Cosmos DB [Graph API](graph-introduction.md) stöder [Gremlin](https://github.com/tinkerpop/gremlin/wiki)-frågor. Den här artikeln innehåller exempeldokument och frågor för att komma igång. En detaljerad Gremlin-referens finns i artikeln [Gremlin-support](gremlin-support.md).

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Fråga efter data med Gremlin

## <a name="prerequisites"></a>Nödvändiga komponenter

För att de här frågorna ska fungera måste du ha ett konto i Azure Cosmos DB och ha diagramdata i behållaren. Har du detta? Slutför [snabbstarten på 5 minuter](create-graph-dotnet.md) eller [självstudien för utvecklare](tutorial-query-graph.md) om du behöver skapa ett konto och fylla i databasen. Du kan köra följande frågor med hjälp av [Azure Cosmos DB .NET-diagrambiblioteket](graph-sdk-dotnet.md), [Gremlin-konsolen](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console), eller din favoritdrivrutin för Gremlin.

## <a name="count-vertices-in-the-graph"></a>Antal hörn i diagrammet

Följande kodfragment visar hur du räknar antalet hörn i diagrammet:

```
g.V().count()
```

## <a name="filters"></a>Filter

Du kan använda filter med Gremlins `has` och `hasLabel`-steg, samt kombinera dem med hjälp av `and`, `or` och `not` för att skapa fler komplexa filter. Azure Cosmos DB innehåller schemaoberoende indexering av alla egenskaper i dina hörn och grader för snabba frågor:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projektion

Du kan projicera vissa egenskaper i frågeresultaten med hjälp av steget `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Hitta relaterade kanter och hörn

Hittills har vi endast sett frågeoperatorer som fungerar i alla databaser. Diagram är snabba och effektiva vid bläddringsåtgärder när du behöver navigera till relaterade kanter och hörn. Nu ska vi hitta alla Thomas vänner. Vi kan göra detta med hjälp av Gremlins `outE`-steg för att söka efter alla ut-kanter från Thomas och sedan bläddra till in-hörnen från dessa kanter med Gremlins `inV`-steg:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

Nästa fråga utför två hopp för att hitta alla Thomas ”vänner till vänner”, genom att anropa `outE` och `inV` två gånger. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Du kan skapa mer komplexa frågor och implementera kraftfull bläddringslogik i diagrammet med Gremlin, inklusive att blanda filteruttryck, utföra loopar med hjälp av `loop`-steget och implementera villkorlig navigering med hjälp av `choose`-steget. Läs mer om vad du kan göra med [Gremlin-support](gremlin-support.md)!

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Lärt dig hur man frågar med Graph 

Du kan nu fortsätta till nästa självstudie för att lära dig hur du distribuerar dina data globalt.

> [!div class="nextstepaction"]
> [Distribuera dina data globalt](tutorial-global-distribution-graph.md)
