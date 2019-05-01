---
title: Azure Data Factory mappning dataflödet härledda kolumnen omvandling
description: Visar hur du omvandlar data i skala med Azure Data Factory mappning Flow härledda kolumnen Dataomvandling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 6568e5ebf356bb0e6b4ac8ff6059cd093f8da821
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917567"
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

Läs mer om den [Data Factory Uttrycksspråk transformeringar](https://aka.ms/dataflowexpressions) och [Uttrycksverktyget](concepts-data-flow-expression-builder.md)
