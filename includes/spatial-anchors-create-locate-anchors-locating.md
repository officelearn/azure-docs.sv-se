---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76887643"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Hitta ett rumsligt molnankringsankringsankring

Att kunna hitta ett tidigare sparat molnrumligt ankare är en av de främsta anledningarna till att använda Azure Spatial Anchors. Det finns flera olika sätt att hitta ett rumsligt molnankringsankring. Du kan använda en strategi på en watcher i taget.
- Leta reda på ankare efter identifierare.
- Leta reda på fästpunkter som är anslutna till ett tidigare placerat ankare. Du kan lära dig mer om ankarrelationer [här](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Leta reda på ankare med [grov omlokalisering](/azure/spatial-anchors/concepts/coarse-reloc/).

Om du hittar molnrumliga ankare efter identifierare, vill du lagra molnet rumsliga ankare identifierare i programmets backend-tjänst, och göra den tillgänglig för alla enheter som kan korrekt autentisera till den. Ett exempel på detta finns i [Självstudiekurs: Dela rumsliga ankare på olika enheter](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instansiera `AnchorLocateCriteria` ett objekt, ange identifierare du letar efter och `CreateWatcher` anropa metoden för `AnchorLocateCriteria`sessionen genom att tillhandahålla din .