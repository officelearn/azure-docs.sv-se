---
title: Parametrisera mappningsdataflöden
description: Lär dig hur du Parameterisera ett mappnings data flöde från Data Factory-pipeliner
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c717d9ab44493d15589030073cd2ab260ef52e1c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760223"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrisera mappningsdataflöden

Att mappa data flöden i Azure Data Factory stödja användning av parametrar. Du kan definiera parametrar i data flödes definitionen, som du sedan kan använda i alla uttryck. Parametervärdena kan anges av den anropande pipelinen via aktiviteten kör data flöde. Det finns tre alternativ för att ställa in värden i uttryck för data flödes aktivitet:

* Använd språket för flödes kontroll flödets uttryck för att ange ett dynamiskt värde
* Använd data flödets uttrycks språk för att ange ett dynamiskt värde
* Använd antingen uttrycks språk för att ange ett statiskt litteralt värde

Använd den här funktionen för att göra dina data flöden generella, flexibla och återanvändbara. Du kan Parameterisera data flödes inställningar och uttryck med dessa parametrar.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Skapa parametrar i ett data flöde för mappning

Om du vill lägga till parametrar till ditt data flöde, klickar du på den tomma delen av data flödes arbets ytan för att se de allmänna egenskaperna. I fönstret inställningar visas en flik som heter **parameter**. Välj **ny** för att skapa en ny parameter. För varje parameter måste du tilldela ett namn, välja en typ och alternativt ange ett standardvärde.

![Skapa data flödes parametrar](media/data-flow/create-params.png "Skapa data flödes parametrar")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Använda parametrar i ett data flöde för mappning 

Det går att referera till parametrar i ett data flödes uttryck. Parametrarna börjar med $ och är oföränderliga. Du hittar listan över tillgängliga parametrar inuti uttrycks verktyget under fliken **parametrar** .

![Parameter uttryck för data flöde](media/data-flow/parameter-expression.png "Parameter uttryck för data flöde")

Du kan snabbt lägga till ytterligare parametrar genom att välja **ny parameter** och ange namn och typ.

![Parameter uttryck för data flöde](media/data-flow/new-parameter-expression.png "Parameter uttryck för data flöde")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Skicka in ett kolumn namn som en parameter

Ett vanligt mönster är att skicka in ett kolumn namn som ett parameter värde. Om du vill referera till kolumnen som är associerad med parametern använder du funktionen `byName()`. Kom ihåg att omvandla kolumnen till lämplig typ med en gjutning som `toString()`.

Om du till exempel vill mappa en sträng kolumn baserat på en parameter `columnName`kan du lägga till en härledd kolumn omvandling som motsvarar `toString(byName($columnName))`.

![Skicka in ett kolumn namn som en parameter](media/data-flow/parameterize-column-name.png "Skicka in ett kolumn namn som en paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Tilldela parameter värden från en pipeline

När du har skapat ditt data flöde med parametrar kan du köra det från en pipeline med aktiviteten kör data flöde. När du har lagt till aktiviteten till din pipeline-arbetsyta visas de tillgängliga data flödes parametrarna på fliken **parametrar** i aktiviteten.

![Ange en data flödes parameter](media/data-flow/parameter-assign.png "Ange en data flödes parameter")

Om parameter data typen är sträng, när du klickar på text rutan för att ange parameter värden, kan du välja att ange antingen en pipeline eller ett data flödes uttryck. Om du väljer pipeline-uttryck visas uttrycks panelen för pipelinen. Se till att inkludera pipeline-funktioner i syntax för String-interpolation med hjälp av `'@{<expression>}'`, till exempel:

```'@{pipeline().RunId}'```

Om parametern inte är av typen sträng visas alltid uttrycks verktyget för data flöde. Här kan du ange ett uttryck eller litterala värden som du vill ska matcha data typen för parametern. Nedan visas exempel på data flödes uttryck och en litteral sträng från uttrycks verktyget:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Varje mappnings data flöde kan ha valfri kombination av parametrar för pipeline och data flödes uttryck. 

![Exempel på data flödes parametrar](media/data-flow/parameter-example.png "Exempel på data flödes parametrar")



## <a name="next-steps"></a>Nästa steg
* [Kör data flödes aktivitet](control-flow-execute-data-flow-activity.md)
* [Kontroll flödes uttryck](control-flow-expression-language-functions.md)
