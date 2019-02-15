---
title: Azure Data Factory mappning Dataomvandling Flow sökning
description: Azure Data Factory mappning Dataomvandling Flow sökning
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 28c8f2b0005641934f7fcd9549f1cf004b206d80
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271997"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory mappning Dataomvandling Flow sökning

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använda sökning för att lägga till referensdata från en annan källa till ditt dataflöde. Det krävs en definierad källa som pekar på referenstabell och matchar på nyckelfält för sökning transformeringen.

![Lookup omvandling](media/data-flow/lookup1.png "sökning")

Välj de fält som du vill matcha i mellan de inkommande dataströmmen och fälten från käll-referens. Du måste först ha skapat en ny källa på dataflöde design arbetsytan ska användas som till höger för sökningen.

När matchningar påträffas läggs de resulterande rader och kolumner från käll-referens till ditt dataflöde. Du kan välja vilka fält av intresse som du vill inkludera i dina mottagare i slutet av ditt dataflöde.
