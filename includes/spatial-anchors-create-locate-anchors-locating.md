---
ms.openlocfilehash: c2b1e1d87600e83733eb8bdbf5bc6e2e63376cf4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993117"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Hitta en moln rums ankare

Att kunna hitta en tidigare sparad moln rums fäst punkt är en av de viktigaste orsakerna till att använda Azures ankare för avstånd. Det finns flera olika sätt att hitta ett moln rums ankare. Du kan använda en strategi på en övervakare i taget.
- Hitta ankare per identifierare.
- Placera fäst punkter som är kopplade till ett tidigare placerat ankare. Du kan lära dig om ankar relationer [här](../articles/spatial-anchors/concepts/anchor-relationships-way-finding.md).
- Hitta ankare med hjälp av [grov omlokalisering](../articles/spatial-anchors/concepts/coarse-reloc.md).

> [!NOTE]
> Varje gången du hittar ett ankare försöker Azure spatial-ankare använda de miljö data som samlas in för att utöka den visuella informationen på ankaret. Om du har problem med att hitta en fäst punkt kan det vara praktiskt att skapa ett ankare och sedan hitta det flera gånger från olika vinklar och ljus förhållanden.

Om du letar efter ID för moln rums ankare per identifierare, vill du lagra molnets plats för moln rums ankare i programmets backend-tjänst och göra den tillgänglig för alla enheter som kan autentiseras på rätt sätt. Ett exempel på detta finns i [Självstudier: dela spatiala ankare mellan enheter](../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md).

Instansiera ett `AnchorLocateCriteria` objekt, ange de identifierare som du letar efter och anropa `CreateWatcher` metoden i sessionen genom att tillhandahålla din `AnchorLocateCriteria` .