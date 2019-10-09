---
title: Azure Data Factory mappa data flödes parametrar
description: Lär dig hur du Parameterisera ett mappnings data flöde från Data Factory-pipeliner
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 733d3f9c4079193107f22178bdbde3a3ecf0e7ca
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028225"
---
# <a name="mapping-data-flow-parameters"></a>Mappa data flödes parametrar



Att mappa data flöden i Azure Data Factory stödja användning av parametrar. Du kan definiera parametrar i data flödes definitionen, som du sedan kan använda i alla uttryck. Parametervärdena kan anges av den anropande pipelinen via aktiviteten kör data flöde. Det finns tre alternativ för att ställa in värden i uttryck för data flödes aktivitet:

* Använd språket för flödes kontroll flödets uttryck för att ange ett dynamiskt värde
* Använd data flödets uttrycks språk för att ange ett dynamiskt värde
* Använd antingen uttrycks språk för att ange ett statiskt litteralt värde

Använd den här funktionen för att göra dina data flöden generella, flexibla och återanvändbara. Du kan Parameterisera data flödes inställningar och uttryck med dessa parametrar.

> [!NOTE]
> För att kunna använda flödes regler för pipeline-kontroll måste data flödes parametern vara av typen sträng.

## <a name="create-parameters-in-mapping-data-flow"></a>Skapa parametrar i data flöde för mappning

Om du vill lägga till parametrar till ditt data flöde, klickar du på den tomma delen av data flödes arbets ytan för att se de allmänna egenskaperna. I fönstret inställningar visas en flik med namnet Parameters. Klicka på knappen Nytt om du vill generera en ny parameter. För varje parameter måste du tilldela ett namn, välja en typ och alternativt ange ett standardvärde.

![Skapa data flödes parametrar](media/data-flow/create-params.png "skapa data flödes parametrar")

Parametrar kan användas i alla data flödes uttryck. Parametrarna börjar med $ och är oföränderliga. Du hittar listan över tillgängliga parametrar inuti uttrycks verktyget under fliken parametrar.

![](media/data-flow/parameter-expression.png "Parameter uttryck") för data flödes parameter uttryck

## <a name="use-parameters-in-your-data-flow"></a>Använda parametrar i ditt data flöde

* Du kan använda parameter värden inuti dina omvandlings uttryck. Parameter listan visas under fliken parametrar i uttrycks verktyget. ![Använd data flödes parametrar](media/data-flow/params9.png "använder data flödes parametrar")

* Parametrar används också för att konfigurera dynamiska värden för dina omvandlings inställningar för källa och mottagare. När du klickar på i konfigurerbara fält visas länken Lägg till dynamisk contect. Om du klickar på det tas du till ett uttrycks verktyg där du kan använda parametrar för att använda dynamiska värden. (media/data-flow/params6.png "Dynamiskt innehåll") för data flöde för ![dynamiskt innehåll]

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Ange mappnings data flödes parametrar från pipeline

När du har skapat ditt data flöde med parametrar kan du köra det från en pipeline med aktiviteten kör data flöde. När du har lagt till aktiviteten till din pipeline-arbetsyta visas de tillgängliga data flödes parametrarna på fliken "parametrar" i aktiviteten.

Ange ![en data]flödes parameter inställning för en(media/data-flow/parameter-assign.png "data flödes parameter")

Om parameter data typen är sträng, när du klickar på text rutan för att ange parameter värden, kan du välja att ange antingen en pipeline eller ett data flödes uttryck. Om du väljer pipeline-uttryck visas uttrycks panelen för pipelinen. Se till att ta med pipeline-funktioner i syntax för String-interpolation med `'@{<expression>}'`, till exempel:

```'@{pipeline().RunId}'```

Om parametern inte är av typen sträng visas alltid uttrycks verktyget för data flöde. Här kan du ange ett uttryck eller litterala värden som du vill ska matcha data typen för parametern. Nedan visas exempel på data flödes uttryck och en litteral sträng från uttrycks verktyget:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Varje mappnings data flöde kan ha valfri kombination av parametrar för pipeline och data flödes uttryck. 

![Data flödes parametrar]exempel på(media/data-flow/parameter-example.png "data flödes parametrar")



## <a name="next-steps"></a>Nästa steg
* [Kör data flödes aktivitet](control-flow-execute-data-flow-activity.md)
* [Kontroll flödes uttryck](control-flow-expression-language-functions.md)
