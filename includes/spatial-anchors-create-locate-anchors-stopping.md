---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187064"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausa, återställa eller stoppa sessionen

Om du vill stoppa sessionen tillfälligt kan du anropa `Stop()`. Om du gör det stoppas alla Övervakare och miljö bearbetning, även om du anropar ProcessFrame (). Sedan kan du anropa `Start()` för att återuppta bearbetningen. När du återupptar underhålls miljö data som redan har registrerats i sessionen.
