---
author: diberry
ms.author: v-junlch
ms.service: cognitive-services
ms.topic: include
origin.date: 03/25/2019
ms.date: 04/23/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598896"
---
## <a name="validate-container-is-running"></a>Verifiera behållaren körs 

Det finns flera sätt att verifiera behållaren körs: 

|Förfrågan|Syfte|
|--|--|
|`http://localhost:5000/`|Behållaren innehåller en webbsida.|
|`http://localhost:5000/status`|Begärd med GET, körs för att verifiera behållaren utan att orsaka en slutpunkt-fråga. Detta kan användas för Kubernetes [liveness och beredskap avsökningar](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Behållaren innehåller en fullständig uppsättning av dokumentationen för slutpunkter som en `Try it now` funktionen. Den här funktionen kan du ange dina inställningar i en webbaserad HTML-formulär och gör att frågan utan att behöva skriva någon kod. När frågan returnerar, ett exempel CURL-kommando har angetts för att visa de HTTP-rubrikerna och brödtext format som krävs. |

![Behållarens startsida](./media/cognitive-services-containers-api-documentation/container-webpage.png)

