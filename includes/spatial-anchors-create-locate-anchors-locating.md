---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110781"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Hitta en spatial ankare för molnet

Att kunna hitta en tidigare överförda molnet spatial ankare är en av särskilda skäl till att använda Azure Spatial ankare-biblioteket. För att hitta spatial molnankare måste veta deras identifierare. Ankar-ID: N kan lagras i programmets backend-tjänst och tillgänglig för alla enheter som korrekt kan autentisera till den. Ett exempel på detta finns i [självstudien: Dela Spatial ankare över enheter](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Skapa en instans av en `AnchorLocateCriteria` objekt genom att ange de identifierare som du letar efter och anropa den `CreateWatcher` metod för sessionen genom att tillhandahålla ditt `AnchorLocateCriteria`.
