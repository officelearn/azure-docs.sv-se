---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 00cc63f53388ab7bea05a0b55784247f63477684
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704198"
---
## <a name="validate-that-a-container-is-running"></a>Kontrollera att en behållare körs 

Det finns flera sätt att kontrollera att behållaren körs. 

|Förfrågan|Syfte|
|--|--|
|`http://localhost:5000/`|Behållaren innehåller en startsida.|
|`http://localhost:5000/status`|Begärt med GET för att kontrollera att behållaren körs utan att orsaka en slutpunkt-fråga. Den här förfrågan kan användas för Kubernetes [liveness och beredskap avsökningar](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Behållaren innehåller en fullständig uppsättning av dokumentationen för slutpunkter och en `Try it now` funktionen. Med den här funktionen kan du ange dina inställningar till en webbaserad HTML-formulär och gör att frågan utan att behöva skriva någon kod. Efter att frågan returnerar är ett exempel CURL-kommandot tillgängligt för att demonstrera HTTP-rubriker och brödtext format som krävs. |

![Behållarens startsida](./media/cognitive-services-containers-api-documentation/container-webpage.png)
