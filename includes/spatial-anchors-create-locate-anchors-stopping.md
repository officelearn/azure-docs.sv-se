---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232599"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausa, återställa eller stoppa sessionen

Stoppa sessionen tillfälligt, du kan anropa `Stop()`. Då slutar att alla Övervakare och bearbetning av miljön, även om du anropar ProcessFrame(). Du kan sedan anropa `Start()` att återuppta bearbetningen. När de återställs, underhålls miljödata som redan hämtats i sessionen.
