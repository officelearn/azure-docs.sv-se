---
title: Azure Data Factory mappning dataflödet härledda kolumnen omvandling
description: Visar hur du omvandlar data i skala med Azure Data Factory mappning Flow härledda kolumnen Dataomvandling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: f53e122eb1b2a5b6dabb9a44aef42394d0c7edb6
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999494"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>Mappning av dataflödet härledda kolumnen omvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd transformeringen härledd kolumn att generera nya kolumner i ditt dataflöde eller ändra befintliga fält.

![Härled kolumner](media/data-flow/dc1.png "härledd kolumn")

Du kan utföra flera åtgärder för härledd kolumn i en enda härledd kolumn omvandling. Klicka på ”Lägg till kolumn” för att omvandla mer än 1 kolumn i enkel omvandling steg.

Välj en befintlig kolumn att skriva över med ett nytt härledda värde i fältet kolumnen eller klicka på ”Skapa ny kolumn” för att generera en ny kolumn med nyligen härledda värdet.

Textrutan uttryck öppnas Uttrycksverktyget där du kan skapa uttrycket för de härledda kolumner med hjälp av uttryck.

## <a name="column-patterns"></a>Kolumnmönster

Om din kolumnnamn kan variera från dina källor, kan du skapa omvandlingar i den härledda kolumnen med hjälp av kolumnen mönster istället för att använda med namnet kolumner. Se den [schemat Drift](concepts-data-flow-schema-drift.md) nedan för mer information.

![mönster för kolumnen](media/data-flow/columnpattern.png "kolumnen mönster")

## <a name="next-steps"></a>Nästa steg

Läs mer om den [Data Factory Uttrycksspråk transformeringar](http://aka.ms/dataflowexpressions) och [Uttrycksverktyget](concepts-data-flow-expression-builder.md)
