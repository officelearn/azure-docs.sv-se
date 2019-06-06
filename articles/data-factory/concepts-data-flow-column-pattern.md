---
title: Azure Data Factory mappning datamönster Flow kolumn
description: Lär dig hur du använder Azure Data Factory kolumnen mönster i mappning dataflöde för att skapa generaliserad mall mönster för omvandling av fälten i ett dataflöde utan hänsyn till underliggande schemametadata
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 08cdaafe00b7dc586ea75f6ff03fdb89107edee9
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430753"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Azure data factory mappningsdata flödar kolumnen mönster

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Flera Azure Data Factory-dataflöde transformationer stöder uppfattning om ”kolumner mönster” så att du kan skapa mallen kolumner baserat på mönster i stället för hårdkodade kolumnnamn. Du kan använda den här funktionen i Uttrycksverktyget för att definiera mönster för att matcha kolumner för omvandling i stället för att exakt, specifika fältnamn. Mönster är användbara om inkommande källfält ändras ofta, särskilt om du ska ändra kolumner i textfiler eller NoSQL-databaser. Det här tillståndet kallas ibland för ”Schema Drift”.

![kolumnen mönster](media/data-flow/columnpattern2.png "kolumnen mönster")

Kolumnen mönster är användbara för att hantera både Schema-Drift scenarier samt allmänna scenarier. Det är bra för villkor där du kan inte helt veta varje kolumnnamn. Du kan mönstret matchar på kolumnnamnet och datatypen för kolumnen och skapa ett uttryck för omvandling som utför åtgärden mot alla fält i dataströmmen som matchar din `name`  &  `type` mönster.

När du lägger till ett uttryck en transformering som accepterar mönster, välj ”Lägg till kolumn mönstret”. Kolumnen mönster kan schemat drift kolumnen matchande mönster.

När du skapar mallen kolumnen mönster, använda `$$` i uttryck som representerar en referens till varje matchade fält från den inkommande dataströmmen.

Om du väljer att använda en av de Uttrycksverktyget regex-funktionerna kan du sedan senare använda $1, $2, $3... att referera till de underordnade mönster som matchade från regex-uttrycket.

Ett exempel på kolumnen mönstret scenario använder SUMMAN med en serie inkommande fält. Sammanställd SUMMAN beräkningar finns i samlingen omvandling. Du kan sedan använda SUMMAN på varje matchning av fälttyper som matchar ”heltal” och sedan använda $ för att referera till varje matchning i uttrycket.
