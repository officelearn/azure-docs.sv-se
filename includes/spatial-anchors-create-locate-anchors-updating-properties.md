---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110521"
---
## <a name="update-properties"></a>Uppdatera egenskaper

Om du vill uppdatera egenskaperna på en fästpunkt måste du använda den `UpdateAnchorProperties()` metoden. Om två eller flera enheter försöker uppdatera egenskaper för samma förtroendeankare på samma gång, använder vi en modell för Optimistisk samtidighet. Vilket innebär att den första skrivningen ska ha.  Alla andra skrivningar får ett ”Samtidighetsfel”: en uppdatering av egenskaperna skulle behövas innan du försöker igen.
