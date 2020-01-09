---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "70034431"
---
## <a name="validate-that-a-container-is-running"></a>Verifiera att en behållare körs 

Det finns flera sätt att verifiera att behållaren körs. Leta reda på den *externa IP-* adressen och den exponerade porten för den aktuella behållaren och öppna din favorit webbläsare. Använd de olika URL: erna för begäran nedan för att verifiera att behållaren körs. URL: erna för exempel förfrågningar som anges nedan är `http://localhost:5000`, men din speciella behållare kan variera. Tänk på att du är beroende av behållarens *externa IP* -adress och exponerad port.

| URL för begäran | Syfte |
|--|--|
| `http://localhost:5000/` | Behållaren är en start sida. |
| `http://localhost:5000/status` | Begärde med en HTTP GET för att verifiera att behållaren körs utan att orsaka en slut punkts fråga. Den här begäran kan användas för Kubernetes [Live och beredskaps avsökningar](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/). |
| `http://localhost:5000/swagger` | Behållaren innehåller en fullständig uppsättning dokumentation för slut punkterna och en funktion för att **testa den** . Med den här funktionen kan du ange dina inställningar i ett webbaserat HTML-formulär och göra frågan utan att behöva skriva någon kod. När frågan returnerar visas ett exempel på ett spiral kommando som visar de HTTP-rubriker och det text format som krävs. |

![Behållarens start sida](./media/cognitive-services-containers-api-documentation/container-webpage.png)
