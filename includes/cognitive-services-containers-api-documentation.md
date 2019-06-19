---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: f4925401235aedb341a7e29ca36b079126647f7b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187617"
---
## <a name="validate-that-a-container-is-running"></a>Kontrollera att en behållare körs 

Det finns flera sätt att kontrollera att behållaren körs. 

|Förfrågan|Syfte|
|--|--|
|`http://localhost:5000/`|Behållaren innehåller en startsida.|
|`http://localhost:5000/status`|Begärt med GET för att kontrollera att behållaren körs utan att orsaka en slutpunkt-fråga. Den här förfrågan kan användas för Kubernetes [liveness och beredskap avsökningar](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Behållaren innehåller en fullständig uppsättning av dokumentationen för slutpunkter och en `Try it now` funktionen. Med den här funktionen kan du ange dina inställningar till en webbaserad HTML-formulär och gör att frågan utan att behöva skriva någon kod. Efter att frågan returnerar är ett exempel CURL-kommandot tillgängligt för att demonstrera HTTP-rubriker och brödtext format som krävs. |

![Behållarens startsida](./media/cognitive-services-containers-api-documentation/container-webpage.png)
