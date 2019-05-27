---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110521"
---
## <a name="update-properties"></a>Uppdatera egenskaperna

Om du vill uppdatera egenskaperna på en fästpunkt måste du använda den `UpdateAnchorProperties()` metoden. Om två eller flera enheter försöker uppdatera egenskaper för samma förtroendeankare på samma gång, använder vi en modell för Optimistisk samtidighet. Vilket innebär att den första skrivningen ska ha.  Alla andra skrivningar får ett ”Samtidighetsfel”: en uppdatering av egenskaperna skulle behövas innan du försöker igen.
