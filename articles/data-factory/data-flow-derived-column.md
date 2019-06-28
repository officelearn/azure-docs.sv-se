---
title: Härledda kolumnen omvandling i mappning dataflöde – Azure Data Factory | Microsoft Docs
description: Lär dig hur du omvandlar data i skala i Azure Data Factory genom att mappa Data flöda härledd kolumn omvandla.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 941c629fd8359edc7fc1cf364a6735314044d95e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312205"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Den härledda kolumnen omvandling i mappning av dataflöde

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd transformeringen härledd kolumn att generera nya kolumner i ditt dataflöde eller ändra befintliga fält.

## <a name="derived-column-settings"></a>Härledda kolumninställningar

Om du vill åsidosätta en befintlig kolumn väljer du den via i listrutan i kolumnen. Annars kan använda kolumnen val av fält som en textruta och ange den nya kolumnens namn. Om du vill skapa den härledda kolumnen uttryck, klicka på rutan ”Ange uttryck” så att du öppnar den [Data flöda Uttrycksverktyget](concepts-data-flow-expression-builder.md).

![Härledda kolumninställningar](media/data-flow/dc1.png "härledda kolumninställningar")

Om du vill lägga till ytterligare härledda kolumner, hovrar du över en befintlig härledda kolumnen och klicka på ”+”. Välj 'Lägg till kolumn ”eller” Lägg till kolumn mönstret ”. Kolumnen mönster kan bli användbar om din kolumnnamn kan variera från dina källor. Mer information finns i [kolumnen mönster](concepts-data-flow-column-pattern.md).

![Ny härledda kolumnen](media/data-flow/columnpattern.png "New härledda Kolumnurval")

## <a name="next-steps"></a>Nästa steg

- Läs mer om den [mappning dataflöde Uttrycksspråk](data-flow-expression-functions.md).
