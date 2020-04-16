---
title: Parametrisera mappningsdataflöden
description: Lär dig hur dupararerar ett mappningsdataflöde från datafabrikspipelor
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 82660cdb4ab6523bae7608fe3b071f20cb3603f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419178"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrisera mappningsdataflöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Mappning av dataflöden i Azure Data Factory stöder användningen av parametrar. Du kan definiera parametrar inuti dataflödesdefinitionen, som du sedan kan använda i hela uttrycken. Parametervärdena kan ställas in av anropande pipeline via aktiviteten Kör dataflöde. Du har tre alternativ för att ange värdena i dataflödesaktivitetsuttrycken:

* Använd uttrycksspråket för flödeskontroll för pipelinekontroll för att ange ett dynamiskt värde
* Använd dataflödesuttrycksspråket för att ange ett dynamiskt värde
* Använda något av uttrycksspråken för att ange ett statiskt litteralt värde

Använd den här funktionen för att göra dina dataflöden allmänt, flexibla och återanvändbara. Du kan parametriera inställningar och uttryck för dataflöde med dessa parametrar.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Skapa parametrar i ett mappningsdataflöde

Om du vill lägga till parametrar i dataflödet klickar du på den tomma delen av dataflödesarbetsytan för att se de allmänna egenskaperna. I inställningsfönstret visas en flik med namnet **Parameter**. Välj **Ny** om du vill generera en ny parameter. För varje parameter måste du tilldela ett namn, välja en typ och eventuellt ange ett standardvärde.

![Skapa parametrar för dataflöde](media/data-flow/create-params.png "Skapa parametrar för dataflöde")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Använda parametrar i ett mappningsdataflöde 

Parametrar kan refereras i alla dataflödesuttryck. Parametrar börjar med $ och är oföränderliga. Du hittar listan över tillgängliga parametrar inuti Uttrycksverktyget under fliken **Parametrar.**

![Parameteruttryck för dataflöde](media/data-flow/parameter-expression.png "Parameteruttryck för dataflöde")

Du kan snabbt lägga till ytterligare parametrar genom att välja **Ny parameter** och ange namn och typ.

![Parameteruttryck för dataflöde](media/data-flow/new-parameter-expression.png "Parameteruttryck för dataflöde")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Skicka in ett kolumnnamn som en parameter

Ett vanligt mönster är att skicka i ett kolumnnamn som ett parametervärde. Om du vill referera till kolumnen `byName()` som är associerad med parametern använder du funktionen. Kom ihåg att kasta kolumnen till sin rätta `toString()`typ med en gjutningsfunktion som .

Om du till exempel vill mappa en `columnName`strängkolumn baserat på en `toString(byName($columnName))`parameter kan du lägga till en härledd kolumnomvandling som är lika med .

![Skicka in ett kolumnnamn som en parameter](media/data-flow/parameterize-column-name.png "Skicka i ett kolumnnamn som ett paramet")

## <a name="assign-parameter-values-from-a-pipeline"></a>Tilldela parametervärden från en pipeline

När du har skapat dataflödet med parametrar kan du köra det från en pipeline med körningsdataflödesaktiviteten. När du har lagt till aktiviteten på pipeline-arbetsytan visas de tillgängliga dataflödesparametrarna på fliken **Parametrar** i aktiviteten.

![Ange en dataflödesparameter](media/data-flow/parameter-assign.png "Ange en dataflödesparameter")

Om parameterdatatypen är sträng kan du välja att ange antingen en pipeline eller ett dataflödesuttryck när du klickar på textrutan för att ange parametervärden. Om du väljer pipeline-uttryck visas pipeline-uttryckspanelen. Se till att inkludera pipeline-funktioner `'@{<expression>}'`inuti stränginterpoleringssyntax med , till exempel:

```'@{pipeline().RunId}'```

Om parametern inte är av typen sträng visas alltid dataflödesuttrycksverktyget. Här kan du ange alla uttryck eller litterala värden som du vill ha som matchar parameterns datatyp. Nedan finns exempel på dataflödesuttryck och en bokstavlig sträng från uttrycksverktyget:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Varje mappningsdataflöde kan ha valfri kombination av pipeline- och dataflödesuttrycksparametrar. 

![Exempel på dataflödesparametrar](media/data-flow/parameter-example.png "Exempel på dataflödesparametrar")



## <a name="next-steps"></a>Nästa steg
* [Utföra dataflödesaktivitet](control-flow-execute-data-flow-activity.md)
* [Styr flödesuttryck](control-flow-expression-language-functions.md)
