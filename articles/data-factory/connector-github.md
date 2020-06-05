---
title: Anslut till GitHub
description: Använd GitHub för att ange dina gemensamma data modells entitetsreferenser
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 3ad666a477595b8367b388c4343df8aaed561a87
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435445"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Använda GitHub för att läsa vanliga data modells entitetsreferenser

GitHub-anslutaren i Azure Data Factory används bara för att ta emot entitetens referens schema för det [gemensamma data modell](format-common-data-model.md) formatet i data flödet för mappning.

## <a name="linked-service-properties"></a>Egenskaper för länkad tjänst

Följande egenskaper stöds för den länkade tjänsten GitHub.

| Egenskap | Beskrivning | Obligatorisk |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till **GitHub**. | ja
| userName | GitHub användar namn | ja |
| password | GitHub-lösenord | ja |

## <a name="next-steps"></a>Efterföljande moment

Skapa en [käll data uppsättning](data-flow-source.md) i mappnings data flödet.