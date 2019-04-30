---
title: Azure Data Factory mappning Dataomvandling Flow sökning
description: Azure Data Factory mappning Dataomvandling Flow sökning
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef473ea5f88b9108894787785fe1e9083fab1b0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348183"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory mappning Dataomvandling Flow sökning

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använda sökning för att lägga till referensdata från en annan källa till ditt dataflöde. Det krävs en definierad källa som pekar på referenstabell och matchar på nyckelfält för sökning transformeringen.

![Lookup omvandling](media/data-flow/lookup1.png "sökning")

Välj de fält som du vill matcha i mellan de inkommande dataströmmen och fälten från käll-referens. Du måste först ha skapat en ny källa på dataflöde design arbetsytan ska användas som till höger för sökningen.

När matchningar påträffas läggs de resulterande rader och kolumner från käll-referens till ditt dataflöde. Du kan välja vilka fält av intresse som du vill inkludera i dina mottagare i slutet av ditt dataflöde.

## <a name="optimizations"></a>Optimeringar

I Data Factory kör Data flödar i utskalade Spark miljöer. Om din datauppsättning passar in i minnesutrymme för worker-nod, optimera vi prestanda för din sökning.

![Broadcast-koppling](media/data-flow/broadcast.png "Broadcast-koppling")

### <a name="broadcast-join"></a>Broadcast-koppling

Välj till vänster och/eller höger broadcast-koppling för att begära ADF om du vill skicka hela datamängden från endera sidan av uppslagsrelation i minnet.

### <a name="data-partitioning"></a>Datapartitionering

Du kan också ange partitionering av dina data genom att välja ”Ange Datapartitionering” på fliken Optimize Lookup omvandlingen att skapa uppsättningar av data som kan passa bättre minne per worker.

## <a name="next-steps"></a>Nästa steg

[Ansluta till](data-flow-join.md) och [Exists](data-flow-exists.md) transformationer utföra liknande aktiviteter i ADF mappning Data flödar. Ta en titt på dessa transformationer nästa.
