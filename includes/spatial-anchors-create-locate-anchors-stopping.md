---
ms.openlocfilehash: 8e02067b32d9404a5bbdf7362b2cc32712b96250
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907777"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausa, återställa eller stoppa sessionen

Om du vill stoppa sessionen tillfälligt, kan du anropa stop (). Då slutar att alla Övervakare och bearbetning av miljön, även om du anropar ProcessFrame(). Du kan sedan anropa Start() för att återuppta bearbetningen. När de återställs, underhålls miljödata som redan hämtats i sessionen.
