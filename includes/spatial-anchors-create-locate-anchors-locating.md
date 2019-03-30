---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632494"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Hitta en spatial ankare för molnet

Att kunna hitta en tidigare överförda molnet spatial ankare är en av särskilda skäl till att använda Azure Spatial ankare-biblioteket. För att hitta spatial molnankare måste veta deras identifierare. Ankar-ID: N kan lagras i programmets backend-tjänst och tillgänglig för alla enheter som korrekt kan autentisera till den. Ett exempel på detta finns i [självstudien: Dela Spatial ankare över enheter](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Skapa en instans av en `AnchorLocateCriteria` objekt genom att ange de identifierare som du letar efter och anropa den `CreateWatcher` metod för sessionen genom att tillhandahålla ditt `AnchorLocateCriteria`.
