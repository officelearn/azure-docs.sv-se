---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907812"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Hitta en spatial ankare för molnet

För att hitta spatial molnankare måste veta deras identifierare. Ankar-ID: N kan vara lagras i programmets serverdelstjänst och tillgänglig för alla enheter som korrekt kan autentisera till den. Ett exempel på detta finns i [självstudien: Dela Spatial ankare över enheter](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Skapa en instans av ett AnchorLocateCriteria objekt genom att ange de identifierare som du letar efter och anropa metoden CreateWatcher för sessionen genom att tillhandahålla ditt AnchorLocateCriteria.
