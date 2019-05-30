---
title: Azure Data Factory mappning Dataomvandling Flow villkorlig dela
description: Azure Data Factory Data Flow villkorlig Split-transformering
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: f9fd346d4c4eaed0797d564fe52dd44e9f0e240a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795637"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mappning av dataflödet villkorlig dela omvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![villkorlig dela verktygslådan](media/data-flow/conditionalsplit2.png "villkorlig dela verktygslådan")

Villkorlig dela transformeringen kan vidarebefordra rader med data till olika dataströmmar beroende på innehållet i data. Implementeringen av villkorlig dela transformeringen liknar en CASE beslut struktur i ett programmeringsspråk. Transformeringen utvärderar uttryck, och baserat på resultatet, leder datarad till den angivna dataströmmen. Den här omvandlingen tillhandahåller även en standardutdata, så att om en rad matchar inget uttryck dirigeras den till standardutdata.

![villkorlig dela](media/data-flow/conditionalsplit1.png "villkorlig delningsalternativ")

## <a name="multiple-paths"></a>Flera sökvägar

Välj ”Lägg till Stream” längst ned i fönstret konfiguration för att lägga till ytterligare villkor, och klicka i textrutan Uttrycksverktyget för att skapa uttrycket.

![dela med flera villkorlig](media/data-flow/conditionalsplit3.png "villkorlig dela med flera")

## <a name="next-steps"></a>Nästa steg

Vanliga flödet dataomvandlingar används med villkorlig delning: [Ansluta till omvandling](data-flow-join.md), [Loopup omvandling](data-flow-lookup.md), [Välj omvandling](data-flow-select.md)
