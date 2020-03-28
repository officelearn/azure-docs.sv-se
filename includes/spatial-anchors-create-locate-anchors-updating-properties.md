---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "67187056"
---
## <a name="update-properties"></a>Uppdatera egenskaper

Om du vill uppdatera egenskaperna för `UpdateAnchorProperties()` ett ankare använder du metoden. Om två eller flera enheter försöker uppdatera egenskaper för samma ankare samtidigt använder vi en optimistisk samtidighetsmodell. Vilket innebär att den första skrivningen kommer att vinna.  Alla andra skrivningar kommer att få ett "Samtidighet" fel: en uppdatering av egenskaperna skulle behövas innan du försöker igen.
