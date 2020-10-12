---
title: Anslut till GitHub
description: Använd GitHub för att ange dina gemensamma data modells entitetsreferenser
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 0e17580524d6a67934aed83c6f745583b92e2422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "84771044"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Använda GitHub för att läsa vanliga data modells entitetsreferenser

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

GitHub-anslutaren i Azure Data Factory används bara för att ta emot entitetens referens schema för det [gemensamma data modell](format-common-data-model.md) formatet i data flödet för mappning.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten GitHub.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **GitHub**. | ja
| userName | GitHub användar namn | ja |
| password | GitHub-lösenord | ja |

## <a name="next-steps"></a>Nästa steg

Skapa en [käll data uppsättning](data-flow-source.md) i mappnings data flödet.