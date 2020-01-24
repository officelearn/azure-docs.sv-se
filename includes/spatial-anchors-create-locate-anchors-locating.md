---
ms.openlocfilehash: ac9fe1d367dba0ebdf4250b3213f191ced758dd3
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694256"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Hitta en moln rums ankare

Att kunna hitta en tidigare sparad moln rums fäst punkt är en av de viktigaste orsakerna till att använda Azures ankare för avstånd. Det finns flera olika sätt att hitta ett moln rums ankare. Du kan använda en strategi på en övervakare i taget.
- Hitta ankare per identifierare.
- Placera fäst punkter som är kopplade till ett tidigare placerat ankare. Du kan lära dig om ankar relationer [här](/azure/spatial-anchors/concepts/anchor-relationships-way-finding.md).
- Hitta ankare med hjälp av [grov omlokalisering](/azure/spatial-anchors/concepts/coarse-reloc.md).

Om du letar efter ID för moln rums ankare per identifierare, vill du lagra molnets plats för moln rums ankare i programmets backend-tjänst och göra den tillgänglig för alla enheter som kan autentiseras på rätt sätt. Ett exempel på detta finns i [Självstudier: dela spatiala ankare mellan enheter](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instansiera ett `AnchorLocateCriteria`-objekt, ange de identifierare som du letar efter och anropa metoden `CreateWatcher` på sessionen genom att tillhandahålla din `AnchorLocateCriteria`.