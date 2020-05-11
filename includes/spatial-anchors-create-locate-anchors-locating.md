---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006502"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Hitta en moln rums ankare

Att kunna hitta en tidigare sparad moln rums fäst punkt är en av de viktigaste orsakerna till att använda Azures ankare för avstånd. Det finns flera olika sätt att hitta ett moln rums ankare. Du kan använda en strategi på en övervakare i taget.
- Hitta ankare per identifierare.
- Placera fäst punkter som är kopplade till ett tidigare placerat ankare. Du kan lära dig om ankar relationer [här](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Hitta ankare med hjälp av [grov omlokalisering](/azure/spatial-anchors/concepts/coarse-reloc/).

> [!NOTE]
> Varje gången du hittar ett ankare försöker Azure spatial-ankare använda de miljö data som samlas in för att utöka den visuella informationen på ankaret. Om du har problem med att hitta en fäst punkt kan det vara praktiskt att skapa ett ankare och sedan hitta det flera gånger från olika vinklar och ljus förhållanden.

Om du letar efter ID för moln rums ankare per identifierare, vill du lagra molnets plats för moln rums ankare i programmets backend-tjänst och göra den tillgänglig för alla enheter som kan autentiseras på rätt sätt. Ett exempel på detta finns i [Självstudier: dela spatiala ankare mellan enheter](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Instansiera ett `AnchorLocateCriteria` objekt, ange de identifierare som du letar efter och anropa `CreateWatcher` metoden i sessionen genom att tillhandahålla din. `AnchorLocateCriteria`