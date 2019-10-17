---
title: Azure Data Factory mappa data flöde villkorlig delnings omvandling
description: Transformering av villkorlig delning för Azure Data Factory data flöde
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d06b5b86737d0940930a3ccea3b6d65be0a802f9
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387896"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mappa data flöde villkorlig delnings omvandling



![verktyg för villkorlig delning](media/data-flow/conditionalsplit2.png "verktyg för villkorlig delning")

Den villkorliga delnings omvandlingen kan dirigera data rader till olika strömmar beroende på innehållet i data. Implementeringen av den villkorliga delnings omvandlingen liknar en ärende besluts struktur i ett programmeringsspråk. Omvandlingen utvärderar uttryck och baseras på resultaten och dirigerar data raden till den angivna data strömmen. Den här omvandlingen innehåller också en standardutdata, så att om en rad matchar inget uttryck dirigeras den till standardutdata.

![villkorlig delning](media/data-flow/conditionalsplit1.png "alternativ för villkorlig delning")

## <a name="multiple-paths"></a>Flera sökvägar

Om du vill lägga till ytterligare villkor väljer du Lägg till ström i det nedre konfigurations fönstret och klickar i text rutan uttrycks verktyg för att skapa ditt uttryck.

![villkorlig delning multi](media/data-flow/conditionalsplit3.png "villkorlig delning multi")

## <a name="next-steps"></a>Nästa steg

Transformeringar av gemensamt data flöde som används med villkorlig delning: [koppla omvandling](data-flow-join.md), [Lookup-omvandling](data-flow-lookup.md), [Välj omvandling](data-flow-select.md)
