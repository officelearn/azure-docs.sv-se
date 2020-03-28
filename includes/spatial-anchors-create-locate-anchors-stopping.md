---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "67187064"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausa, återställa eller stoppa sessionen

Om du vill stoppa sessionen `Stop()`tillfälligt kan du anropa . Om du gör det stoppas alla watchers och miljöbearbetning, även om du anropar ProcessFrame(). Du kan sedan `Start()` anropa för att återuppta bearbetningen. När du återupptar underhålls miljödata som redan tagits in i sessionen.
