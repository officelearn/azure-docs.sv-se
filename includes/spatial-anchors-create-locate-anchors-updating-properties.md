---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "67187056"
---
## <a name="update-properties"></a>Uppdatera egenskaper

Om du vill uppdatera egenskaperna för en fäst punkt använder du- `UpdateAnchorProperties()` metoden. Om två eller flera enheter försöker uppdatera egenskaper för samma ankare samtidigt, använder vi en optimistisk samtidighets modell. Vilket innebär att den första skrivningen kommer att vinna.  Alla andra skrivningar får ett "samtidigt"-fel: en uppdatering av egenskaperna krävs innan du försöker igen.
