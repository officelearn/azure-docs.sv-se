---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187056"
---
## <a name="update-properties"></a>Uppdatera egenskaper

Om du vill uppdatera egenskaperna för en fäst punkt använder du- `UpdateAnchorProperties()` metoden. Om två eller flera enheter försöker uppdatera egenskaper för samma ankare samtidigt, använder vi en optimistisk samtidighets modell. Vilket innebär att den första skrivningen kommer att vinna.  Alla andra skrivningar får ett "samtidigt"-fel: en uppdatering av egenskaperna krävs innan du försöker igen.
