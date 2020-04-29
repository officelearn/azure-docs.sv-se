---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76887643"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Hitta en moln rums ankare

Att kunna hitta en tidigare sparad moln rums fäst punkt är en av de viktigaste orsakerna till att använda Azures ankare för avstånd. Det finns flera olika sätt att hitta ett moln rums ankare. Du kan använda en strategi på en övervakare i taget.
- Hitta ankare per identifierare.
- Placera fäst punkter som är kopplade till ett tidigare placerat ankare. Du kan lära dig om ankar relationer [här](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Hitta ankare med hjälp av [grov omlokalisering](/azure/spatial-anchors/concepts/coarse-reloc/).

Om du letar efter ID för moln rums ankare per identifierare, vill du lagra molnets plats för moln rums ankare i programmets backend-tjänst och göra den tillgänglig för alla enheter som kan autentiseras på rätt sätt. Ett exempel på detta finns i [Självstudier: dela spatiala ankare mellan enheter](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instansiera ett `AnchorLocateCriteria` objekt, ange de identifierare som du letar efter och anropa `CreateWatcher` metoden i sessionen genom att tillhandahålla din. `AnchorLocateCriteria`