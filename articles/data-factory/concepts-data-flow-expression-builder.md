---
title: Azure Data Factory mappning Data Flow Uttrycksverktyget
description: Uttrycksverktyget för Azure Data Factory mappningsdata flöden
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: dab0a6a5eee8893f28a221b44d57afe255841fa0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329753"
---
# <a name="mapping-data-flow-expression-builder"></a>Mappningen Data Flow Uttrycksverktyget

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

I Azure Data Factory mappning dataflöde hittar du uttryck rutor där du kan ange uttryck för transformering av data. Använd kolumner, fält, variabler, parametrar, funktioner från ditt dataflöde i dessa rutor. Om du vill skapa uttrycket använda Uttrycksverktyget, som startas genom att klicka i textrutan uttryck i transformeringen. Du ser även ibland ”beräknad kolumn” alternativ när du väljer kolumner för omvandling. När du klickar på som visas också Uttrycksverktyget startas.

![Uttrycksverktyget](media/data-flow/expression.png "Uttrycksverktyget")

Verktyget Uttrycksverktyget som standard alternativet text redigeraren. funktionen för automatisk komplettering läser från objektmodellen hela Azure Data Factory-dataflöde med syntax kontrollerar och markeringar.

![Uttrycksverktyget automatisk komplettering](media/data-flow/expb1.png "Uttrycksverktyget automatisk komplettering")

## <a name="currently-working-on-field"></a>För närvarande arbetar med fält

![Uttrycksverktyget](media/data-flow/exp3.png "för närvarande arbetar med")

Längst upp till vänster i uttrycket Builder-Användargränssnittet, visas ett fält med namnet ”för närvarande arbetar på” med namnet på fältet som du arbetar med. Uttrycket som du skapar i Användargränssnittet kommer att gälla precis som för närvarande arbetsområde. Om du vill att omvandla ett annat fält kan spara ditt aktuella arbete och använder den här listrutan att välja ett annat fält och skapa ett uttryck för andra fält.

## <a name="data-preview-in-debug-mode"></a>Förhandsgranskning i felsökningsläge

![Uttrycksverktyget](media/data-flow/exp4b.png "uttryck förhandsgranskning")

När du arbetar på ditt uttryck kan växla du om du vill på felsökningsläge från designyta Azure Data Factory-dataflöde aktiverar dynamisk pågående förhandsgranskning av data resultaten från uttrycket som du skapar. I realtid live-felsökning är aktiverad för din uttryck.

![Felsökningsläge](media/data-flow/debugbutton.png "felsöka knappen")


![Uttrycksverktyget](media/data-flow/exp5.png "uttryck förhandsgranskning")

## <a name="comments"></a>Kommentarer

Lägga till kommentarer i ditt uttryck med hjälp av enda rad och flerradiga kommentarssyntax:

![Kommentarer](media/data-flow/comments.png "kommentarer")

## <a name="regular-expressions"></a>Reguljära uttryck

Uttrycksspråk Azure Data Factory-dataflöde [här fullständig referensdokumentation](http://aka.ms/dataflowexpressions), möjliggör funktioner som innehåller syntax för reguljära uttryck. När du använder reguljära uttryck, Uttrycksverktyget försöker tolka omvänt snedstreck (\) som en sekvens för escape-tecken. När du använder snedstreck i din reguljära uttryck måste du omge hela regex i ticken ` ` eller Använd en dubbla omvända snedstrecken.

Exempel på användning av skalstreck

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

eller använda dubbla snedstreck

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Adressering matris index

Med funktioner för uttryck som returnerar matriser, använder du hakparenteser [] att åtgärda specifika index i den returnerade matrisobjekt. Matrisen är de-baserade.

![Uttrycket Builder matris](media/data-flow/expb2.png "uttryck förhandsgranskning")
