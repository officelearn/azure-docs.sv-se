---
title: Härledd kolumn omvandling i data flöde för mappning – Azure Data Factory | Microsoft Docs
description: Lär dig hur du omvandlar data i skala i Azure Data Factory med den härledda kolumn omvandlingen för data flöde.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aacd6f1799f1813e168bd04e78f18cf60ad5243f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026846"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Härledd kolumn omvandling i data flöde för mappning

Använd omvandlingen för härledd kolumn för att skapa nya kolumner i ditt data flöde eller ändra befintliga fält.

## <a name="derived-column-settings"></a>Härledda kolumn inställningar

Om du vill åsidosätta en befintlig kolumn väljer du den via List rutan kolumn. Annars använder du fältet kolumn markering som en text ruta och skriver in namnet på den nya kolumnen. Skapa uttrycket för den härledda kolumnen genom att klicka på rutan "Ange uttryck" för att öppna [uttrycks verktyget för data flödet](concepts-data-flow-expression-builder.md).

Härledda ![kolumn inställningar](media/data-flow/dc1.png "härledda kolumn inställningar")

Om du vill lägga till ytterligare härledda kolumner, Hovra över en befintlig härledd kolumn och klicka på "+". Välj sedan antingen Lägg till kolumn eller Lägg till kolumn mönster. Kolumn mönster kan komma att vara praktiska om kolumn namnen är variabla från dina källor. Mer information finns i [kolumn mönster](concepts-data-flow-column-pattern.md).

![Ny härledd kolumn markering](media/data-flow/columnpattern.png "ny härledd kolumn markering")

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [språket för mappning av data Flow-uttryck](data-flow-expression-functions.md).
