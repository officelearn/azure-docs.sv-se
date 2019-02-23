---
title: Azure Data Factory mappning Dataomvandling Flow sökning
description: Azure Data Factory mappning Dataomvandling Flow sökning
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b156917a9987b023a9bf94e51c0cc14aebb133c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738393"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory mappning Dataomvandling Flow sökning

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använda sökning för att lägga till referensdata från en annan källa till ditt dataflöde. Det krävs en definierad källa som pekar på referenstabell och matchar på nyckelfält för sökning transformeringen.

![Lookup omvandling](media/data-flow/lookup1.png "sökning")

Välj de fält som du vill matcha i mellan de inkommande dataströmmen och fälten från käll-referens. Du måste först ha skapat en ny källa på dataflöde design arbetsytan ska användas som till höger för sökningen.

När matchningar påträffas läggs de resulterande rader och kolumner från käll-referens till ditt dataflöde. Du kan välja vilka fält av intresse som du vill inkludera i dina mottagare i slutet av ditt dataflöde.
