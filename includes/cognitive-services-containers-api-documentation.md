---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70034431"
---
## <a name="validate-that-a-container-is-running"></a>Verifiera att en behållare körs 

Det finns flera sätt att verifiera att behållaren körs. Leta reda på den *externa IP-adressen* och den exponerade porten för behållaren i fråga och öppna din favoritwebbläsare. Använd de olika url:erna nedan för att verifiera att behållaren körs. De exempel på webbadresser `http://localhost:5000`som anges nedan är , men din specifika behållare kan variera. Tänk på att du ska förlita dig på behållarens *externa IP-adress* och exponerade port.

| URL för begäran | Syfte |
|--|--|
| `http://localhost:5000/` | Behållaren innehåller en startsida. |
| `http://localhost:5000/status` | Begärs med en HTTP GET, för att verifiera att behållaren körs utan att orsaka en slutpunktsfråga. Denna begäran kan användas för Kubernetes [liveness och beredskap sonder](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/). |
| `http://localhost:5000/swagger` | Behållaren innehåller en fullständig uppsättning dokumentation för slutpunkterna och en **Prova-funktion.** Med den här funktionen kan du ange dina inställningar i ett webbaserat HTML-formulär och göra frågan utan att behöva skriva någon kod. När frågan har returnerats tillhandahålls ett exempel på CURL-kommandot för att demonstrera de HTTP-huvuden och brödtextformat som krävs. |

![Behållarens hemsida](./media/cognitive-services-containers-api-documentation/container-webpage.png)
