---
title: Azure Data Factory mappa data flöde villkorlig delnings omvandling
description: Transformering av villkorlig delning för Azure Data Factory data flöde
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd51cc2d5f95598154b76b5da8e3fc9e4801100d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104952"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mappa data flöde villkorlig delnings omvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![verktyg för villkorlig delning](media/data-flow/conditionalsplit2.png "verktyg för villkorlig delning")

Den villkorliga delnings omvandlingen kan dirigera data rader till olika strömmar beroende på innehållet i data. Implementeringen av den villkorliga delnings omvandlingen liknar en ärende besluts struktur i ett programmeringsspråk. Omvandlingen utvärderar uttryck och baseras på resultaten och dirigerar data raden till den angivna data strömmen. Den här omvandlingen innehåller också en standardutdata, så att om en rad matchar inget uttryck dirigeras den till standardutdata.

![villkorlig delning](media/data-flow/conditionalsplit1.png "alternativ för villkorlig delning")

## <a name="multiple-paths"></a>Flera sökvägar

Om du vill lägga till ytterligare villkor väljer du Lägg till ström i det nedre konfigurations fönstret och klickar i text rutan uttrycks verktyg för att skapa ditt uttryck.

![villkorlig delning multi](media/data-flow/conditionalsplit3.png "villkorlig delning multi")

## <a name="next-steps"></a>Nästa steg

Vanliga data flödes omvandlingar som används med villkorlig delning: [Koppla omvandling](data-flow-join.md), [Lookup-omvandling](data-flow-lookup.md), [Välj omvandling](data-flow-select.md)
