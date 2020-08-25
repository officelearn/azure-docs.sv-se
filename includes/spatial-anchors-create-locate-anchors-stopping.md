---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "83006428"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausa, återställa eller stoppa sessionen

Om du vill stoppa sessionen tillfälligt kan du anropa `Stop()` . Om du gör det stoppas alla Övervakare och miljö bearbetning, även om du anropar `ProcessFrame()` . Sedan kan du anropa `Start()` för att återuppta bearbetningen. När du återupptar underhålls miljö data som redan har registrerats i sessionen.
