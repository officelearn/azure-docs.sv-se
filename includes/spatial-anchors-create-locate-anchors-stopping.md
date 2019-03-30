---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632719"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausa, återställa eller stoppa sessionen

Stoppa sessionen tillfälligt, du kan anropa `Stop()`. Då slutar att alla Övervakare och bearbetning av miljön, även om du anropar ProcessFrame(). Du kan sedan anropa `Start()` att återuppta bearbetningen. När de återställs, underhålls miljödata som redan hämtats i sessionen.
