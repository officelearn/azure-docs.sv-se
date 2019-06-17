---
title: Azure Data Factory-mappning för flow dataarametrar
description: Lär dig hur du Parameterisera ett dataflöde för mappning från data factory-pipelines
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: af5f421cc3802f3a7ad44bb294f5066c32569f8b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082890"
---
# <a name="mapping-data-flow-parameters"></a>Mappningen dataarametrar flöde

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mappa data stöder flöden i data factory användningen av parametrar. Du kan definiera parametrar inuti flödesdefinitionen dina data som du sedan kan använda i hela din uttryck. Parametrarna kan sedan anges av den anropande pipelinen via aktiviteten kör dataflöde. Du har tre alternativ för att använda för att ange värden i dataflödet aktivitet uttryck:

* Använd Uttrycksspråk för pipeline control flow för att ange ett dynamiskt värde
* Använd Uttrycksspråk för data-flöde för att ange ett dynamiskt värde
* Använd antingen Uttrycksspråk att ställa in en statisk literalvärde

Använd den här funktionen för att göra din dataflöden Allmänt, flexibla och återanvändbara. Du kan Parameterisera inställningar för flödet och uttryck med dessa parametrar.

> [!NOTE]
> Om du vill använda pipeline control flow uttryck måste din data flow-parametern vara av typen sträng.

* Lägga till en aktivitet som kör dataflöde i rityta för pipelinen.
* Om ditt dataflöde har parametrar, visas listan över tillgängliga parametrar i parametrar tab.* * klickar du på rutan bredvid varje parameter anger parametervärde.
* Du kan välja att skapa din parametern expression via pipeline control flow-Uttrycksspråk eller data flow-uttryck.

![Dataflöde parametrar 3](media/data-flow/params3.png "dataflöde parametrar 3")

## <a name="create-parameters-in-data-flow"></a>Skapa parametrar i dataflöde

![Dataflöde parametrar 1](media/data-flow/params1.png "dataflöde parametrar 1")

Om du vill lägga till parametrar till ditt dataflöde, klickar du på den tomma delen av arbetsytan data flöde för att se de allmänna egenskaperna. I inställningsfönstret visas en flik som heter parametrar. Klicka på knappen Nytt för att generera nya parametrar som sedan kan ställas in från pipelinen, skicka värden till ditt dataflöde. Ange ett parameternamn och välj datatypen för varje parameter.

Du kan använda parametrar med de värden som anges från pipeline i data flow-uttryck. Parametrar börja med $ och är inte kan ändras. Du hittar även listan med tillgängliga parametrarna i Uttrycksverktyget under fliken Parametrar. Du kan använda dessa värden i ditt uttryck, men du inte kan tilldela nya värden till parametrarna.

![Dataflöde parametrar 2](media/data-flow/params2.png "dataflöde parametrar 2")

## <a name="set-data-flow-parameters-from-pipeline"></a>Ange data flow parametrar från pipeline

När du har skapat ditt dataflöde med parametrar, kan du nu köra dataflödet från en pipeline med aktiviteten kör Data flöde. När du lägger till den aktiviteten i din pipeline arbetsyta visas med tillgängliga data flow parametrar i aktivitetens egenskaper som styr fliken.

![Dataflöde parametrar Uttrycksspråk](media/data-flow/params4.png "dataflöde parametrar Uttrycksspråk")

Om du klickar på textrutan egna parametervärden visas Data flöda Uttrycksverktyget. Här kan du ange ett uttryck eller exakta värden som du vill som matchar datatypen för parametern. Nedan följer exempel på uttryck för flödet av data och en teckensträng från Uttrycksverktyget:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Om din Parameterdatatypen är en sträng, kan du välja att ange en pipeline eller ett uttryck för flödet av data. Om du väljer pipeline-uttryck, i stället visas med panelen pipeline-uttryck. Se till att inkludera pipeline-funktioner inuti string interpolation syntaxen med ”@{<expression>}', till exempel:

```'@{pipeline().RunId}'```

![Datasamplingen flow parametrar](media/data-flow/params5.png "datasamplingen flow parametrar")

## <a name="next-steps"></a>Nästa steg

* [Köra data flödesaktivitet](control-flow-execute-data-flow-activity.md)
* [Control flow uttryck](control-flow-expression-language-functions.md)
