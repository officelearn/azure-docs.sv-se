---
title: Azure Data Factory mappa parametrar för flödet av Data
description: Lär dig hur du Parameterisera ett dataflöde för mappning från data factory-pipelines
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a7140f70db78c8511f3c4da00b2f9c11c368163
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477708"
---
# <a name="mapping-data-flow-parameters"></a>Mappa parametrar för flödet av Data

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mappning av Data som flödar i Azure Data Factory stöder användningen av parametrar. Du kan definiera parametrar inuti flödesdefinitionen dina data som du sedan kan använda i hela din uttryck. Parametervärdena kan ställas in av den anropande pipelinen via aktiviteten kör dataflöde. Har du tre alternativ för att ställa in värdena i dataflödet aktivitet uttryck:

* Använd Uttrycksspråk för pipeline control flow för att ange ett dynamiskt värde
* Använd Uttrycksspråk för data-flöde för att ange ett dynamiskt värde
* Använd antingen Uttrycksspråk att ställa in en statisk literalvärde

Använd den här funktionen för att göra din dataflöden Allmänt, flexibla och återanvändbara. Du kan Parameterisera inställningar för flödet och uttryck med dessa parametrar.

> [!NOTE]
> Om du vill använda pipeline control flow uttryck måste din data flow-parametern vara av typen sträng.

## <a name="create-parameters-in-mapping-data-flow"></a>Skapa parametrar i mappning av dataflöde

Om du vill lägga till parametrar till ditt dataflöde, klickar du på den tomma delen av arbetsytan data flöde för att se de allmänna egenskaperna. I inställningsfönstret visas en flik som heter ”Parameters”. Klicka på knappen ”Nytt” för att generera en ny parameter. För varje parameter måste du tilldela ett namn, Välj en typ och om du vill ange ett standardvärde.

![Skapa dataflöde parametrar](media/data-flow/create-params.png "skapa dataflöde parametrar")

Parametrar kan användas i ett uttryck för flödet av data. Parametrar börja med $ och är inte kan ändras. Du hittar listan över tillgängliga parametrar inom Uttrycksverktyget under fliken ”Parameters”.

![Dataflöde parametern expression](media/data-flow/parameter-expression.png "parametern expression för dataflöde")

## <a name="use-parameters-in-your-data-flow"></a>Använda parametrar i ditt dataflöde

* Du kan använda parametervärden inuti omvandling-uttryck. Du hittar listan över under fliken Parametrar i Uttrycksverktyget. ![Använd Data flöda parametrar](media/data-flow/params9.png "användning av dataflöde parametrar")

* Parametrar används också för att konfigurera dynamiska värden för din källa och mottagare omvandling inställningar. När du klickar inuti konfigurerbara fält visas länken ”Lägg till dynamiska contect” visas. Klicka på det tar dig till en Uttrycksverktyget där du kan använda parametrar för att använda dynamiska värden. ![Dataflöde dynamiskt innehåll](media/data-flow/params6.png "dataflöde dynamiskt innehåll")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Ställa in parametrar för mappning av dataflöde från pipeline

När du har skapat ditt dataflöde med parametrar kan köra du det från en pipeline med aktiviteten kör Data flöde. När du lägger till aktiviteten i pipeline arbetsytan visas med tillgängliga data flow parametrar i aktivitetens ”Parameters” fliken.

![Ett dataflöde för parametern](media/data-flow/parameter-assign.png "en dataflöde för parametern")

Om din Parameterdatatypen är sträng, när du klickar på textrutan för att ange parametervärden, kan du ange en pipeline eller ett uttryck för flödet av data. Om du väljer pipeline-uttrycket visas panelen pipeline-uttryck. Se till att inkludera pipeline-funktioner inuti string interpolation syntaxen med ”@{<expression>}', till exempel:

```'@{pipeline().RunId}'```

Om parametern inte är av typen sträng kan visas du alltid Data flöda Uttrycksverktyget. Här kan du ange ett uttryck eller exakta värden som du vill som matchar datatypen för parametern. Nedan följer exempel på uttryck för flödet av data och en teckensträng från Uttrycksverktyget:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Varje mappning dataflöde kan ha valfri kombination av parametrarna i uttrycket flow pipeline och data. 

![Datasamplingen flow parametrar](media/data-flow/parameter-example.png "datasamplingen flow parametrar")



## <a name="next-steps"></a>Nästa steg
* [Köra data flödesaktivitet](control-flow-execute-data-flow-activity.md)
* [Control flow uttryck](control-flow-expression-language-functions.md)
